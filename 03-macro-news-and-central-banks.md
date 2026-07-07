# Macro News & Central Bank Trading: How Professionals Trade Fundamentals (No Technicals)

Research dossier for the design of an automated news-trading bot. Covers scheduled macro releases, central bank communication parsing, nowcasting, geopolitical event trading, news-based macro indices, and execution realities. All claims sourced; full source list at the end.

---

## 1. Overview: The Professional Macro News-Trading Stack

Professional macro trading on news is **not** about predicting the economy. It is about three narrower, tractable problems:

1. **Expectations accounting.** Every scheduled release (CPI, NFP, FOMC) has a *consensus forecast* already embedded in prices. The tradeable object is the **surprise** — `actual − consensus` — usually standardized by the historical standard deviation of past surprises. The number itself is almost irrelevant; the deviation from what was priced is everything.
2. **Text-as-data.** Central bank statements, minutes, and speeches are parsed word-by-word (literal diffs of consecutive FOMC statements) and scored on a hawkish–dovish axis using dictionaries, embeddings, and LLMs. The *change in language* is the signal.
3. **Information-speed arbitrage.** At the release millisecond, HFT firms with machine-readable feeds win. Everyone else trades the **drift** — the well-documented tendency of prices to continue adjusting for minutes, hours, or days after a genuine narrative-changing surprise — or trades **positioning ahead** of releases via nowcasts and alternative data.

The macro news-trading edge thus decomposes into: (a) measuring expectations better, (b) reading the text faster/better, (c) predicting releases before they happen, and (d) exploiting predictable over/underreaction. A retail-speed bot can realistically pursue (a) as a framework, (b) cheaply with NLP, (c) using free public nowcasts, and (d) as its core alpha — it cannot win (and should not enter) the millisecond race.

---

## 2. Trading Scheduled Macro Releases (CPI, NFP, FOMC, GDP, PMI)

### What it is
A small set of scheduled US releases moves global markets: monthly CPI (~8:30 ET), Nonfarm Payrolls (first Friday, 8:30 ET), FOMC rate decisions (2:00 PM ET, 8×/year), quarterly GDP, and ISM/S&P PMIs. High-impact releases can move major FX pairs 100–400 pips in minutes ([PriceActionNinja guide](https://priceactionninja.com/forex-news-trading-guide-nfp-cpi-fomc-major-releases/); [FXEmpire](https://www.fxempire.com/education/article/news-driven-fx-trading-how-to-trade-events-like-the-fomc-cpi-and-nfp-1549791)).

### The surprise-vs-consensus framework
- The market trades `S = (actual − E[consensus]) / σ(historical surprises)` — a **standardized surprise**. Consensus is the Bloomberg/Reuters median survey of economists. An in-line print produces little volatility; a 2σ miss produces a violent repricing.
- The canonical academic treatment is **Andersen, Bollerslev, Diebold & Vega**, "Real-Time Price Discovery in Global Stock, Bond and Foreign Exchange Markets" (*Journal of International Economics*, 2007; [NBER w11312](https://www.nber.org/papers/w11312), [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=949180)). Findings:
  - News surprises produce **conditional mean jumps** — prices gap, they don't glide. High-frequency dynamics are genuinely linked to fundamentals.
  - **Bond markets react most strongly** to macro news, then FX, then equities (unconditionally).
  - **Sign-flipping by regime:** equities treat "good news as bad news" in expansions (strong data → higher rates expected → stocks down) but good-news-as-good-news in recessions. Any bot's reaction mapping must be **regime-conditional**, not static.
- **Citi Economic Surprise Index (CESI)** operationalizes this at the portfolio level: a rolling 3-month, time-decayed, weighted sum of standardized surprises, with weights derived from each indicator's historical FX impact ([FP Markets explainer](https://www.fpmarkets.com/uk/education/trading-guides/what-is-the-citigroup-economic-surprise-index/); academic version: Federal Reserve IFDP 1093, ["Surprise and Uncertainty Indexes"](https://www.federalreserve.gov/pubs/ifdp/2013/1093/ifdp1093.pdf), Scotti 2013). Positive CESI = data running hot vs. forecasts; mean-reverting by construction (analysts adapt).

### How markets price expectations: fed funds futures and FedWatch
- **30-Day Fed Funds futures** (CME) price the expected average effective fed funds rate for each month. The **CME FedWatch tool** converts these prices into meeting-by-meeting probabilities of hikes/cuts, assuming moves come in 25 bp multiples ([CME methodology](https://www.cmegroup.com/articles/2023/understanding-the-cme-group-fedwatch-tool-methodology.html); [FedWatch tool](https://www.cmegroup.com/markets/interest-rates/cme-fedwatch-tool.html)). The Atlanta Fed publishes an options-based alternative, the [Market Probability Tracker](https://www.atlantafed.org/research-and-data/data/market-probability-tracker).
- Professional framing of an FOMC decision: the decision is only a surprise relative to the **futures-implied probability**, not relative to "no change." A cut that was 95% priced is a non-event; a cut that was 60% priced moves everything.
- **Gürkaynak, Sack & Swanson**, "Do Actions Speak Louder than Words?" (*International Journal of Central Banking*, 2005; [Fed FEDS 2004-66](https://www.federalreserve.gov/econres/feds/do-actions-speak-louder-than-words-the-response-of-asset-prices-to-monetary-policy-actions-and-statements.htm)) is the key decomposition: FOMC events contain **two factors** — a *target* surprise (the rate move itself) and a *path* surprise (what the statement implies about future policy). **75–90% of the explainable variation in 5- and 10-year Treasury yields around FOMC announcements is the path factor** — i.e., the words, not the rate change. A bot that only reads the headline rate decision misses most of the signal.

### Asset-class reaction map (the standard playbook)
For a **hawkish surprise** (hot CPI, strong NFP, hawkish FOMC): yields up (front-end most, on Fed repricing), USD up, gold down (higher real yields + stronger USD), equities usually down in tightening regimes. Dovish surprise: mirror image. The gold transmission chain — inflation expectations → rate expectations → real yields → USD → gold — is well documented ([FXStreet gold/CPI analysis](https://www.fxstreet.com/analysis/gold-enters-credibility-test-as-inflation-repricing-reaches-an-inflection-point-202606100655)). But note the ABDV regime-flip caveat for equities, and that an upside *inflation* surprise can eventually *support* gold via inflation-hedge demand if the market doubts the Fed will respond ([Investing.com gold/CPI](https://www.investing.com/analysis/gold-tests-resistance-as-investors-position-ahead-of-us-cpi-inflation-data-200676427)).

### Two-phase price action: spike then drift
Practitioner and academic sources agree news moves have two components: the **initial spike** (algos, milliseconds-to-seconds) and the **drift/follow-through** (minutes to weeks) as the market digests what the data means for the policy path ([Benzinga on trading systems and news](https://www.benzinga.com/Opinion/26/06/53097073/trading-systems-and-market-news-how-to-handle-cpi-fomc-and-unexpected-events)). Surprises that **change the narrative** ("inflation is back") produce multi-day trends; surprises that don't, mean-revert. There is also documented **post-FOMC announcement drift in bonds**: Brooks, Katz & Lustig, "Post-FOMC Announcement Drift in U.S. Bond Markets" ([NBER w25127](https://www.nber.org/papers/w25127), 2018) — long yields keep drifting in the direction of the policy surprise for weeks, driven by slow mutual-fund flows that arbitrageurs absorb only gradually.

### How a bot could implement it
- Maintain an economic calendar (release timestamps, consensus, prior) via API (Trading Economics, FXStreet, investing.com, or paid: Bloomberg/Refinitiv/Econoday).
- On release: compute standardized surprise per component (headline + core CPI; NFP payrolls + unemployment + average hourly earnings — internals often dominate the headline).
- Map surprise → expected direction per asset (futures: ZN/ZF for rates, 6E for EUR, GC for gold, ES/MES for equities) using a **regime-conditional** lookup estimated from past releases.
- Enter on the *drift* (e.g., 1–15 minutes post-release, after the spike, conditional on |S| > threshold like 1σ and the spike direction agreeing with the surprise sign), hold hours-to-days, exit on time or on the next narrative event.
- Track fed funds futures / FedWatch daily to know what is priced into FOMC before parsing any Fed text.

### Risks / pitfalls
- **Revisions:** NFP prior-month revisions can flip the signal; trade the full vector, not the headline.
- **Conflicting internals** (hot headline CPI, soft core) produce whipsaw — require coherence across components or stand aside.
- **Regime misclassification** is the biggest model risk (good-news-is-bad-news flips).
- **In-line prints**: no trade. Most releases are non-events; the bot must be comfortable doing nothing ~70% of the time.

---

## 3. Central Bank Communication Parsing

### 3.1 FOMC statement diff analysis
**What it is.** The FOMC statement is a short, highly conventionalized document changed only deliberately. Desks read the **redline diff vs. the previous statement first** — the diff *is* the news. Single-word changes ("solid" → "moderate"; adding/removing "patient," "for some time," "additional policy firming") reprice the implied rate path, because each phrase carries accumulated cycle-specific meaning ([PageCrawl on FOMC diff monitoring](https://pagecrawl.io/blog/fomc-statement-change-detection-monitoring)). The WSJ "Fed Statement Tracker" and the Fed's own redlines institutionalized this.

**Why it works.** Gürkaynak–Sack–Swanson (above): the path factor in the *words* drives most of the bond reaction. The statement is short enough that a word-level diff is a near-complete summary of the policy signal.

**Bot implementation.** Trivially automatable: scrape federalreserve.gov at 14:00:00 ET on decision days, run a sentence-level diff against the stored prior statement, classify each changed span as hawkish/dovish/neutral (dictionary or LLM), aggregate into a net score, compare against what futures had priced. This is one of the highest signal-to-effort components available to a small bot.

### 3.2 Hawkish/dovish scoring and Fed speech NLP
**What it is.** Quantifying the tone of statements, minutes, press conferences, and the ~hundreds of yearly Fed speeches on a hawk–dove axis.

**Approaches, from simple to state-of-the-art:**
- **Dictionary/bag-of-words:** counts of hawkish vs. dovish keywords (e.g., "inflation pressures," "tightening" vs. "accommodation," "downside risks"). Used in early ECB work (Picault & Renault, "Words are not all created equal," *JIMF* 2017, [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0261560617301808)) with ECB-specific dictionaries.
- **Fine-tuned transformers:** FinBERT and BERT models fine-tuned on labeled central-bank sentences classify hawkish/neutral/dovish with context sensitivity; a BERT-based central bank sentiment index forecasts the future policy path beyond a standard Taylor rule (Gorodnichenko-style deep-learning indices; ["Unveiling the sentiment behind central bank narratives"](https://www.sciencedirect.com/science/article/abs/pii/S2214635023000230), *Journal of Behavioral and Experimental Finance*, 2023). Kansas City Fed: Doh, Song & Yang, ["Deciphering Federal Reserve Communication via Text Analysis"](https://www.kansascityfed.org/documents/5642/rwp20-14dohsongyang.pdf) (RWP 20-14).
- **LLMs:** GPT-4-class models now match or beat fine-tuned classifiers on Fedspeak (Kim, Spörer & Handschuh 2023 compare VADER/FinBERT/GPT-4; see also the [MDPI agentic-retrieval paper](https://www.mdpi.com/2227-7390/13/20/3255), 2025).
- **Commercial/industrial:** Morgan Stanley's patented [MNLPFEDS sentiment index](https://www.morganstanley.com/articles/mnlpfeds-sentiment-index-federal-reserve); [MacroMicro's AI FOMC Hawkish-Dovish Index](https://en.macromicro.me/collections/4238/us-federal/74572/us-mm-fed-statement-hawkish-dovish-index) (0–100 scale); [MNI's FOMC Hawk-Dove Spectrum](https://www.mnimarkets.com/mni-fomc-hawk-dove-spectrum) (−10 to +10, ±2 neutral) ranking individual FOMC members; bank "hawk/dove cheat sheets" ([InTouch Capital Markets](https://www.itcmarkets.com/hawk-dove-cheat-sheet-2/), [BBVA](https://www.bbvamarketstrategy.com/public/fx-strategy/fed-ecb-boe-boj-hawk-dove-cheat-sheet/)) scoring each FOMC/ECB/BoE/BoJ member so a speech can be read *relative to the speaker's known bias*.

**Key professional nuance:** a hawkish speech from a known über-hawk is no news; the same words from a known dove are a major signal. Voter status and proximity to the next meeting (the pre-FOMC "blackout period") matter for weighting.

### 3.3 The pre-FOMC announcement drift (Lucca & Moench)
**What it is.** **Lucca & Moench, "The Pre-FOMC Announcement Drift"** (*Journal of Finance*, 2015; [NY Fed Staff Report 512](https://www.newyorkfed.org/research/staff_reports/sr512.html), [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1923197)): from Sept 1994–Mar 2011, the S&P 500 earned an average **+49 bp excess return in the 24 hours *before* scheduled FOMC announcements** — about **80% of total annual equity excess returns** earned on just 8 afternoons a year. No equivalent effect in Treasuries or money-market futures, and no such drift before other macro releases. International equity indices showed similar pre-FOMC gains.
**Status: largely dead.** Follow-up work (["The disappearing pre-FOMC announcement drift"](https://www.sciencedirect.com/science/article/pii/S1544612320315956), *Finance Research Letters*, 2020) shows the drift **essentially disappeared after 2015**, with some evidence the abnormal return migrated to the post-announcement press-conference window. Lesson for a bot: published anomalies decay; condition any pre-FOMC long-equity overlay on rolling out-of-sample performance, and treat it as a small seasonal tilt, not a core strategy.

### 3.4 Pre-release drift in other macro data (leakage)
Kurov, Sancetta, Strasser & Wolfe, "Price Drift Before U.S. Macroeconomic News: Private Information about Public Announcements?" (*JFQA* 2019; [ECB WP 1901](https://www.ecb.europa.eu/pub/pdf/scpwps/ecbwp1901.en.pdf)): since 2008, **7 of 18 market-moving US announcements show prices moving in the "correct" direction starting ~30 minutes before release**, accounting for ~half of the total adjustment — attributed to leakage and proprietary data collection (firms replicating the statistic before publication). Implication: by release time, part of the surprise may already be in the price; a bot can use the pre-release 30-minute drift direction as a *confirming/contradicting* feature.

### 3.5 ECB and BoJ equivalents
- **ECB:** decision at 14:15 CET, press conference at 14:45 CET — a two-stage event. Research splits the **Introductory Statement** vs. the **Q&A**; both move stocks, with distinct effects ([Finance Research Letters 2022](https://www.sciencedirect.com/science/article/abs/pii/S1544612322007048)). The ECB itself publishes work measuring its communication tone ([ECB blog, "How words guide markets," 2023](https://www.ecb.europa.eu/press/blog/date/2023/html/ecb.blog230809~f101598a82.en.html)); academic indices include a media-based Hawkish-Dovish index built from ~9,000 articles, 1999–2016 (Tobback et al.; see [IJCB textual analysis of ECB pressers](https://www.ijcb.org/sites/default/files/journal/v19n2/ijcb-v19n2-shifts-ecb-communication-textual-analysis-press-conference.pdf) and the [BIS volume on measuring central bank communication](https://www.bis.org/ifc/publ/ifcb44l.pdf)).
- **BoJ:** no fixed announcement time (decision drops "around midday" Tokyo when deliberations end — itself a timing signal), historically the most surprise-prone major central bank (e.g., 2016 NIRP, 2022/2024 YCC changes hit USD/JPY for hundreds of pips in minutes). Hawk/dove member scoring exists ([InTouch](https://www.itcmarkets.com/hawk-dove-cheat-sheet-2/), [BBVA cheat sheets](https://www.bbvamarketstrategy.com/public/fx-strategy/fed-ecb-boe-boj-hawk-dove-cheat-sheet/)). Translation latency (Japanese → English) historically created exploitable seconds for JP-language NLP.

### Risks / pitfalls
- Tone models trained on one regime (ZIRP era) misread another (inflation-fight era); "data dependent" flips meaning.
- Press-conference Q&A is unscripted — a single Powell ad-lib can reverse the statement-driven move within minutes. Don't size up until the presser ends.
- Hawkish *words* can coexist with dovish *projections* (dot plot); reconcile text score with SEP numbers ([State Street, "Hawkish Words, Dovish Moves"](https://www.ssga.com/us/en/institutional/insights/weekly-economic-perspectives-28-july-2025)).

---

## 4. Macro Nowcasting & Positioning Ahead of Data

### What it is
Predicting the release **before it happens** using higher-frequency data, then positioning before the print. If your nowcast diverges from consensus, you have a directional view on the surprise itself.

### Public, free, bot-usable nowcasts
- **Atlanta Fed GDPNow** ([model page](https://www.atlantafed.org/cqer/research/gdpnow), [explainer](https://www.atlantafed.org/research-and-data/data/gdpnow/explainer), [FRED series GDPNOW](https://fred.stlouisfed.org/series/GDPNOW)): aggregates statistical forecasts of **13 GDP subcomponents** using bridge equations, dynamic factor models, and Bayesian VARs, mimicking the BEA's own methodology. Updated **6–7 times per month** as inputs (ISM, construction, trade, retail, CPI/PPI, industrial production, housing starts...) arrive. The *changes* in GDPNow after each input release are themselves a real-time read on growth momentum.
- **NY Fed Staff Nowcast** (dynamic factor model) — the other canonical public GDP nowcast.
- **Cleveland Fed Inflation Nowcasting** ([page](https://www.clevelandfed.org/indicators-and-data/inflation-nowcasting)): daily-updated nowcasts of CPI, core CPI, PCE, core PCE using daily oil prices, weekly gasoline prices, and lagged inflation. Documented to **beat professional consensus surveys** in many periods ([Cleveland Fed EC 2023-06 real-time assessment](https://www.clevelandfed.org/publications/economic-commentary/ec-202306-real-time-assessment-inflation-nowcasting-cleveland-fed); methodology: [Cleveland Fed WP 24-06, "Nowcasting Inflation"](https://www.clevelandfed.org/-/media/project/clevelandfedtenant/clevelandfedsite/publications/working-papers/2024/wp2406.pdf)). For a CPI-trading bot this is the single most valuable free input: trade only when Cleveland nowcast minus Bloomberg consensus exceeds a threshold.

### Alternative data (the professional layer)
Hedge funds front-run releases with proprietary replication: web-scraped prices (PriceStats / MIT Billion Prices Project lineage for CPI), credit/debit card panels and payroll-processor data for NFP/retail sales, satellite imagery and shipping data for industrial activity, job-postings scrape (e.g., LinkUp, Indeed) for labor. The Kurov et al. pre-release drift evidence (Section 3.4) is consistent with this proprietary nowcasting being traded ahead of prints. A retail bot can't buy these panels but gets a diluted version free via the regional Fed nowcasts above, plus weekly public series (initial claims, AAA gasoline prices, Manheim used-car index for CPI components).

### How a bot could implement it
- Daily job: pull GDPNow, NY Fed Nowcast, Cleveland CPI nowcast; store deltas; compute `nowcast − consensus` for the next release.
- Position 1–3 days ahead (small size — this is the riskiest module), or use the nowcast purely as a **prior** so post-release logic distinguishes "genuine surprise" from "surprise vs. a stale consensus."
- Track the **Citi Surprise Index** regime: when CESI is strongly negative, consensus forecasters are systematically too optimistic — surprises cluster.

### Risks / pitfalls
- GDPNow is volatile early in the quarter (few inputs) — confidence-weight by days-to-release.
- The market may already trade off these public nowcasts (they're free to everyone) — the edge is in *combining* them with consensus gaps, not in the level.
- Positioning ahead of data is a coin-flip with a slightly weighted coin; never size it like the post-release drift trade.

---

## 5. Geopolitical Event Trading (Wars, Elections, Tariffs, OPEC, Sanctions)

### What it is
Unscheduled (or semi-scheduled, e.g., elections/OPEC meetings) events that hit risk premia rather than a single data series. Classic safe-haven rotation: long gold, CHF, JPY, USTs; short risk assets; long oil on supply-side conflicts ([Adams Brown on geopolitical events and markets](https://www.adamsbrownwc.com/blog/how-geopolitical-events-shape-the-markets-and-what-it-means-for-your-plan/); [GoMoon: 10 geopolitical FX events](https://gomoon.ai/blog/geopolitical-events-and-forex)). BlackRock maintains a public [Geopolitical Risk Dashboard](https://www.blackrock.com/corporate/insights/blackrock-investment-institute/interactive-charts/geopolitical-risk-dashboard) tracking market attention to specific risks.

### Overreaction / underreaction patterns (the actual edge)
- **Overreaction then fade is the modal pattern for pure-fear shocks.** The cleanest documented case: **US election night 2016** — Dow futures fell ~4% (S&P futures hit limit-down) as Trump's win became likely, consistent with event-study estimates (Wolfers & Zitzewitz, ["What do financial markets think of the 2016 election?"](https://www.brookings.edu/articles/what-do-financial-markets-think-of-the-2016-election/), Brookings 2016) that a Trump win implied an S&P ~11–12% lower; the entire decline reversed within hours and stocks closed *up* the next day. See also Wolfers & Zitzewitz, "The 'Standard Error' of Event Studies: Lessons from the 2016 Election" ([AEA P&P 2018](https://www.aeaweb.org/articles?id=10.1257%2Fpandp.20181090)) and the firm-level dispersion study ([NBER w23152](https://www.nber.org/system/files/working_papers/w23152/w23152.pdf)).
- **Macro-event effects fade**: practitioner reviews note tariff/OPEC/war-driven moves tend to decay unless they change cash flows persistently ([FinSyn on OPEC, tariffs and all-time highs](https://www.finsyn.com/opec-tariffs-and-all-time-highs-macro-events-and-investing/)). The professional question is always: *does this event change the earnings/rates path, or only the risk premium?* Risk-premium-only shocks mean-revert; cash-flow shocks (tariffs actually implemented, supply actually destroyed) trend.
- **Underreaction** shows up in slow-burn events: sanctions regimes, tariff escalation ladders, and wars that markets initially treat as local. Funds trade these via options (long vol into known dates like elections and OPEC meetings; VIX calls as tariff hedges — [optionstrading.org on geopolitics and options](https://www.optionstrading.org/blog/how-geopolitical-events-influence-options-markets/)).
- **OPEC specifics:** announcement-day oil moves depend on decision vs. *expected* quota change (same surprise framework as CPI); OPEC's pricing power has declined with US shale, muting reactions ([FinSyn](https://www.finsyn.com/opec-tariffs-and-all-time-highs-macro-events-and-investing/)).

### How a bot could implement it
- Headline ingestion (news API / wire feed) → event classifier (war escalation / tariff / OPEC / sanction / election) → asset map (oil, gold, CHF, JPY, defense stocks, affected FX).
- Rule of thumb encoded from the evidence: **fade pure-uncertainty spikes in equities after the first session if no cash-flow channel is identified; ride supply shocks in commodities.**
- Election playbook: trade the *reversal* risk, not the knee-jerk; or stay flat through the binary and trade the post-event repricing of specific sectors (the 2016 firm-level reaction was huge and durable in sector cross-section even though the index move reversed — [NBER w23152](https://www.nber.org/system/files/working_papers/w23152/w23152.pdf)).

### Risks / pitfalls
- Headline-bot adverse selection: fake/old/mistranslated headlines (the 2019 "WSJ tariff" flash events). Require two-source confirmation before acting on unscheduled headlines.
- Limit-locks and gap risk: futures can lock limit-down (election 2016) — stops don't protect through gaps; size for gap-through scenarios.
- Event taxonomy is hard; misclassifying a cash-flow shock as a fear shock (and fading it) is the expensive error.

---

## 6. News-Based Macro Indices as Signals

### Economic Policy Uncertainty (EPU) — Baker, Bloom & Davis
- **Paper:** "Measuring Economic Policy Uncertainty," *Quarterly Journal of Economics*, 2016 ([NBER w21633](https://www.nber.org/papers/w21633); [paper PDF](https://users.ssc.wisc.edu/~mchinn/bakerbloomdavis_QJE2016.pdf); live data at [policyuncertainty.com](https://www.policyuncertainty.com)).
- **Construction:** counts of articles in 10 major US newspapers (WSJ, WaPo, NYT-adjacent set incl. USA Today, LA Times, Chicago Tribune, Boston Globe, SF Chronicle, Dallas Morning News, Houston Chronicle, Miami Herald) containing terms from all three sets: **E** (economy/economic), **P** (policy: Congress, Fed, regulation, White House...), **U** (uncertain/uncertainty). Counts scaled by each paper's total articles per month, standardized, averaged, normalized to mean 100 over 1985–2010. Validated against 12,000 human-read articles. Daily and country-level versions exist; category sub-indices (monetary policy, trade policy, fiscal) are published ([BBD monetary policy indices](https://www.policyuncertainty.com/bbd_monetary.html)).
- **Findings:** EPU spikes around elections, debt-ceiling fights, wars, 9/11; high EPU foreshadows reduced investment, hiring, and output; at the firm level, policy-exposed sectors show higher stock volatility.

### Geopolitical Risk (GPR) — Caldara & Iacoviello
- **Paper:** "Measuring Geopolitical Risk," *American Economic Review*, 2022 ([AER page](https://www.aeaweb.org/articles?id=10.1257%2Faer.20191823); [Fed IFDP 1222](https://www.federalreserve.gov/econres/ifdp/files/ifdp1222.pdf); live data at [matteoiacoviello.com/gpr](https://www.matteoiacoviello.com/gpr.htm)).
- **Construction:** automated search of 10 newspapers' electronic archives counting articles on adverse geopolitical events in **8 categories**: war threats, peace threats, military buildups, nuclear threats, terror threats, beginning of war, escalation of war, terror acts. Split into **GPR-Threats** and **GPR-Acts** sub-indices; benchmark from 1985, historical (3-paper) version from 1900. Updated daily/monthly, freely downloadable.
- **Findings:** higher GPR predicts lower investment and employment, higher disaster probability and downside risk; both *threats* and *acts* matter (threats move risk premia before anything happens).

### How professionals use them / bot implementation
- **Regime filters, not entry signals.** High/rising EPU or GPR → cut leverage, widen stop assumptions, increase gold/duration allocation, expect surprise sensitivity to be amplified. The indices are slow (monthly headline, daily versions noisier) — they tell you *which playbook* to run, not *when to click*.
- A bot can replicate a real-time mini-EPU/GPR on a news API with the published keyword sets (the methodology papers list the exact term sets) and use the *innovation* (today's count vs. trailing average) as a volatility/risk-off feature. The newer literature ([arXiv unifying framework for unstructured-data inference](https://arxiv.org/pdf/2505.00282)) formalizes how to debias such text-derived measures.
- Pitfall: these are **uncertainty** measures, not direction measures. High EPU ≠ short equities (markets often rally through high measured uncertainty); use them to scale risk, not to pick sides.

---

## 7. Execution Realities for a News Bot

### Liquidity evaporation and the spike
- Market makers and HFTs **pull quotes seconds before scheduled releases**; depth collapses, spreads blow out, and the first prints after the number occur in a near-vacuum. Participation routinely drops ahead of CPI/central-bank decisions, producing thin, erratic moves ([B2Broker liquidity examples](https://b2broker.com/news/liquidity-examples/); [PickMyTrade on futures slippage](https://blog.pickmytrade.io/slippage-causes-futures-trading/)). Stop-loss cascades drain remaining book depth and create gaps; HFT liquidity is "ephemeral" — present in calm, gone under stress ([MOSS on order books](https://moss.sh/news/understanding-order-books-and-market-depth/)).
- Consequence: a market order at 8:30:00.5 on CPI day fills at catastrophic slippage. **Realized fills, not chart prices, determine spike-trade P&L** — and the chart price at the release is largely untradeable for anyone without colocation.

### The millisecond race
- The release-instant move is competed away by HFT firms consuming **machine-readable news feeds** (Bloomberg Event-Driven Feeds, Refinitiv/LSEG Machine Readable News, RavenPack) with servers colocated at CME Aurora / NY4. Latency arms race includes microwave networks between Chicago, NJ, and DC.
- **The 2013 Fed minutes incident:** on the September 2013 "no-taper" surprise, massive gold-futures orders hit Chicago **within ~1–2 ms of 2:00 PM ET — before the ~7 ms light-speed transit time from Washington**, implying the news was traded from a server already in Chicago, i.e., leaked from the media lockup; Nanex estimated ~$600M changed hands on the early access ([Washington Post, 2013](https://www.washingtonpost.com/news/wonk/wp/2013/09/24/traders-may-have-gotten-last-weeks-fed-news-7-milliseconds-early/)).
- **The lockup system and its end:** since the mid-1980s, DOL/BLS gave credentialed media data 30–60 min early in a secured, signal-shielded room with a government-controlled release switch. News organizations monetized this by selling preloaded low-latency feeds to traders. Electronics were banned from the lockup effective March 1, 2020 ([BLS notice](https://www.bls.gov/bls/changes-to-dol-media-lockup-effective-march-1-2020.htm); [Federal Register 2020-02383](https://www.federalregister.gov/documents/2020/02/07/2020-02383/announcing-elimination-of-electronic-devices-in-the-dol-lock-up-facility-for-participating-news)), and the lockup facility was **permanently discontinued June 3, 2020** — data now drops simultaneously on the BLS website for everyone ([Federal Register 2020-11297](https://www.federalregister.gov/documents/2020/05/27/2020-11297/announcing-discontinuation-of-the-dol-lock-up-facility-for-participating-news-media-organizations)). Today the race is "fastest website scraper + parser," still a sub-10-ms professional game.

### Why a retail-speed bot must trade the drift, not the spike
1. **You will never win the race**: hundreds of ms of internet + broker latency vs. competitors at tens of microseconds. By the time your order arrives, the efficient part of the move is over and you're providing exit liquidity.
2. **Slippage at the spike** routinely exceeds the average drift-trade edge; spreads at 8:30:00 can be 10–50× normal.
3. **The drift is the documented, capturable anomaly**: post-FOMC bond drift ([NBER w25127](https://www.nber.org/papers/w25127)), multi-hour/day continuation after narrative-changing surprises ([Benzinga](https://www.benzinga.com/Opinion/26/06/53097073/trading-systems-and-market-news-how-to-handle-cpi-fomc-and-unexpected-events)), regime-conditional reaction patterns (ABDV 2007). These play out over horizons where retail latency is irrelevant.

### Concrete execution rules for the bot
- **Never hold resting stop/market orders through a scheduled release** (gap-through risk); flatten or widen brackets pre-release.
- Enter **T+60s to T+15min** after release, only after spread normalizes (monitor bid-ask vs. trailing baseline) and only on |surprise| above threshold.
- Use **limit orders** sized well inside post-release depth; futures (MES, ZN, GC micro contracts) over CFDs/spot-FX retail venues (which widen spreads and reject orders around news).
- Budget slippage explicitly in backtests: assume fills at the *worst* quote in the entry window, not mid.
- Kill-switch on data anomalies: if the feed's release timestamp is late or values fail sanity checks (e.g., CPI MoM > |2%|), stand down — bad parses are how news bots blow up.

---

## 8. Synthesis: Bot Architecture Implied by the Evidence

| Module | Signal | Horizon | Evidence base |
|---|---|---|---|
| Calendar + consensus DB | standardized surprise per release component | event-driven | ABDV 2007; CESI methodology |
| Fed pricing monitor | FedWatch-implied probabilities vs. decision | daily / FOMC days | CME FedWatch methodology |
| Statement differ + tone scorer | net hawkish/dovish delta vs. priced path | FOMC/ECB/BoJ days | GSS 2005 (path factor = 75–90% of yield reaction); FinBERT/LLM literature |
| Nowcast aggregator | Cleveland CPI nowcast − consensus; GDPNow deltas | days pre-release | Cleveland Fed EC 2023-06 (beats consensus) |
| Drift trader (core P&L) | post-release continuation, regime-conditional sign | 15 min – weeks | NBER w25127; two-phase spike/drift evidence |
| Geopolitical classifier | fade fear-only shocks, ride supply shocks | hours – weeks | Wolfers-Zitzewitz 2016/2018; GPR literature |
| Risk regime layer | EPU/GPR innovations scale gross exposure | continuous | BBD 2016; Caldara-Iacoviello 2022 |
| Execution guard | no resting orders through releases; delayed limit entries | always | Lockup-era latency history; liquidity-evaporation evidence |

Overarching design principles from the research: (1) trade the surprise, never the level; (2) words beat numbers for central banks; (3) public nowcasts are a free prior; (4) published anomalies decay (pre-FOMC drift) — monitor everything out-of-sample; (5) the bot's structural edge is patience at the minutes-to-weeks horizon, where the latency arms race doesn't reach.

---

## Sources

**Academic papers**
- Lucca, D. & Moench, E. (2015), "The Pre-FOMC Announcement Drift," *Journal of Finance* — [NY Fed SR 512](https://www.newyorkfed.org/research/staff_reports/sr512.html), [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1923197)
- "The disappearing pre-FOMC announcement drift" (2020), *Finance Research Letters* — [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S1544612320315956)
- Andersen, Bollerslev, Diebold & Vega (2007), "Real-Time Price Discovery in Global Stock, Bond and FX Markets," *JIE* — [NBER w11312](https://www.nber.org/papers/w11312), [PDF](https://www.sas.upenn.edu/~fdiebold/papers/paper61/abdv2_062804.pdf)
- Gürkaynak, Sack & Swanson (2005), "Do Actions Speak Louder than Words?", *IJCB* — [Fed FEDS 2004-66](https://www.federalreserve.gov/econres/feds/do-actions-speak-louder-than-words-the-response-of-asset-prices-to-monetary-policy-actions-and-statements.htm)
- Brooks, Katz & Lustig (2018), "Post-FOMC Announcement Drift in U.S. Bond Markets" — [NBER w25127](https://www.nber.org/papers/w25127)
- Kurov, Sancetta, Strasser & Wolfe (2019), "Price Drift Before U.S. Macroeconomic News" — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=2778549), [ECB WP 1901](https://www.ecb.europa.eu/pub/pdf/scpwps/ecbwp1901.en.pdf)
- Baker, Bloom & Davis (2016), "Measuring Economic Policy Uncertainty," *QJE* — [NBER w21633](https://www.nber.org/papers/w21633), [PDF](https://users.ssc.wisc.edu/~mchinn/bakerbloomdavis_QJE2016.pdf), [policyuncertainty.com](https://www.policyuncertainty.com/bbd_monetary.html)
- Caldara & Iacoviello (2022), "Measuring Geopolitical Risk," *AER* — [AEA](https://www.aeaweb.org/articles?id=10.1257%2Faer.20191823), [Fed IFDP 1222](https://www.federalreserve.gov/econres/ifdp/files/ifdp1222.pdf), [GPR data](https://www.matteoiacoviello.com/gpr.htm)
- Scotti, C. (2013), "Surprise and Uncertainty Indexes" — [Fed IFDP 1093](https://www.federalreserve.gov/pubs/ifdp/2013/1093/ifdp1093.pdf)
- Wolfers & Zitzewitz (2016), "What do financial markets think of the 2016 election?" — [Brookings](https://www.brookings.edu/articles/what-do-financial-markets-think-of-the-2016-election/); (2018) "The 'Standard Error' of Event Studies" — [AEA P&P](https://www.aeaweb.org/articles?id=10.1257%2Fpandp.20181090); firm-level: [NBER w23152](https://www.nber.org/system/files/working_papers/w23152/w23152.pdf)
- Picault & Renault (2017), "Words are not all created equal" (ECB communication), *JIMF* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0261560617301808)
- Doh, Song & Yang (2020), "Deciphering Federal Reserve Communication via Text Analysis" — [KC Fed RWP 20-14](https://www.kansascityfed.org/documents/5642/rwp20-14dohsongyang.pdf)
- "Unveiling the sentiment behind central bank narratives: A novel deep learning index" (2023), *JBEF* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S2214635023000230)
- "Shifts in ECB Communication: A Textual Analysis of the Press Conferences," *IJCB* — [PDF](https://www.ijcb.org/sites/default/files/journal/v19n2/ijcb-v19n2-shifts-ecb-communication-textual-analysis-press-conference.pdf)
- "Stock price reaction to ECB communication: Introductory Statements vs. Q&A" (2022), *FRL* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1544612322007048)
- BIS IFC, "Between hawks and doves: measuring central bank communication" — [PDF](https://www.bis.org/ifc/publ/ifcb44l.pdf)
- Cleveland Fed, "Nowcasting Inflation" — [WP 24-06](https://www.clevelandfed.org/-/media/project/clevelandfedtenant/clevelandfedsite/publications/working-papers/2024/wp2406.pdf); real-time assessment — [EC 2023-06](https://www.clevelandfed.org/publications/economic-commentary/ec-202306-real-time-assessment-inflation-nowcasting-cleveland-fed)
- Kim, Spörer & Handschuh (2023)-line LLM Fedspeak work; agentic retrieval — [MDPI Mathematics 13(20):3255](https://www.mdpi.com/2227-7390/13/20/3255)

**Official / data sources**
- CME FedWatch methodology — [CME Group](https://www.cmegroup.com/articles/2023/understanding-the-cme-group-fedwatch-tool-methodology.html); tool — [link](https://www.cmegroup.com/markets/interest-rates/cme-fedwatch-tool.html)
- Atlanta Fed GDPNow — [model](https://www.atlantafed.org/cqer/research/gdpnow), [explainer](https://www.atlantafed.org/research-and-data/data/gdpnow/explainer), [FRED](https://fred.stlouisfed.org/series/GDPNOW); Market Probability Tracker — [link](https://www.atlantafed.org/research-and-data/data/market-probability-tracker)
- Cleveland Fed Inflation Nowcasting — [link](https://www.clevelandfed.org/indicators-and-data/inflation-nowcasting)
- BLS lockup changes (Mar 1, 2020 electronics ban) — [BLS](https://www.bls.gov/bls/changes-to-dol-media-lockup-effective-march-1-2020.htm); permanent discontinuation (Jun 3, 2020) — [Federal Register](https://www.federalregister.gov/documents/2020/05/27/2020-11297/announcing-discontinuation-of-the-dol-lock-up-facility-for-participating-news-media-organizations), [electronics-ban rule](https://www.federalregister.gov/documents/2020/02/07/2020-02383/announcing-elimination-of-electronic-devices-in-the-dol-lock-up-facility-for-participating-news)

**Industry / practitioner**
- Washington Post (2013), "Traders may have gotten last week's Fed news 7 milliseconds early" — [link](https://www.washingtonpost.com/news/wonk/wp/2013/09/24/traders-may-have-gotten-last-weeks-fed-news-7-milliseconds-early/)
- Morgan Stanley MNLPFEDS Fed sentiment index — [link](https://www.morganstanley.com/articles/mnlpfeds-sentiment-index-federal-reserve)
- MNI FOMC Hawk-Dove Spectrum — [link](https://www.mnimarkets.com/mni-fomc-hawk-dove-spectrum); MacroMicro AI FOMC Hawk-Dove Index — [link](https://en.macromicro.me/collections/4238/us-federal/74572/us-mm-fed-statement-hawkish-dovish-index)
- InTouch Capital Markets Hawk/Dove Cheat Sheet — [link](https://www.itcmarkets.com/hawk-dove-cheat-sheet-2/); BBVA Fed/ECB/BoE/BoJ cheat sheet — [link](https://www.bbvamarketstrategy.com/public/fx-strategy/fed-ecb-boe-boj-hawk-dove-cheat-sheet/)
- ECB blog (2023), "How words guide markets" — [link](https://www.ecb.europa.eu/press/blog/date/2023/html/ecb.blog230809~f101598a82.en.html)
- PageCrawl, FOMC statement diff monitoring — [link](https://pagecrawl.io/blog/fomc-statement-change-detection-monitoring)
- FXEmpire, news-driven FX trading (FOMC/CPI/NFP) — [link](https://www.fxempire.com/education/article/news-driven-fx-trading-how-to-trade-events-like-the-fomc-cpi-and-nfp-1549791); PriceActionNinja news-trading guide — [link](https://priceactionninja.com/forex-news-trading-guide-nfp-cpi-fomc-major-releases/)
- Benzinga, "Trading Systems and Market News" (spike vs. drift) — [link](https://www.benzinga.com/Opinion/26/06/53097073/trading-systems-and-market-news-how-to-handle-cpi-fomc-and-unexpected-events)
- FP Markets, Citi Economic Surprise Index — [link](https://www.fpmarkets.com/uk/education/trading-guides/what-is-the-citigroup-economic-surprise-index/)
- BlackRock Geopolitical Risk Dashboard — [link](https://www.blackrock.com/corporate/insights/blackrock-investment-institute/interactive-charts/geopolitical-risk-dashboard)
- FinSyn, "OPEC, Tariffs, and All-Time Highs" — [link](https://www.finsyn.com/opec-tariffs-and-all-time-highs-macro-events-and-investing/); optionstrading.org on geopolitics and options — [link](https://www.optionstrading.org/blog/how-geopolitical-events-influence-options-markets/)
- State Street, "Hawkish Words, Dovish Moves" — [link](https://www.ssga.com/us/en/institutional/insights/weekly-economic-perspectives-28-july-2025)
- B2Broker on liquidity around news — [link](https://b2broker.com/news/liquidity-examples/); PickMyTrade on futures slippage — [link](https://blog.pickmytrade.io/slippage-causes-futures-trading/); MOSS on order books/depth — [link](https://moss.sh/news/understanding-order-books-and-market-depth/)
