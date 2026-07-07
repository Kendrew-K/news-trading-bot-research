# NLP, News-Sentiment, and LLM Trading Signals — Research Notes

**Scope:** Quantitative NLP and news-sentiment techniques used by quant funds and Wall Street to extract trading signals from text. Pure fundamental/news-driven — no technical analysis. Goal: inform the design of an automated news-trading bot.

**Date compiled:** June 2026

---

## 1. Overview

Text is the dominant "alternative data" category in quant finance. The core insight, established over ~20 years of academic and industry research, is that:

1. **News tone (sentiment) predicts returns**, but only briefly for daily signals (1–2 days) and longer for aggregated/weekly signals (up to a quarter) — Heston & Sinha (2017).
2. **Markets underreact to genuinely new information and overreact to stale (repeated) information** — Tetlock (2011). Distinguishing fresh from stale news is arguably the single most valuable filter in news trading.
3. **Speed matters enormously and varies by market cap.** Large-cap prices react to machine-readable news within milliseconds; small-cap and complex/negative news can take days-to-months to be fully priced (post-earnings-announcement drift persists up to ~60 days).
4. **Domain-specific NLP beats generic NLP** (Loughran-McDonald vs Harvard dictionary; FinBERT vs vanilla BERT; GPT-4 vs simpler models in Lopez-Lira & Tang).
5. **Alpha decays as adoption grows.** Every published text signal — WSB recommendations post-GameStop, ChatGPT news scores from 2021→2024 — shows documented decay. A bot must be designed for signal half-lives that shrink.

The industrial pipeline is consistent across vendors and funds: **ingest → entity resolution (which ticker?) → event classification (what happened?) → relevance scoring → novelty scoring (is it new?) → sentiment scoring → aggregation → portfolio construction.** Each stage is covered below.

---

## 2. Commercial News-Sentiment Systems

### 2.1 RavenPack (the quant-fund standard)

**What it is.** Investment-grade news analytics since 2003; per its own marketing, used by **more than 70% of the best-performing quant hedge funds** for alpha and risk management. For every entity/event detected in a story it emits structured analytics:

- **Relevance (REL), 0–100** — how strongly the story is about a given firm. 0 = vague mention; 75+ is treated as "significantly relevant"; serious strategies typically filter REL ≥ 90 or 100.
- **Event Novelty Score (ENS)** — novelty of the story within a 24-hour window (first report of an event vs repeats).
- **Event Sentiment Score (ESS)** — directional sentiment of the detected event for that entity.
- **Event taxonomy** — stories mapped to **~6,900 event categories in 56 groups** (earnings, analyst ratings, M&A, litigation, executive changes, labor disputes, product recalls, etc.).

**Why it works.** Filtering on novelty + relevance isolates the subset of stories that actually carry new firm-specific information; vendor backtests report the combined filter raises **Information Ratio by up to ~50%**, with sentiment indicators statistically significant at the 95% level across regions and size buckets.

**Documented numbers (RavenPack/Hafez research papers):**
- A Russell 3000 long/short sentiment strategy with a "similarity gap" filter (trading only *infrequent* news — stocks that have not been in the news recently) raised the Russell 2000 Sharpe from **1.03 to 1.43** (backtest Jan 2007–Aug 2014).
- APAC sentiment strategies: Sharpe **>2.0 in Asia ex-Japan**, ~1.1 Europe, ~1.2 US; small-cap APAC Information Ratios of **~2.5 for holding periods up to a month** vs ~1 for mid/large-cap — direct evidence that text alpha is bigger and slower-decaying in small caps.
- Reaction to news varies "tremendously" by event category — legal/regulatory, labor disputes, and executive-appointment events behave very differently from earnings events, which is why an event taxonomy matters.

**How funds consume it:** flat-file/API feed of per-story records (timestamp, entity ID, REL, ENS, ESS, event category) joined to their security master; the scores enter cross-sectional ranking models or event-driven execution triggers.

### 2.2 Bloomberg Event-Driven Feeds / News Sentiment

Machine-readable analytics at two levels: **story-level** (sentiment, "impact", novelty, readership heat, social velocity) and **company-level** rollups. Bloomberg's published research ("Trading the news: use machine-readable data to find alpha") reports a 15-month backtest where **8 of 9 sentiment-driven portfolios beat benchmarks with Sharpe ratios from 0.66 to 5.77**, low beta and low volatility. Bloomberg also scores tweets for relevance/sentiment per ticker; strategies on news-only, Twitter-only, and combined feeds all reportedly beat index ETFs in their studies.

### 2.3 Refinitiv / LSEG News Analytics (Thomson Reuters MRN)

Machine Readable News scores Reuters (and select third-party) stories in real time: per-ticker sentiment probabilities (positive/neutral/negative from a neural classifier), relevance, novelty (linked-story counts at 12h/24h/3d/5d/7d windows), and topic codes. Delivered **across the latency spectrum — <3 ms for Ultra Low Latency headline feeds** up to daily aggregates. This is the dataset behind Heston & Sinha (2017) (Section 3.3). LSEG also sells AI-scored **earnings-call transcript analytics** (LLM-measured CEO tone) as an alpha product.

### 2.4 Dow Jones Machine-Readable / Elementized News Feed

First machine-readable feed for institutional traders (launched 2007). Delivers corporate and economic news as **discrete XML-tagged elements** (no text parsing needed) in milliseconds: earnings figures, M&A announcements, analyst up/downgrades, executive changes, bankruptcies, splits, restatements, plus sentiment tags, for all US/Canadian and major UK names. The DJ Newswire archive is also the dataset behind Ke-Kelly-Xiu (Section 3.4) — described there as "widely subscribed and closely monitored by sophisticated investors."

**Bot takeaway:** the commercial vendors converged on the same five fields — **entity, relevance, novelty, sentiment, event type** — because those are the fields that matter. A home-built bot should reproduce that schema even if each component is cruder.

---

## 3. Academic Foundations

### 3.1 Tetlock (2007) — "Giving Content to Investor Sentiment" (Journal of Finance 62:1139–1168)

Measured daily pessimism in the WSJ "Abreast of the Market" column using the Harvard General Inquirer word lists. Findings:
- **High media pessimism → downward pressure on market prices, followed by reversion to fundamentals** (i.e., the tone effect is partly sentiment/noise-trader pressure, not pure information).
- Unusually high *or* low pessimism predicts **high trading volume**.
- Pattern consistent with noise/liquidity-trader models; inconsistent with media tone being pure new fundamental information.

**Implication for a bot:** market-wide media tone is a short-horizon contrarian/mean-reversion signal; firm-specific event news (later literature) is a continuation signal. Don't conflate the two.

### 3.2 Loughran & McDonald (2011) — "When Is a Liability Not a Liability?" (Journal of Finance 66)

Showed that **~3/4 of "negative" Harvard-dictionary words in 10-Ks are not negative in finance** ("liability", "cost", "tax", "cancer" in pharma…). Built finance-specific word lists: **negative, positive, uncertainty, litigious, constraining, superfluous** (plus modal-strength lists). LM-negative tone in filings predicts returns around the filing, volume, volatility, fraud, material weaknesses, and unexpected earnings; later work links tone to weekly return predictability. The LM dictionary remains the standard fast/transparent baseline and the standard *sanity check* for fancier models.

**Bot takeaway:** a dictionary scorer is essentially free, fully auditable, has zero look-ahead bias, and runs in microseconds — keep one in the stack as baseline and tie-breaker.

### 3.3 Heston & Sinha (2017) — "News vs. Sentiment" (Financial Analysts Journal 73(3):67–83; Fed FEDS 2016-048)

900k+ stories scored by the Thomson Reuters neural classifier. Key horizon results:
- **Daily news sentiment predicts returns for only 1–2 days.**
- **Weekly aggregated sentiment predicts returns for up to a quarter.**
- **Positive news is priced quickly; negative news has a long delayed reaction**, with much of the delayed response realized at the *next earnings announcement*.

**Bot takeaway:** aggregation window is a design parameter, not a detail. A fast bot trades the 0–2 day window; a slow bot holds weekly-aggregated sentiment portfolios for weeks. Negative-news shorts have longer runway than positive-news longs.

### 3.4 Ke, Kelly & Xiu (2019) — "Predicting Returns with Text Data" (NBER w26186; SESTM)

Supervised learning (SESTM: screen sentiment words by return-predictiveness → weight via topic model → score articles via penalized MLE) on the Dow Jones Newswires archive (38 years). Results:
- **Net-of-cost Sharpe ratios up to 4.0 (weekly), 1.5 (monthly), 0.9 (quarterly)** for equal-weight long-short portfolios.
- Newswire information is assimilated **with an exploitable delay**, tradable in real time with reasonable turnover, net of costs.
- Supervised, return-trained sentiment beats off-the-shelf vendor and dictionary scores — i.e., *train the scorer on your own label (forward returns), not on human sentiment labels*.

### 3.5 Tetlock (2011) — "All the News That's Fit to Reprint" (Review of Financial Studies 24(5):1481–1512)

Defines **staleness as textual similarity to the previous 10 stories about the same firm**. Findings:
- Returns respond less to stale news, **but the day-of reaction to stale news negatively predicts the next week's return** — i.e., reactions to stale news **reverse**; reactions to fresh news persist or continue.
- Individual investors trade more aggressively on stale news; reversal is largest in stocks with high retail activity.

**Implication for a bot:** this is a two-sided signal. (a) Only trade *continuation* on news that is textually novel. (b) Optionally *fade* price moves that occur on stale news, especially in retail-heavy names. Novelty detection (Section 6.1) is the prerequisite for both.

### 3.6 Post-Earnings-Announcement Drift (PEAD)

The granddaddy news anomaly (Ball & Brown 1968; Bernard & Thomas 1989): prices **drift in the direction of an earnings surprise for up to ~60 days**. Drift is **stronger in small, illiquid, low-analyst-coverage stocks** where information diffuses slowly. Modern evidence (Chordia & Miao, MIT; UCSD studies) shows the *immediate* reaction in large caps now completes in **milliseconds**, and post-announcement opportunities at second-level latency have largely vanished in big liquid names — but multi-day drift survives in the small/neglected cross-section. Heston-Sinha's "negative news resolves at the next earnings announcement" links news-tone signals directly to PEAD.

---

## 4. Modern LLM-Based Approaches

### 4.1 FinBERT

BERT further pre-trained on financial corpora, fine-tuned on Financial PhraseBank for positive/neutral/negative classification (Araci 2019; Yang et al. 2020 "FinBERT: A Pretrained Language Model for Financial Communications"). Materially more accurate than generic sentiment models on financial text (handles "risk", "uncertainty", hedging language). Recent backtesting papers (e.g., "Backtesting Sentiment Signals for Trading", arXiv 2507.03350; SHAP-explainability studies) find FinBERT features **improve classification accuracy, AUC, and simulated trading P&L over dictionary baselines**, with feature importance highest around earnings and high-volatility regimes. Runs locally, cheap, deterministic, no look-ahead-bias problem if you only feed it the headline. Standard architecture: FinBERT score per headline → aggregate per ticker per day → cross-sectional rank.

### 4.2 Lopez-Lira & Tang (2023) — "Can ChatGPT Forecast Stock Price Movements?" (arXiv 2304.07619, SSRN 4412788)

The reference paper for LLM headline trading. Prompted GPT to rate whether a headline is good/bad/unknown for the company (out-of-sample, post-knowledge-cutoff news). Findings:
- GPT-4 correctly anticipated the direction of next-day reaction on **93.3% of days for overnight news, 88.8% for intraday news**.
- Long-short on GPT-4 scores: **Sharpe 3.28** over the sample, vs 1.79 (GPT-3.5), 1.61 (DistilBart-MNLI), and **negative Sharpe for most simple/dictionary models**. Drift strategies: Sharpe **2.97 (overnight) / 2.63 (intraday)** pre-cost.
- The **short leg carries most of the alpha** (short leg ~26 bps/day, Sharpe 2.01 vs long leg 8 bps/day, Sharpe 0.78) — consistent with slow incorporation of negative news (Heston-Sinha).
- **Decay is documented in the paper itself:** annualized Sharpe fell from **6.54 (2021Q4) → 3.68 (2022) → 2.33 (2023) → 1.22 (Jan–May 2024)** as LLM adoption spread. Complexity/size of model correlates with predictive power.

### 4.3 LLM Earnings-Call Analysis

- LSEG and others now sell LLM-measured **CEO tone scores from transcripts** as a packaged alpha signal.
- Communication-quality research (Chiang et al. 2025, ~192k transcripts, embedding-based Q&A alignment): a long/short on **"on-topic & proactive" vs "off-topic & reactive" executives generated ~+515 bps annualized alpha**. Evasive, off-topic answers to analyst questions are a robust short signal.
- Classical antecedents: hedging-language density ("may", "could", "we expect"), tone change vs prior quarter's call, and Q&A-section tone (less scripted than prepared remarks) all predict drift.

### 4.4 Agentic LLM Trading Research

- **TradingAgents** (Xiao et al., arXiv 2412.20138): multi-agent LLM "trading firm" — fundamental/sentiment/news analysts, bull-vs-bear debate, trader, risk manager — reports improved cumulative return, Sharpe, and drawdown vs rule-based baselines in US equity backtests. Open source (TauricResearch/TradingAgents).
- **FinMem / FinAgent**: layered-memory and multimodal reflection agents with superior backtests and reduced hallucination.
- Caveat: most agentic backtests are short, on a handful of liquid tickers, and within the LLM's knowledge window — treat reported numbers as upper bounds.

### 4.5 The Look-Ahead-Bias Problem (critical pitfall)

LLMs memorize history. If you backtest GPT-4 on 2021 headlines, the model *knows what happened next* (earnings, prices, the fact that SVB collapsed), so backtests are inflated. Documented in:
- Noguer i Alonso (2024), "Look-Ahead Bias in LLMs: Implications and Applications in Finance" (SSRN 5022165).
- Sarkar (2025), "AI's predictable memory in financial analysis" (Economics Letters) — LLMs reproduce memorized financials.
- arXiv 2512.06607 "A Fast and Effective Solution to the Problem of Look-ahead Bias in LLMs" — logit-steering unlearning of post-date knowledge; arXiv 2603.26797 "MemGuard-Alpha" — membership-inference + cross-model-disagreement filters for contaminated signals.
- Key point: **a stated knowledge cutoff does not guarantee exclusion of post-cutoff information**, and RAG context can leak future info too.

**Mitigations for a bot:** (1) backtest only on data after the model's cutoff (Lopez-Lira & Tang's approach); (2) anonymize/mask tickers and entity names in prompts ("Can Blindfolded LLMs Still Trade?", arXiv 2603.17692); (3) prefer small fine-tuned models (FinBERT) for backtests, reserve big LLMs for live scoring; (4) paper-trade forward as the real test.

---

## 5. Social Media Signals

### 5.1 Twitter/X

- Twitter sentiment predicts intraday/next-day returns with **fast attenuation**; the signal is **not subsumed by news sentiment** (Gu & Kurov 2020, Journal of Banking & Finance — also found Twitter-sentiment predictability *without subsequent reversal*, suggesting genuine information, not just hype).
- Predictive power concentrates when **tweet volume is abnormally high** (event-conditional).
- Bloomberg's own research scores tweets per ticker and reports tweet-based portfolios beating ETF benchmarks.

### 5.2 StockTwits

- Classified sentiment (bullish/bearish tags + ML-classified messages) is **positively associated with contemporaneous returns but on average does NOT predict next-day returns** (Springer Digital Finance, 2023).
- Exception: **conditioning on message-volume spikes**, polarity does predict abnormal returns. Same lesson as Twitter: volume spike + direction, not level.

### 5.3 Reddit / WallStreetBets (post-GameStop literature)

- **Pre-GameStop, WSB "DD" recommendations significantly predicted returns and cash-flow news. Post-GME this predictability was eliminated** ("Place Your Bets? The Value of Investment Research on Reddit's WallStreetBets", 2024). The platform's culture shifted toward price-pressure/attention plays and informativeness collapsed — a textbook case of **crowding destroying a signal**.
- WSB activity remains useful for **volatility/squeeze risk prediction**: Reddit signals anticipate abrupt volatility shifts better than Twitter; strong Granger-causal feedback loop between WSB engagement and trading volume.
- GameStop-specific studies (Long et al. 2023, Financial Review; Fernandez-Perez et al. 2025): sentiment/emotions and comment volume tracked the rally, but causality is two-way and weak for prices generally.

**Bot takeaway:** treat social media as (a) an **attention/volume-spike detector** and **risk overlay** (avoid shorting names with exploding WSB mentions), not a standalone directional alpha; (b) only act directionally on *abnormal-volume* sentiment; (c) assume any popular social signal is crowded and decays.

---

## 6. Signal-Construction Concepts (the engineering layer)

### 6.1 Novelty detection — "is this news NEW?"
- Tetlock (2011) operationalization: cosine/textual similarity vs the previous N (he used 10) stories on the same firm; vendors use 24h–7d linked-story windows (RavenPack ENS; Refinitiv novelty counts at 12h/24h/3d/5d/7d).
- Bot implementation: embed each headline/story (sentence-transformer), keep per-ticker rolling vector store, novelty = 1 − max cosine similarity over trailing window. Trade continuation only above a novelty threshold; optionally fade moves on sub-threshold (stale) stories. Dedupe quality and window size are the two parameters that matter.

### 6.2 Entity recognition — "which ticker?"
- Map company mentions → instrument IDs via a security master with aliases, former names, subsidiaries, ADRs. The classic hidden-error source (Apple the company vs apple the fruit; "Meta"/"Alphabet" vs common words; parent vs subsidiary attribution).
- Vendors solve with curated entity databases + relevance scores. Bot: NER model (or LLM extraction, e.g. arXiv 2407.15788 "Extracting Structured Insights from Financial News") + alias table + a relevance score (entity in headline vs buried in paragraph 8). **Only trade headline-level relevance** at first.

### 6.3 Event classification taxonomies
- RavenPack: ~6,900 categories / 56 groups. Dow Jones elementized feed: earnings, M&A, analyst revisions, executive changes, bankruptcy, splits, restatements. Minimum viable taxonomy for a bot (~10–15 classes): earnings beat/miss, guidance raise/cut, M&A target, M&A acquirer, analyst upgrade/downgrade, FDA/regulatory approval/rejection, litigation, executive departure, buyback/dividend, offering/dilution, bankruptcy/going-concern, product recall, contract win.
- Each class gets its **own expected response and half-life** (an M&A-target headline reprices in seconds and is done; a guidance cut drifts for days; litigation drifts for weeks). Market reaction "varies tremendously" across event types (RavenPack/Hafez).

### 6.4 Sentiment aggregation windows
- Heston-Sinha: daily score → 1–2 day predictability; weekly aggregate → one-quarter predictability. Aggregation also de-noises single-story classification errors. Common practice: per-ticker exponentially-decayed sum of (sentiment × relevance × novelty) over a chosen window; one fast (hours) and one slow (5-day) version.

### 6.5 Decay half-lives
- Large-cap scheduled news (earnings, macro prints): priced in **milliseconds-to-seconds** (Chordia-Miao; UCSD; macro prints react within ~5 ms). A retail-latency bot has no edge here on direction — only on *drift after* the jump.
- Unscheduled/complex news, small caps, negative news: **hours to days** of underreaction; PEAD up to **60 days**; weekly sentiment up to **a quarter**; APAC small-cap sentiment IR>2 at ~1-month holding.
- Strategy-level decay: ~50% of published anomaly alpha disappears post-publication (McLean-Pontiff); ChatGPT-signal Sharpe fell ~80% in 2.5 years; WSB predictability went to zero post-GME. Budget for it.

### 6.6 Cross-sectional vs time-series usage
- **Cross-sectional** (the quant-fund default): each day/week, rank the universe by aggregated news score; long top decile, short bottom decile; market-neutral; works because idiosyncratic news errors diversify. All the big Sharpe numbers above (Ke-Kelly-Xiu 4.0, Lopez-Lira 3.28) are cross-sectional long-shorts.
- **Time-series / event-driven**: per-event trigger → enter at detection → exit at estimated half-life or at the next earnings date. This is the natural mode for a single-account bot that can't short hundreds of names; expect lumpier P&L and fewer, bigger bets.
- Market-level tone (Tetlock 2007) is a third, contrarian time-series mode (fade extreme aggregate pessimism over days).

---

## 7. Evidence and Numbers — Cheat Sheet

| Signal / Study | Result | Caveat |
|---|---|---|
| Ke-Kelly-Xiu SESTM on DJ Newswires (2019) | Sharpe **4.0 weekly / 1.5 monthly / 0.9 quarterly**, net of costs | EW portfolios; pre-2018 sample; needs newswire feed |
| Lopez-Lira & Tang GPT-4 headlines (2023) | Sharpe **3.28** L/S; 93% next-day direction hit rate; short leg = most alpha | Sharpe decayed 6.54→1.22 from 2021Q4 to 2024 |
| Bloomberg sentiment backtest | 8/9 portfolios beat benchmark; Sharpe **0.66–5.77** | Vendor research, 15-month window |
| RavenPack infrequent-news filter | Russell 2000 Sharpe **1.03→1.43** | Vendor research, 2007–2014 |
| RavenPack APAC small-cap | IR **>2.5** at ≤1-month holding | Small caps = capacity limits |
| Chiang et al. earnings-call Q&A quality | **+515 bps/yr** alpha (on-topic vs evasive execs) | Quarterly-frequency, slow signal |
| Heston-Sinha horizons | Daily news → 1–2 days; weekly → 1 quarter; negative news resolves at next earnings | Vendor (TR) sentiment engine |
| Tetlock 2011 stale news | Stale-news-day returns **reverse next week**, esp. retail-heavy names | Needs similarity infrastructure |
| PEAD | Drift up to **~60 days**, strongest in small/low-coverage names | Large-cap immediate reaction now in **milliseconds** |
| WSB post-GameStop | Return predictability **eliminated** post-2021 | Still useful for volatility/squeeze risk |
| Speed of pricing | Macro prints: **~5 ms**; large-cap earnings: ms; second-delay traders: no edge in large caps | Retail bot must trade *drift*, not the jump |

---

## 8. Blueprint Implications for a News-Trading Bot

1. **Don't race the jump — trade the drift.** At retail latency the millisecond repricing is gone. Target: small/mid caps, negative news, complex events, PEAD windows, weekly-aggregated sentiment.
2. **Reproduce the vendor schema:** per story compute (ticker via entity resolution, event class, relevance, novelty via embedding similarity vs trailing per-ticker stories, sentiment via FinBERT + LM dictionary + optional LLM).
3. **Gate every trade on novelty and relevance** (the documented +50% IR filter). Consider a separate stale-news *fade* book per Tetlock 2011.
4. **Short leg first.** Negative news underreaction is the most robust documented edge (Heston-Sinha delayed negative reaction; Lopez-Lira short-leg Sharpe 2.01 vs long 0.78). If shorting is impractical, at minimum use negative news as an exit/avoid signal.
5. **Per-event-class playbook:** expected move, half-life, and exit rule per event type; exit residual positions before/at the next earnings date (where delayed reactions resolve).
6. **LLM hygiene:** backtest only post-cutoff; mask entities when validating; keep FinBERT/LM as deterministic baselines; forward paper-trade before sizing.
7. **Social media = risk overlay + volume-spike trigger,** not standalone alpha. Never short a name with an abnormal WSB/StockTwits mention spike.
8. **Assume decay:** monitor live IR vs backtest, halve size when realized edge < 50% of backtest, and keep a research loop adding new event classes as old ones crowd out.

---

## 9. Sources

**Commercial systems**
- RavenPack — News Analytics product & relevance/novelty/event docs: https://www.ravenpack.com/products/edge/data/news-analytics ; event detection: https://www.ravenpack.com/blog/new-ravenpack-analytics-event-detection ; NLP in quant investing: https://www.ravenpack.com/blog/natural-language-processing-quant-investing/
- RavenPack Data Science / Hafez — "Improved Stock Market Returns From Systematically Trading Infrequent News": https://www.researchgate.net/publication/351360077 ; "News Sentiment Everywhere: Trading Global Equities": https://www.researchgate.net/publication/351360244 ; APAC factor study: https://www.ravenpack.com/research/news-sentiment-apac
- Bloomberg — "Trading the news: Use machine-readable data to find alpha": https://www.bloomberg.com/professional/insights/data/trading-news-use-machine-readable-data-find-alpha/ ; "How you can get an edge by trading on news sentiment data": https://www.bloomberg.com/professional/insights/data/can-get-edge-trading-news-sentiment-data/ ; Event-Driven Feeds: https://www.bloomberg.com/professional/products/data/enterprise-catalog/event-driven-feeds/
- LSEG/Refinitiv — Machine Readable News: https://www.lseg.com/en/data-analytics/financial-news-service/machine-readable-news ; MRN factsheet: https://www.lseg.com/content/dam/data-analytics/en_us/documents/fact-sheets/machine-readable-news-and-quantitative-data-factsheet.pdf ; AI earnings-call analytics: https://www.lseg.com/en/insights/data-analytics/ai-unlock-investment-risk-management-opportunities-earnings-call-transcripts
- Dow Jones Elementized News Feed — launch coverage: https://www.prnewswire.com/news-releases/dow-jones-introduces-news-analytics-to-institutional-trading-community-139277198.html ; https://www.finextra.com/newsarticle/17594/dow-jones-adds-company-news-to-machine-readable-xml-data-feed

**Academic foundations**
- Tetlock, P. (2007). "Giving Content to Investor Sentiment: The Role of Media in the Stock Market." *Journal of Finance* 62(3): 1139–1168. https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1540-6261.2007.01232.x ; PDF: https://business.columbia.edu/sites/default/files-efs/pubfiles/3097/Tetlock_Media_Sentiment_JF.pdf
- Tetlock, P. (2011). "All the News That's Fit to Reprint: Do Investors React to Stale Information?" *Review of Financial Studies* 24(5): 1481–1512. https://academic.oup.com/rfs/article-abstract/24/5/1481/1613314 ; SSRN: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1018221
- Loughran, T. & McDonald, B. (2011). "When Is a Liability Not a Liability? Textual Analysis, Dictionaries, and 10-Ks." *Journal of Finance* 66(1). https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1540-6261.2010.01625.x ; SSRN: https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1331573
- Heston, S. & Sinha, N. (2017). "News vs. Sentiment: Predicting Stock Returns from News Stories." *Financial Analysts Journal* 73(3): 67–83. https://www.federalreserve.gov/econres/feds/news-versus-sentiment-predicting-stock-returns-from-news-stories.htm ; https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2792559
- Ke, Z.T., Kelly, B. & Xiu, D. (2019). "Predicting Returns with Text Data." NBER w26186. https://www.nber.org/papers/w26186 ; AQR version: https://www.aqr.com/Insights/Research/Working-Paper/Predicting-Returns-with-Text-Data
- PEAD — Quantpedia: https://quantpedia.com/strategies/post-earnings-announcement-effect ; review: https://www.sciencedirect.com/science/article/pii/S2214635020303750 ; Wikipedia: https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift
- Speed of price adjustment — Chordia & Miao, "Market Efficiency in Real Time: Evidence from Low Latency...": https://mitsloan.mit.edu/sites/default/files/inline-files/Chordia_Miao.pdf ; UCSD on earnings jumps: https://today.ucsd.edu/story/earnings-news-cause-immediate-stock-price-jumps-sometimes-moving-whole-market

**LLM approaches**
- Lopez-Lira, A. & Tang, Y. (2023). "Can ChatGPT Forecast Stock Price Movements? Return Predictability and Large Language Models." arXiv:2304.07619 / SSRN 4412788. https://arxiv.org/abs/2304.07619 ; https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4412788
- FinBERT — https://finbert.org/ ; Yang et al. (2020) "FinBERT: A Pretrained Language Model for Financial Communications": https://arxiv.org/pdf/2006.08097 ; "Backtesting Sentiment Signals for Trading": https://arxiv.org/pdf/2507.03350 ; QuantConnect FinBERT docs: https://www.quantconnect.com/docs/v2/writing-algorithms/machine-learning/hugging-face/popular-models/finbert
- TradingAgents (Xiao et al. 2024/2025): https://arxiv.org/abs/2412.20138 ; code: https://github.com/tauricresearch/tradingagents
- Look-ahead bias — Noguer i Alonso (2024), SSRN 5022165: https://www.ssrn.com/abstract=5022165 ; "A Fast and Effective Solution to the Problem of Look-ahead Bias in LLMs": https://arxiv.org/abs/2512.06607 ; "AI's predictable memory in financial analysis": https://www.sciencedirect.com/science/article/pii/S0165176525004392 ; MemGuard-Alpha: https://arxiv.org/pdf/2603.26797 ; "Can Blindfolded LLMs Still Trade?": https://arxiv.org/pdf/2603.17692
- Earnings-call communication alpha (Chiang et al. 2025, via LSEG insights above); review of LLMs for stock forecasting (hedge-fund perspective): https://arxiv.org/html/2605.05211

**Social media**
- "Place Your Bets? The Value of Investment Research on Reddit's WallStreetBets" (2024): https://www.researchgate.net/publication/377492919
- Long, C. et al. (2023). "'I just like the stock': The role of Reddit sentiment in the GameStop share rally." *Financial Review*: https://onlinelibrary.wiley.com/doi/10.1111/fire.12328
- Fernandez-Perez et al. (2025). "Emotions and stock returns during the GameStop bubble." *Financial Review*: https://onlinelibrary.wiley.com/doi/10.1111/fire.12438
- Gu, C. & Kurov, A. (2020). "Informational role of social media: Evidence from Twitter sentiment." *Journal of Banking & Finance*: https://www.sciencedirect.com/science/article/abs/pii/S0378426620302314
- "StockTwits classified sentiment and stock returns." *Digital Finance* (2023): https://link.springer.com/article/10.1007/s42521-023-00102-z
- Reddit/Twitter volatility study: https://www.researchgate.net/publication/396206198 ; CEPR on Twitter sentiment: https://cepr.org/voxeu/columns/twitter-sentiment-and-stock-market-movements-predictive-power-social-media

**Pipeline / engineering**
- "Extracting Structured Insights from Financial News: An Augmented LLM Driven Approach": https://arxiv.org/abs/2407.15788
- Newsdata.io, "Integrating News Data into Quantitative Finance Models": https://newsdata.io/blog/newsdata-in-quant-models/
- Alpha decay modeling: "Not All Factors Crowd Equally": https://arxiv.org/pdf/2512.11913
