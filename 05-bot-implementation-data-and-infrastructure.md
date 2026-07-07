# News-Trading Bot: Practical Implementation — Data, Infrastructure, and Process

*Research compiled June 2026. Focus: how professionals and serious independents actually build automated news/fundamental-driven trading systems. No technical analysis — purely infrastructure, data sourcing, architecture, backtesting methodology, risk, and regulation.*

---

## 1. Overview

An automated news-trading bot has five irreducible components: (1) a news/data ingestion layer, (2) a filtering and entity-mapping layer that turns raw text into "this headline is about ticker X and it is new information," (3) a signal layer (rules, ML, or LLM) that decides direction and conviction, (4) a risk layer that decides size and whether to trade at all, and (5) an execution layer talking to a broker API.

The single most important strategic fact: **you will not win the speed race.** HFT firms parse machine-readable news in microseconds from co-located servers. The realistic edge for a retail-speed bot lives in the **minutes-to-weeks** horizon — under-reaction and drift effects (e.g., post-earnings announcement drift), small-cap filings that big desks ignore, and synthesis/interpretation tasks where an LLM reading a 200-page 8-K exhibit faster than a human analyst is genuinely valuable. Design every component around that honest assessment, not around shaving milliseconds.

The second most important fact: **backtests of news strategies are almost always overstated** — by look-ahead bias (especially with LLMs that have memorized history), survivorship bias, and fantasy fills during exactly the moments when spreads blow out. Paper trade for months before risking money.

---

## 2. News Data Sources Ranked by Tier, Latency, and Cost

### 2.1 Professional tier (institutional money required)

| Source | What it is | Latency | Approx. cost (2025–2026) |
|---|---|---|---|
| **Bloomberg Event-Driven Feeds (EDF)** | Highly structured, machine-readable real-time event data; "tickerised" news feeds designed to plug directly into automated market-making and event-driven systems. Delivered alongside B-PIPE | Milliseconds; built for algo consumption | B-PIPE ~$2,000–3,000/mo; enterprise data feeds custom, typically **$100,000+/year**; machine-readable news is a premium add-on ([Bloomberg EDF](https://www.bloomberg.com/professional/products/data/enterprise-catalog/event-driven-feeds/), [godeldiscount cost breakdown](https://godeldiscount.com/blog/bloomberg-terminal-cost-2026)) |
| **Dow Jones Newswires (machine-readable)** | The canonical low-latency newswire (WSJ/Barron's/MarketWatch ecosystem); "elementized" news for machines | Milliseconds | Custom institutional contracts, typically five to six figures/year; also resold through analytics vendors like RavenPack |
| **RavenPack** | News *analytics* layered on Dow Jones, WSJ, MT Newswires, Benzinga and more — entity detection, sentiment, novelty, relevance scores on ~1,200 entity types. The de facto standard for academic + quant event studies | ~**300 ms** from DJ newswire release to RavenPack metrics ([Fed research paper](https://www.federalreserve.gov/econres/ifdp/files/ifdp1233.pdf)); 99.98% uptime via dual datacenters ([RavenPack](https://www.ravenpack.com/products/edge/data/news-analytics)) | Institutional pricing (typically tens of thousands of $/yr); historical archives available to academics via WRDS |
| **Benzinga APIs (Pro/enterprise)** | Real-time headlines optimized for speed-traders: 600–900 real-time headlines/day, 130–160 full articles/day; earnings, analyst ratings, M&A as structured feeds. Delivered via REST (pull), TCP (push), FTP, websocket. Often beats Bloomberg/CNBC to retail-relevant stories by minutes ([Benzinga API](https://www.benzinga.com/apis/cloud-product/stock-news-api/), [LiberatedStockTrader benchmark](https://www.liberatedstocktrader.com/benzinga-pro-review-real-time-news/)) | Sub-second on push delivery; no intentional delay | Benzinga Pro UI: Basic ~**$37/mo**, Essential ~**$166/mo** (squawk, sentiment, calendars) ([pricing](https://www.benzinga.com/pro/pricing)). Direct API contracts are custom (typically $1k+/mo) — but see Alpaca below for a cheap back door |

**Key practical note:** Benzinga is the one "professional-grade" feed an independent can realistically afford, and it's resold through Alpaca and Polygon (below) at hobbyist prices.

### 2.2 Mid-tier APIs (the realistic sweet spot for an independent)

| API | News product | Delivery | Latency character | Approx. price |
|---|---|---|---|---|
| **Alpaca News API** | Benzinga content: up to 250 full articles + 900 real-time headlines/day, stocks + crypto | **WebSocket stream** + REST historical | Real-time push (Benzinga-sourced) | Bundled with Alpaca market-data plans; free tier exists on paper/live accounts ([Alpaca docs](https://docs.alpaca.markets/us/docs/streaming-real-time-news), [Alpaca–Benzinga partnership](https://alpaca.markets/blog/alpaca-partners-with-benzinga-to-deliver-real-time-embedded-financial-news/)) |
| **Polygon.io (rebranded "Massive")** | News endpoint with Benzinga editorial feeds (earnings, upgrades/downgrades, M&A) + sentiment via insights field | REST + **WebSocket, ~25 ms median latency** on streams | Near-real-time | Free: 5 calls/min; paid plans from ~**$199/mo** for serious usage ([comparison](https://www.ksred.com/the-complete-guide-to-financial-data-apis-building-your-own-stock-market-data-pipeline-in-2025/)) |
| **Finnhub** | Company news + sentiment scores, market news, websocket delivery | REST + WebSocket | Near-real-time | Free: **60 calls/min** (most generous free tier); premium from ~**$49/mo** ([Finnhub comparison](https://finnhub.io/finnhub-stock-api-vs-alternatives)) |
| **Alpha Vantage News & Sentiment** | Broad-coverage article + ticker-level sentiment (direction and magnitude) | REST only (poll) | Near-real-time but polling-bound | Free: 25 calls/**day** (nearly useless); paid from ~**$49.99/mo** |
| **Tiingo News API** | Curated financial news feed, flat-rate pricing, good for backtest archives | REST | Minutes-class | Free starter tier; flat-rate individual plans (~$10–50/mo class); commercial tiers higher ([Tiingo](https://www.tiingo.com/products/news-api)) |
| **Marketaux** | Entity-tagged global news: 200,000+ entities across 80+ markets | REST (JSON) | Minutes-class | Free tier + paid plans (low tens of $/mo) ([docs](https://www.marketaux.com/documentation)) |
| **EODHD** | Financial news bundled in All-In-One; strongest for *historical* news archives and backtesting | REST | Minutes-class | All-In-One **$99.99/mo** ($83/mo annual); cheaper tiers ($19.99–59.99) exclude news ([pricing](https://eodhd.com/pricing)) |
| **NewsAPI.org** | General (non-finance-specific) news aggregator | REST | **Free tier is 24-hour delayed** — useless for trading; real-time requires paid | Free: 100 req/day, dev/test only, no production use; Business **$449/mo**; Advanced $1,749/mo ([pricing](https://newsapi.org/pricing)) |
| **sec-api.io / Quantillium** | Third-party SEC EDGAR wrappers: real-time filing streams, full-text search over 20M+ filings, XBRL-to-JSON | WebSocket/stream + REST | Sub-second to seconds from EDGAR acceptance | Roughly $50–100+/mo class ([sec-api.io](https://sec-api.io/), [Quantillium](https://www.quantillium.com/products/sec-filings-api)) |

**Practical ranking for a solo builder:** Alpaca News websocket (free, Benzinga content) + SEC EDGAR (free, see below) as the backbone; Finnhub free tier for breadth; EODHD or Tiingo for historical news archives to backtest against; upgrade to Polygon/direct Benzinga only when live results justify it.

### 2.3 Free sources (slower, but where real fundamental alpha hides)

- **SEC EDGAR.** The single best free source. New filings (8-K, 10-Q/K, S-1, 13D/G, Form 4) are published with **sub-second latency from acceptance**; official free endpoints include the full-text search API (`efts.sec.gov`), the submissions JSON API, and RSS/Atom feeds of latest filings. Rate limit: 10 requests/sec with a declared User-Agent. Material 8-Ks (M&A, CEO exits, guidance withdrawals, FDA news in exhibits) routinely move stocks, and small/mid-cap filings get slow human coverage ([sec-api GitHub overview](https://github.com/janlukasschroeder/sec-api), [Apify EDGAR monitor](https://apify.com/wiry_kingdom/sec-edgar-filing-monitor)).
- **Macro release pages.** [BLS release schedule](https://www.bls.gov/schedule/) (CPI, NFP — typically 8:30 a.m. ET), [BEA schedule](https://www.bea.gov/news/schedule) (GDP, PCE), [FOMC calendar](https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm) (statement at **2:00 p.m. ET**, presser 2:30 p.m.), plus the [FRED economic release calendar](https://fred.stlouisfed.org/releases/calendar) as an aggregator. These are scheduled, embargoed releases — your bot knows the exact second, so the game is parsing speed and pre-positioning rules, not discovery. Note: media lock-ups historically let wire services sell machine-formatted feeds at the release instant; DOL curtailed devices in lock-ups in 2020 precisely because of this ([BLS lockup changes](https://www.bls.gov/bls/changes-to-dol-media-lockup-effective-march-1-2020.htm)); research has even found informed trading *ahead of* embargoed releases ([CFA digest](https://rpc.cfainstitute.org/research/cfa-digest/2017/02/can-information-be-locked-up-informed-trading-ahead-of-macro-news-announcements-digest-summary)).
- **RSS feeds.** PR Newswire/GlobeNewswire/Business Wire category feeds, company IR-page RSS, Google News RSS queries, Reuters/AP sitemaps. Latency: seconds-to-minutes depending on poll interval; free but increasingly throttled. Broad scraping of news sites is effectively dead in 2026 due to anti-bot defenses ([APITube analysis](https://apitube.io/blog/post/best-financial-news-api-trading)) — prefer official feeds and APIs.
- **Company IR pages.** Earnings press releases sometimes hit the IR page or the wire seconds before aggregators. Polling a watchlist of IR pages around known earnings dates is a legitimate free-tier tactic (respect robots.txt and ToS).

---

## 3. The Latency Hierarchy — Where You Can and Cannot Compete

**Why HFT wins the first milliseconds.** HFT firms co-locate servers inside exchange datacenters meters from the matching engine, consume direct exchange feeds (not consolidated SIP), and subscribe to elementized machine-readable news (Bloomberg EDF, DJ elementized, RavenPack at ~300 ms enrichment). They parse a headline and have orders resting at the exchange in **microseconds to low milliseconds**. The infrastructure costs millions per year; a home setup on residential internet is 4–6 orders of magnitude slower and cannot compete on scheduled-release reaction trades ([Quantt HFT guide](https://www.quantt.co.uk/resources/high-frequency-trading-guide), [InvestmentNews](https://www.investmentnews.com/transformation/hft-trading-software/263152), [ICC](https://www.icc-usa.com/blog/zero-latency-in-high-frequency-trading-solutions)).

**The honest time-horizon map for a retail-speed bot:**

| Horizon after news | Who wins | Retail bot viability |
|---|---|---|
| 0–100 ms | Co-located HFT with machine-readable feeds | **None.** Don't even design for it |
| 100 ms – 5 s | Fast prop firms, low-latency quants | Effectively none for liquid large caps |
| 5 s – 5 min | Mixed; price often overshoots/undershoots; halts trigger | Marginal. Possible on small caps and obscure filings where no algo desk is watching, but execution risk (halts, spreads) is severe |
| 5 min – hours | Slow interpretation: "what does this 8-K *mean*?" | **Viable.** LLM-assisted reading of filings/transcripts faster and more thoroughly than human analysts |
| Days – weeks | Drift/under-reaction anomalies | **Most viable.** PEAD: abnormal returns drift for weeks-to-months after earnings surprises; classic strategies show ~7.5% (single sort) to ~12.5%/yr (earnings + price momentum combined) abnormal returns in academic studies, with the drift accelerating between trading days 20–75 ([Quantpedia PEAD](https://quantpedia.com/strategies/post-earnings-announcement-effect), [Wikipedia PEAD](https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift)) — Fama called PEAD "the granddaddy of underreaction events" |

The practitioner consensus: HFT competition is irrelevant to anyone operating on 15-minute-or-slower decision cycles ([Quantt](https://www.quantt.co.uk/resources/high-frequency-trading-guide)). Your edge must be **interpretation and coverage breadth**, not reaction speed. Caveat: academic anomaly returns (PEAD's 7–12%) shrink substantially after costs and post-publication decay — treat them as upper bounds.

---

## 4. Architecture Patterns for a News Bot

A production-shaped pipeline (drawn from practitioner write-ups and the academic LLM-trading literature — [end-to-end LLM trading system](https://arxiv.org/pdf/2502.01574), [structured insights from financial news](https://arxiv.org/pdf/2407.15788), [LLM trading agent survey](https://arxiv.org/pdf/2408.06361)):

```
[Ingestion] → [Dedup/Novelty] → [Entity/Ticker mapping] → [Signal] → [Risk gate] → [Execution] → [Logging/Monitoring]
```

### 4.1 Ingestion: polling vs webhooks vs websockets
- **WebSockets** (Alpaca news stream, Polygon, Finnhub): push delivery, lowest latency for an independent, one persistent connection. Preferred wherever offered. Handle reconnect-with-backoff and sequence gaps.
- **Webhooks**: provider POSTs to your endpoint (some SEC-API vendors, sec-api.io streams). Requires a public HTTPS endpoint; great when available.
- **Polling**: required for REST-only sources (Alpha Vantage, EDGAR full-text search, RSS, IR pages). Use conditional GETs (ETag/If-Modified-Since), staggered schedules, and respect rate limits (EDGAR: ≤10 req/s with User-Agent). Polling interval defines your floor latency.
- Normalize everything into one internal event schema: `{source, received_at, published_at, headline, body, url, raw_tickers, hash}`. Persist raw events append-only (SQLite/Postgres) — you'll need them for point-in-time backtests later.

### 4.2 Deduplication and novelty filtering
The same story arrives 5–50 times (wire → aggregators → rewrites). Without dedup, your bot trades the same news repeatedly.
- Layer 1: exact-hash on normalized headline/URL.
- Layer 2: fuzzy/embedding similarity (MinHash or sentence embeddings + cosine threshold) within a rolling window per ticker.
- Layer 3: **novelty vs the story chain** — is this new information or a follow-up? RavenPack sells exactly this as a "novelty score"; you approximate it by comparing against the last N stories for the ticker. Only the first genuinely novel item should generate a signal.

### 4.3 Entity / ticker mapping
Harder than it looks: "Apple" the company vs apple the fruit; "Meta" in crypto contexts; subsidiaries, tickers reused after delistings, ADRs.
- Maintain a point-in-time security master (ticker ↔ CIK ↔ company name ↔ aliases). EDGAR's `company_tickers.json` is a free starting point.
- Prefer feeds that ship ticker tags (Benzinga/Alpaca, Polygon, Marketaux, Alpha Vantage) and *validate* them — vendor tagging has errors, and careful validation of ticker mappings is repeatedly flagged as a reliability bottleneck in LLM-pipeline studies ([FlowHunt comparison](https://www.flowhunt.io/blog/llm-trading-bots-comparison/)).
- For untagged text (EDGAR exhibits, RSS), use NER + your alias table; have the LLM output the ticker AND a confidence, and drop low-confidence matches.

### 4.4 Signal generation: rules vs ML vs LLM
- **Rules first.** "8-K item 1.01 with 'merger agreement' + small cap + premium implied" or "guidance withdrawn" are high-precision, auditable, and cheap. Most professional event desks are rules-heavy.
- **Classical ML/sentiment**: works on large labeled archives (vendor sentiment scores, FinBERT). Adds breadth, modest depth.
- **LLM**: best used as a *structured extractor and reasoner*, not an oracle: output strict JSON `{ticker, event_type, direction, magnitude, confidence, half_life, summary}`; chain-of-thought prompting measurably improves sentiment extraction quality ([CometAPI](https://www.cometapi.com/how-to-use-llms-for-crypto-research-and-trading-decisions/), [arXiv 2407.15788](https://arxiv.org/pdf/2407.15788)). Hybrid designs (LLM features feeding an RL/ranking layer) appear throughout the recent literature ([arXiv 2510.19173](https://arxiv.org/pdf/2510.19173)). Pin model versions, log every prompt/response, and beware cost-per-event at scale.
- Always gate signals on **materiality + novelty + liquidity** before they reach risk.

### 4.5 Risk gate (pre-trade checks, run synchronously before every order)
Max position size per ticker, max gross/net exposure, max daily loss, per-event-type size limits, "is the stock halted/haltable," earnings-date awareness, duplicate-order suppression, and a global kill switch (Section 6). This mirrors what SEC Rule 15c3-5 forces brokers to do; replicate it client-side.

### 4.6 Broker execution APIs

| Broker | API style | Strengths | Weaknesses | Paper trading |
|---|---|---|---|---|
| **Alpaca** | REST + WebSocket, OAuth keys; first-class Python (`alpaca-py`, async, typed) | Easiest start; commission-free US equities/crypto; news data bundled; no account minimum | US equities/crypto only; fewer order types; PFOF-model execution quality | **Unlimited free paper trading**, identical API — flip a base URL ([brokerchooser](https://brokerchooser.com/best-brokers/best-brokers-for-algo-trading-in-the-united-states)) |
| **Interactive Brokers** | TWS API / Client Portal API; `ib_insync` (now `ib_async`) cuts boilerplate ~70% | Most complete: 150+ order types, 150 markets, 34 countries; ~sub-50 ms execution latency; best margin rates | Steepest learning curve; gateway process to babysit; API is stateful and quirky | Full paper account (separate login) |
| **Tradier** | REST + streaming | Cheap options flow; multi-leg options up to 4 legs; simple REST | Smaller ecosystem; equities focus US-only | Sandbox environment |

Consensus for 2025–2026: **Alpaca to start** (best paper-to-live path for Python developers), **IBKR when you need options breadth, non-US markets, or better execution** ([Medium platform comparison](https://medium.com/@pta.forwork/comparing-algorithmic-trading-platforms-metatrader-interactive-brokers-ibkr-alpaca-3fc0fab11288), [TradeAlgo](https://www.tradealgo.com/trading-guides/tools/best-broker-apis-for-algorithmic-trading-in-2026)).

**Paper trade first — for a long time.** Paper fills are optimistic (no queue position, no impact), so treat paper results as an upper bound; but paper trading is the only honest way to validate your slippage assumptions and your pipeline's end-to-end latency ([LuxAlgo backtesting limitations](https://www.luxalgo.com/blog/backtesting-limitations-slippage-and-liquidity-explained/)). Run the full live pipeline against paper for 1–3 months minimum and compare realized paper entries to your backtest's assumed entries.

---

## 5. Backtesting Event-Driven Strategies Correctly

### 5.1 Event-study methodology
The academically correct frame: define the event (filing timestamp, headline timestamp), align all events at t=0, measure **abnormal returns** (stock return minus a market/factor benchmark) over windows like [t0, t0+5min], [t0, t0+1d], [t0, t0+60d]. Use the *received_at* timestamp your system would actually have had — not the publication timestamp, and never the date alone. The PEAD literature is the methodological template ([ScienceDirect PEAD review](https://www.sciencedirect.com/science/article/pii/S2214635020303750)).

### 5.2 Point-in-time data and survivorship bias
- Use **point-in-time** datasets: original (non-restated) fundamentals, the index membership as of that date, and **delisted/bankrupt/acquired tickers included**. Survivorship bias alone inflates returns ~1–4%/yr, compounding ([LuxAlgo survivorship](https://www.luxalgo.com/blog/survivorship-bias-in-backtesting-explained/), [sharpely PIT explainer](https://sharpely.in/blog/bias-free-backtesting-explained:-how-sharpely-uses-point-in-time-data-to-avoid-look-ahead-and-survivorship-bias)).
- News-specific trap: vendors backfill and re-tag historical news. An archive downloaded today is not what subscribers saw live. Start archiving your own live feed **now**; that becomes your only truly point-in-time news dataset.
- Prefer event-driven backtest engines (process one event at a time, maintain state) over vectorized ones for news strategies — they structurally prevent most look-ahead ([IBKR Quant](https://www.interactivebrokers.com/campus/ibkr-quant-news/a-practical-breakdown-of-vector-based-vs-event-based-backtesting/)).

### 5.3 LLM look-ahead bias — the new and severe failure mode
If you backtest by feeding 2021 headlines to a 2025-trained LLM, the model has *memorized the outcome*. Documented findings:
- Off-the-shelf models (Llama 3.1, DeepSeek) produced 44%+ "returns" trading 2021 stocks from news — almost certainly memorization, not skill ([FinanceAlliance](https://www.financealliance.io/the-hidden-danger-of-look-ahead-bias-in-financial-llms/)).
- Contamination flows through **two channels**: parametric memory in weights, and RAG context that smuggles future info ([arXiv 2602.14233](https://arxiv.org/html/2602.14233v1)).
- **Prompt engineering and ticker-masking do NOT fix it** — the contamination is structural ([arXiv 2512.23847](https://arxiv.org/html/2512.23847v1)). With GPT-4.1, eliminating the spurious Sharpe required excluding up to 22% of observations in context-rich settings.
- Practical mitigations: backtest only on data **after the model's training cutoff**; or use forward (out-of-sample live/paper) evaluation as the real test; smaller models and finer-grained data show less bias; advanced methods adjust logits at inference to suppress memorized knowledge ([arXiv 2512.06607](https://arxiv.org/html/2512.06607v1), [MemGuard-Alpha](https://arxiv.org/pdf/2603.26797)).

### 5.4 Realistic slippage around news — why news backtests overstate
Around releases, liquidity evaporates, spreads widen drastically, and quotes gap — your backtest's "fill at last price" is fiction at exactly the moments your strategy trades ([LuxAlgo](https://www.luxalgo.com/blog/backtesting-limitations-slippage-and-liquidity-explained/), [Benzinga on CPI/FOMC handling](https://www.benzinga.com/Opinion/26/06/53097073/trading-systems-and-market-news-how-to-handle-cpi-fomc-and-unexpected-events)). Generic slippage modeling trims 0.5–3%/yr from simulated returns; news-moment slippage is far worse than average. Rules of thumb:
- Model entry at the **quoted spread's far side plus a volatility-scaled penalty**, not at mid or last.
- Add a realistic **decision-to-exchange latency** (your full pipeline: feed latency + LLM inference + risk checks + broker round trip — often 2–30 seconds for an LLM bot).
- Assume you *cannot* fill during halts; simulate LULD halts (Section 6) and re-opening gaps.
- Then validate every assumption against paper-trading fills.

### 5.5 Other classic sins
Look-ahead via restated fundamentals or final (revised) economic data instead of first prints (use [ALFRED vintage data](https://alfred.stlouisfed.org/) for macro); overfitting to a handful of dramatic events (news datasets are small-N — use multiple-hypothesis corrections); ignoring borrow availability/cost on the short side, where much news alpha nominally sits ([AnalystPrep backtesting problems](https://analystprep.com/study-notes/cfa-level-2/problems-in-backtesting/), [For Traders bias guide](https://www.fortraders.com/blog/how-to-avoid-bias-in-backtesting)).

---

## 6. Risk Management for News Trading

- **Position sizing for binary events.** Treat earnings/FDA/court rulings as binary: size so the *worst plausible gap* (not the average move) is survivable — e.g., risk ≤0.5–1% of equity assuming a 20–40% adverse gap on a small cap. Kelly-style sizing must use gap distributions, not daily vol.
- **LULD halts.** US stocks halt when prices breach bands of 5%/10%/20% (or min($0.15, 75%)) around a rolling 5-minute reference price; a 15-second limit state triggers a 5-minute halt and reopening auction; bands double in the last 25 minutes ([Nasdaq LULD FAQ](https://www.nasdaqtrader.com/content/MarketRegulation/LULD_FAQ.pdf), [Databento microstructure guide](https://databento.com/microstructure/luld)). News pops of 15%+ routinely chain two or three halts. Consequences for your bot: **use limit orders, never market orders, on volatile names**; size assuming your next fill is at the band edge; a stock can reopen *anywhere* ([TradingSim halts explainer](https://www.tradingsim.com/blog/stock-halts-and-circuit-breaker-halts-explained)). Market-wide circuit breakers (7%/13%/20% on S&P 500) also exist ([Investor.gov](https://www.investor.gov/introduction-investing/investing-basics/glossary/stock-market-circuit-breakers)).
- **Gaps and overnight risk.** Most earnings drop outside regular hours. Holding through a release means stops do not protect you — the open gaps straight through them. Either trade the post-news drift (enter *after* the event) or explicitly budget overnight gap risk. Extended-hours liquidity is thin; spreads of 1–5% are normal.
- **Volatility crush (options).** If you express news views via options, long premium *into* a known event loses to IV crush even when you call direction right — implied vol collapses at the announcement. Either trade post-event, use spreads that are vega-neutral-ish, or stick to shares.
- **Drawdown control.** Hard daily-loss limit (e.g., −2% equity → flatten and stop), weekly/monthly limits, and consecutive-loss throttles that cut size after losing streaks. News strategies have fat-tailed P&L; per-trade discipline isn't enough.
- **Kill switches.** Regulators expect *fully automated* halts, not humans watching dashboards — that's the standard the SEC set for broker-dealers under Rule 15c3-5, and it's the right model for your bot too ([SEC 15c3-5 FAQ](https://www.sec.gov/rules-regulations/staff-guidance/trading-markets-frequently-asked-questions/divisionsmarketregfaq-0), [Hedgeweek on kill switches](https://www.hedgeweek.com/dont-be-killed-your-brokers-market-access-kill-switches/)). Implement: (1) automatic flatten-and-halt on daily-loss breach, order-rate anomaly (e.g., >N orders/min), position-limit breach, or stale-data detection; (2) a one-command manual kill that cancels all open orders and closes positions; (3) a watchdog that kills trading if the bot's heartbeat stops. Test the kill switch in paper regularly. Note your broker also runs 15c3-5 controls and can reject or freeze you — don't be surprised by broker-side throttles.

---

## 7. Regulatory and Practical Notes

### 7.1 Pattern Day Trader rule — major 2026 change
- Historically: 4+ day trades in 5 business days in a margin account flagged you a PDT, requiring **$25,000 minimum equity** ([Wikipedia PDT](https://en.wikipedia.org/wiki/Pattern_day_trader)).
- **As of June 4, 2026, the $25,000 PDT minimum is abolished.** FINRA replaced day-trading margin provisions with intraday margin standards (Regulatory Notice 26-10, SEC-approved); the day-trade count and PDT designation are gone. Brokers may phase in until **October 20, 2027** — so individual brokers may still enforce legacy rules for a while ([FINRA 26-10](https://www.finra.org/rules-guidance/notices/26-10), [NerdWallet](https://www.nerdwallet.com/investing/news/pattern-day-trading-rule-change), [Schwab](https://www.schwab.com/learn/story/sec-approves-scrapping-25000-day-trader-minimum)).
- Remaining constraints: $2,000 minimum for *any* margin/leveraged trading; cash accounts can day trade but must avoid **free-riding** (selling shares bought with unsettled funds → 90-day restriction). PDT never applied to futures, forex, or crypto.

### 7.2 Market manipulation — the fake-news problem cuts both ways
- **Trading on fake news you created or spread** is securities fraud, full stop. The SEC actively prosecutes social-media pump schemes: $100M Discord/Twitter influencer case (2022), the Gallagher Twitter manipulation verdict (2025), and FY2025 saw 456 enforcement actions and $17.9B in monetary relief with manipulation a stated priority ([SEC 2022-221](https://www.sec.gov/newsroom/press-releases/2022-221), [SEC FY2025 results](https://www.sec.gov/newsroom/press-releases/2026-34), [Gibson Dunn year-end update](https://www.gibsondunn.com/securities-enforcement-2025-year-end-update/)).
- **Trading on fake news someone else created** is a *risk* to your bot: fake press releases, spoofed wires, and hoax tweets have moved stocks. Defenses: only act on primary/verified sources (EDGAR, official wires via reputable vendors), cross-confirm across ≥2 independent sources for large positions, and down-weight social media entirely.
- Your bot's own behavior matters: rapid-fire order patterns can resemble spoofing/layering even if unintentional. Keep order-to-fill ratios sane; log everything.

### 7.3 API terms of service and scraping
- **NewsAPI free tier prohibits production use** (dev/test only, 24-h delay); commercial use starts at $449/mo ([pricing](https://newsapi.org/pricing)). Most "free" tiers (Alpha Vantage, Finnhub, Marketaux) are personal-use licensed — automated trading is usually fine for personal accounts, but redistribution of the data is not. Read the license before building on it.
- **Vendor news content is licensed, not owned**: Benzinga content via Alpaca/Polygon is for your consumption within those platforms' terms; storing it for internal backtests is generally tolerated, republishing is not.
- **EDGAR is public domain**: free to use for any purpose, just honor the 10 req/s + User-Agent fair-access policy or you'll be IP-banned.
- **General web scraping** of news sites is both legally gray (ToS violations, though hiQ-era case law is mixed) and practically dying — aggressive anti-bot defenses have made large-scale scraping unreliable in 2026 ([APITube](https://apitube.io/blog/post/best-financial-news-api-trading)). Use official feeds.
- **Broker API terms**: Alpaca/IBKR/Tradier all permit automated trading on your own account; market-data redistribution is forbidden; IBKR requires market-data subscriptions per exchange.

---

## 8. Recommended Build Path (Synthesis)

1. **Weeks 1–2 — Ingestion + archive.** Alpaca paper account + News websocket (free Benzinga content); EDGAR poller for your watchlist's 8-Ks; FRED/BLS/BEA calendars. Persist every raw event with received-at timestamps. This archive is your future point-in-time gold.
2. **Weeks 3–4 — Dedup, ticker mapping, rules signals.** Hash + embedding dedup; security master; 5–10 high-precision rules (guidance withdrawal, merger 8-K, FDA approval keywords). No ML yet.
3. **Month 2 — LLM layer + risk gate.** Structured-JSON extraction with confidence scores; full pre-trade risk checks; kill switch; everything still paper.
4. **Months 2–4 — Honest evaluation.** Event-study analysis of your own archived events (post-cutoff data only if using an LLM); compare paper fills to assumed fills; recalibrate slippage.
5. **Month 4+ — Tiny live capital** on the highest-precision rules only, drift-horizon holding periods (days, not seconds), strict daily-loss kill switch. Scale only what survives live.

Budget reality: a credible starter stack costs **$0–100/month** (Alpaca free + EDGAR free + Finnhub free + EODHD All-In-One for history + LLM API costs). The professional stack (Bloomberg EDF/RavenPack/DJ machine-readable) is $100k+/year and buys speed you cannot use anyway.

---

## Sources

**News data and pricing**
- RavenPack News Analytics — https://www.ravenpack.com/products/edge/data/news-analytics
- Fed IFDP paper, "First to 'Read' the News" (RavenPack ~300 ms latency) — https://www.federalreserve.gov/econres/ifdp/files/ifdp1233.pdf
- Bloomberg Event-Driven Feeds — https://www.bloomberg.com/professional/products/data/enterprise-catalog/event-driven-feeds/
- Bloomberg machine-readable news feeds (Hedgeweek) — https://www.hedgeweek.com/bloomberg-unveils-customisable-real-time-news-feeds-to-enhance-systematic-trading-workflows/
- Bloomberg Terminal/B-PIPE cost breakdown — https://godeldiscount.com/blog/bloomberg-terminal-cost-2026
- Benzinga Pro pricing — https://www.benzinga.com/pro/pricing
- Benzinga Stock News API — https://www.benzinga.com/apis/cloud-product/stock-news-api/
- Benzinga Pro benchmark review — https://www.liberatedstocktrader.com/benzinga-pro-review-real-time-news/
- Financial Data APIs compared (Polygon/Alpha Vantage/etc.) — https://www.ksred.com/the-complete-guide-to-financial-data-apis-building-your-own-stock-market-data-pipeline-in-2025/
- Finnhub vs alternatives — https://finnhub.io/finnhub-stock-api-vs-alternatives
- Best financial news APIs for trading — https://apitube.io/blog/post/best-financial-news-api-trading
- NewsAPI pricing — https://newsapi.org/pricing
- Tiingo News API — https://www.tiingo.com/products/news-api ; pricing — https://www.tiingo.com/about/pricing
- EODHD pricing — https://eodhd.com/pricing
- Marketaux documentation — https://www.marketaux.com/documentation
- Alpaca real-time news docs — https://docs.alpaca.markets/us/docs/streaming-real-time-news
- Alpaca–Benzinga partnership — https://alpaca.markets/blog/alpaca-partners-with-benzinga-to-deliver-real-time-embedded-financial-news/
- sec-api SDK / EDGAR streaming — https://github.com/janlukasschroeder/sec-api ; https://sec-api.io/
- Quantillium SEC Filings API — https://www.quantillium.com/products/sec-filings-api
- Apify SEC EDGAR monitor — https://apify.com/wiry_kingdom/sec-edgar-filing-monitor

**Macro release infrastructure**
- BLS release schedule — https://www.bls.gov/schedule/
- BEA release schedule — https://www.bea.gov/news/schedule
- FRED economic release calendar — https://fred.stlouisfed.org/releases/calendar
- FOMC meeting calendar — https://www.federalreserve.gov/monetarypolicy/fomccalendars.htm
- BLS lockup changes (2020) — https://www.bls.gov/bls/changes-to-dol-media-lockup-effective-march-1-2020.htm
- "Can Information Be Locked Up?" (CFA digest) — https://rpc.cfainstitute.org/research/cfa-digest/2017/02/can-information-be-locked-up-informed-trading-ahead-of-macro-news-announcements-digest-summary

**Latency hierarchy / HFT**
- HFT guide 2026 (Quantt) — https://www.quantt.co.uk/resources/high-frequency-trading-guide
- Latency standards in trading systems (LuxAlgo) — https://www.luxalgo.com/blog/latency-standards-in-trading-systems/
- HFT infrastructure (ICC) — https://www.icc-usa.com/blog/zero-latency-in-high-frequency-trading-solutions
- HFT software overview (InvestmentNews) — https://www.investmentnews.com/transformation/hft-trading-software/263152

**Drift / anomalies**
- Quantpedia: Post-Earnings Announcement Effect — https://quantpedia.com/strategies/post-earnings-announcement-effect
- PEAD review (ScienceDirect) — https://www.sciencedirect.com/science/article/pii/S2214635020303750
- PEAD (Wikipedia) — https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift

**Architecture**
- End-to-end LLM-enhanced trading system — https://arxiv.org/pdf/2502.01574
- Extracting structured insights from financial news with LLMs — https://arxiv.org/pdf/2407.15788
- LLM agents in financial trading: survey — https://arxiv.org/pdf/2408.06361
- News-aware direct RL trading — https://arxiv.org/pdf/2510.19173
- LLM trading bots compared (FlowHunt) — https://www.flowhunt.io/blog/llm-trading-bots-comparison/
- Platform comparison: IBKR/Alpaca/MetaTrader/QuantConnect — https://medium.com/@pta.forwork/comparing-algorithmic-trading-platforms-metatrader-interactive-brokers-ibkr-alpaca-3fc0fab11288
- Best broker APIs 2026 (TradeAlgo) — https://www.tradealgo.com/trading-guides/tools/best-broker-apis-for-algorithmic-trading-in-2026
- Best algo brokers US (BrokerChooser) — https://brokerchooser.com/best-brokers/best-brokers-for-algo-trading-in-the-united-states

**Backtesting**
- Survivorship bias explained (LuxAlgo) — https://www.luxalgo.com/blog/survivorship-bias-in-backtesting-explained/
- Point-in-time data and bias-free backtesting (sharpely) — https://sharpely.in/blog/bias-free-backtesting-explained:-how-sharpely-uses-point-in-time-data-to-avoid-look-ahead-and-survivorship-bias
- Backtesting problems and biases (AnalystPrep CFA) — https://analystprep.com/study-notes/cfa-level-2/problems-in-backtesting/
- Avoiding bias in backtesting (For Traders) — https://www.fortraders.com/blog/how-to-avoid-bias-in-backtesting
- Vector vs event-based backtesting (IBKR Quant) — https://www.interactivebrokers.com/campus/ibkr-quant-news/a-practical-breakdown-of-vector-based-vs-event-based-backtesting/
- A Test of Lookahead Bias in LLM Forecasts — https://arxiv.org/html/2512.23847v1
- Evaluating LLMs in finance requires explicit bias consideration — https://arxiv.org/html/2602.14233v1
- Fast solution to LLM look-ahead bias — https://arxiv.org/html/2512.06607v1
- MemGuard-Alpha (memorization-contaminated signals) — https://arxiv.org/pdf/2603.26797
- Assessing look-ahead bias in GPT sentiment stock predictions — https://arxiv.org/pdf/2309.17322
- The hidden danger of look-ahead bias in financial LLMs — https://www.financealliance.io/the-hidden-danger-of-look-ahead-bias-in-financial-llms/
- Backtesting limitations: slippage and liquidity (LuxAlgo) — https://www.luxalgo.com/blog/backtesting-limitations-slippage-and-liquidity-explained/
- Handling CPI/FOMC in trading systems (Benzinga) — https://www.benzinga.com/Opinion/26/06/53097073/trading-systems-and-market-news-how-to-handle-cpi-fomc-and-unexpected-events

**Risk and regulation**
- Nasdaq LULD FAQ — https://www.nasdaqtrader.com/content/MarketRegulation/LULD_FAQ.pdf
- LULD microstructure guide (Databento) — https://databento.com/microstructure/luld
- FINRA volatility guardrails — https://www.finra.org/investors/insights/guardrails-market-volatility
- Circuit breakers (Investor.gov) — https://www.investor.gov/introduction-investing/investing-basics/glossary/stock-market-circuit-breakers
- Stock halts explained (TradingSim) — https://www.tradingsim.com/blog/stock-halts-and-circuit-breaker-halts-explained
- SEC Rule 15c3-5 FAQ — https://www.sec.gov/rules-regulations/staff-guidance/trading-markets-frequently-asked-questions/divisionsmarketregfaq-0
- 15c3-5 compliance guide — https://www.sec.gov/files/rules/final/2010/34-63241-secg.htm
- Broker kill switches (Hedgeweek) — https://www.hedgeweek.com/dont-be-killed-your-brokers-market-access-kill-switches/
- FINRA Regulatory Notice 26-10 (PDT replacement) — https://www.finra.org/rules-guidance/notices/26-10
- PDT rule change (NerdWallet) — https://www.nerdwallet.com/investing/news/pattern-day-trading-rule-change
- PDT change explained (Schwab) — https://www.schwab.com/learn/story/sec-approves-scrapping-25000-day-trader-minimum
- Pattern day trader (Wikipedia) — https://en.wikipedia.org/wiki/Pattern_day_trader
- SEC FY2025 enforcement results — https://www.sec.gov/newsroom/press-releases/2026-34
- SEC $100M Discord/Twitter manipulation case — https://www.sec.gov/newsroom/press-releases/2022-221
- Securities enforcement 2025 year-end (Gibson Dunn) — https://www.gibsondunn.com/securities-enforcement-2025-year-end-update/
- SEC investor alert: social media stock rumors — https://www.sec.gov/oiea/investor-alerts-bulletins/ia_rumors.html
