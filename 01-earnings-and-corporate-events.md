# Trading Scheduled Corporate News Events: Earnings & Corporate Actions
## Research for an Automated News-Trading Bot (Fundamental/News-Driven, No Technical Analysis)

*Research compiled June 2026. All figures are from academic papers and practitioner sources cited inline and in the Sources section.*

---

## 1. Overview

Scheduled corporate news events — earnings reports, guidance updates, analyst actions, index rebalances, splits, dividends, buybacks — are the bread and butter of **event-driven** and **quantitative equity** desks. The unifying theme across five decades of academic literature is **systematic underreaction**: markets incorporate the *direction* of news quickly but the *full magnitude* slowly, creating predictable drift that lasts days to months. A second theme is **mechanical flow**: index funds *must* buy/sell on rebalance dates regardless of price, creating exploitable supply/demand imbalances.

Key structural facts for a bot designer:

- **The big edges are post-announcement, not pre-announcement.** Predicting a surprise before it happens is extremely hard and largely the domain of alternative-data funds. Reacting *correctly and fast* to a published surprise — and then riding the drift — is where the documented, replicable alpha lives.
- **Most documented anomalies have decayed but not died.** PEAD spreads of 8-9%/quarter in the 1980s are now low-single-digit percent per quarter, concentrated in small/illiquid names. The S&P 500 index effect went to ~zero in the 2010s, then partially returned post-2020 with the retail trading revival.
- **Edges survive where arbitrage is costly**: small caps, high-transaction-cost stocks, low institutional ownership, low analyst coverage.
- **Binary-event risk must be sized via the options-implied expected move**, not historical daily volatility.

The sections below cover each technique: what it is, why it works, how professionals execute it, hard numbers, bot implementation notes, and pitfalls.

---

## 2. Post-Earnings Announcement Drift (PEAD)

### What it is
After an earnings announcement, stock prices continue to drift **in the direction of the earnings surprise** for weeks to months. First documented by Ball & Brown (1968); nailed down by **Bernard & Thomas (1989, 1990)**, who showed the spread between the top and bottom deciles of standardized unexpected earnings (SUE) was positive in **41 of 48 quarters** in their sample, and that zero-investment SUE-decile portfolios earned abnormal returns of roughly **8-9% per quarter (~35% annualized)** in their era ([Wikipedia PEAD summary](https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift); [Bernard & Thomas, Semantic Scholar](https://www.semanticscholar.org/paper/POST-EARNINGS-ANNOUNCEMENT-DRIFT-DELAYED-PRICE-OR-Bernard-Thomas/01354e373f23983ac962c8b133e668332ec26da9)). Fama famously called PEAD "the granddaddy of underreaction events."

### SUE calculation (the core signal)
Two standard variants ([Breaking Down Finance](https://breakingdownfinance.com/trading-strategies/standardized-unexpected-earnings-sue/); [QuantConnect research](https://www.quantconnect.com/research/15369/standardized-unexpected-earnings/)):

**Analyst-based (modern, preferred):**
```
SUE = (Actual EPS − Consensus EPS estimate) / σ
```
where σ is either (a) the standard deviation of analyst forecasts (dispersion), or (b) the standard deviation of *past forecast errors* for that stock — variant (b) better adjusts for chronically hard-to-forecast firms.

**Time-series (Bernard-Thomas original, no analyst data needed):**
```
Expected EPS = EPS from same quarter last year + drift term (seasonal random walk with drift)
SUE = (Actual EPS − Expected EPS) / σ(past surprises, typically trailing 8 quarters)
```

Interpretation: SUE is a z-score. |SUE| > 1 means the surprise exceeded one standard deviation of typical surprise noise. Quants sort the universe into deciles (or quintiles) each quarter and trade the extremes.

### Why it works (and persists)
- **Investor underreaction / anchoring**: investors fail to appreciate that earnings surprises are *serially correlated* — a positive surprise this quarter predicts another next quarter. Bernard & Thomas showed the drift partly concentrates around the *next* earnings announcement, consistent with naive seasonal-random-walk expectations.
- **Limits to arbitrage**: drift is strongest in low-liquidity, high-transaction-cost, low-institutional-ownership stocks — exactly where it's expensive to arbitrage away ([ScienceDirect review of PEAD](https://www.sciencedirect.com/science/article/pii/S2214635020303750)).
- **Slow information diffusion**: analysts revise estimates sluggishly after a surprise, dribbling the news into prices over weeks.

### How quants exploit it today
The canonical Quantpedia/academic implementation ([Quantpedia PEAD strategy page](https://quantpedia.com/strategies/post-earnings-announcement-effect), based on Brandt, Kishore, Santa-Clara & Venkatachalam, *"Earnings Announcements are Full of Surprises"*):

- **Universe**: NYSE/AMEX/NASDAQ ex-financials/utilities, price > $5 (~1,000 names traded).
- **Signal**: intersection of top quintile **SUE** and top quintile **EAR** (Earnings Announcement Return — the 3-day market reaction itself, used as a "the market's own read" confirming signal). Long top/top, short bottom/bottom.
- **Entry**: second trading day after the announcement (avoids the chaotic first session and look-ahead bias).
- **Holding period**: one quarter (~60 trading days), rebalanced quarterly.
- **Backtest (1987-2004)**: ~**15% annualized**, max drawdown **-11.2%**. EAR alone: ~7.55%/yr; SUE+EAR combined: ~**12.5%/yr abnormal**.
- Important practitioner note: **"most of the returns come from the long side"** — the strategy works long-only, which slashes borrow costs and short-squeeze risk.

Modern refinements:
- **NLP overlay**: Quantpedia's NLP-enhanced PEAD ([How to improve PEAD with NLP](https://quantpedia.com/how-to-improve-post-earnings-announcement-drift-with-nlp-analysis/)) combining earnings-call text with SUE: ~5.89% CAR, Sharpe 0.76, max DD -11.81% with a 4-week hold (lower headline return but cleaner risk profile in the modern, more efficient era).
- **Conference-call tone** as an *incremental* drift predictor — see Section 3.
- A 2024 study found a simple revised earnings-surprise measure moderated by **investor attention** still yields a "tractable and profitable" strategy ([ScienceDirect 2024](https://www.sciencedirect.com/science/article/abs/pii/S1057521924003922)).

### Evidence / numbers
| Source | Period | Alpha estimate |
|---|---|---|
| Bernard & Thomas (1989/1990) | 1974-1986 | ~8-9%/quarter long-short decile spread |
| Sadka (2006) | — | ~8.76%/yr (after liquidity-risk adjustment) |
| Battalio & Mendenhall (2007) | — | up to ~43%/yr (gross, extreme deciles) |
| Brandt et al. (SUE+EAR) | 1987-2004 | ~12.5%/yr abnormal; 15% total annualized |
| Academic consensus, 60-day window | various | ~6% abnormal return over 60 days post-announcement |
| Recent US large-cap estimates | 2010s+ | low single digits/yr; concentrated in small caps |

(Sources: [Wikipedia](https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift), [Quantpedia 50 Years in PEAD Research](https://quantpedia.com/50-years-in-pead-research/), [Quantpedia strategy page](https://quantpedia.com/strategies/post-earnings-announcement-effect).)

### Current state of the edge (decay debate)
- "Rest in Peace Post-Earnings Announcement Drift" (Martineau, 2021) argued price discovery of earnings news has become nearly immediate in US large caps — drift is dead there ([ResearchGate](https://www.researchgate.net/publication/362596818_Rest_in_Peace_Post-Earnings_Announcement_Drift)).
- But two 2025 papers claim **PEAD is alive and well**; UCLA Anderson's Subrahmanyam has a working paper reconciling the contradiction — the answer largely hinges on universe (small vs. large cap), surprise measure, and sample period ([UCLA Anderson Review](https://anderson-review.ucla.edu/is-post-earnings-announcement-drift-a-thing-again/)).
- CFA Institute (2025) flags that **generative AI accelerating information dissemination may further compress the drift window** ([CFA Institute blog](https://blogs.cfainstitute.org/investor/2025/04/22/can-generative-ai-disrupt-post-earnings-announcement-drift-pead/)).
- Practical takeaway: assume the drift is real but small in liquid names (maybe 1-3% per quarter long-short), larger in small caps where your bot's size won't move the market — a structural advantage for small accounts.

### Bot implementation
1. Nightly: pull earnings calendar (e.g., from a data API); for each reporter compute SUE using consensus EPS and trailing-8-quarter surprise std dev.
2. Compute EAR = stock's 3-day return around the announcement minus market/sector return.
3. T+2 entry: long names in top SUE *and* top EAR quintile (drop bottom-quintile shorts initially — long side carries most alpha and avoids borrow issues).
4. Hold 20-60 trading days; exit earlier if the *next* catalyst (next earnings) is imminent or the position hits a -2σ adverse move.
5. Equal-weight, cap any single name at 2-5% of book; sector-neutralize if possible.

### Risks / pitfalls
- **Decayed edge in large caps** — backtest on recent data (post-2015), not the classic samples.
- **Revisions to "consensus"**: vendor consensus numbers get restated; use point-in-time estimates only (look-ahead bias is the #1 killer of PEAD backtests).
- **GAAP vs. non-GAAP EPS mismatch** between actuals and estimates — must compare like-for-like (vendors' "street" EPS).
- **Earnings management**: firms that barely beat via accruals show weaker/reversing drift.
- Quantpedia also documents a **"Reversal in PEAD"** variant — in some sub-samples extreme drift eventually reverses ([Quantpedia reversal page](https://quantpedia.com/strategies/reversal-in-post-earnings-announcement-drift)); don't hold past ~1 quarter.

---

## 3. Trading the Earnings Announcement Itself

### 3.1 The announcement reaction (event window)

The first minutes after release are dominated by machines parsing the press release. Research findings relevant to bot design:

- **Price discovery is ~complete within 5 minutes** of release in liquid names. One study found average returns of trading positions rise from **0.74% (closed after 30 seconds) to 1.58% (closed after 5 minutes)** — i.e., the move builds over the first minutes, but trying to capture it at the best bid/offer materially reduces realized returns ([arXiv: Warp speed price moves — jumps after earnings announcements](https://arxiv.org/pdf/2601.08962)).
- **After-hours liquidity is thin and price impact is magnified**: announcements outside regular hours have significantly larger price impact, driven by low volume rather than fundamentals; institutional participation fades after ~6:30pm ET, leaving retail ([MDPI study on pre/post-market announcements](https://www.mdpi.com/1911-8074/18/2/75); [ScienceDirect on overnight announcements and preopening price discovery](https://www.sciencedirect.com/science/article/abs/pii/S0922142524000124)).
- Retail flow (Robinhood-era) actually *helps* impound earnings news ([UCLA Anderson, Retail Investor Trading and Market Reactions to Earnings Announcements](https://anderson-review.ucla.edu/wp-content/uploads/2023/01/RetailTrading-20221126.pdf)).

**Practical implication**: a retail-grade bot will not win the millisecond race. The realistic plays are (a) trade the *residual* drift starting T+1/T+2 (PEAD), or (b) trade within the first 1-5 minutes only if the surprise is unambiguous and large, accepting wide after-hours spreads, or (c) trade the *open* the next morning when liquidity returns and there is still measurable continuation on big surprises.

### 3.2 Whisper numbers vs. consensus

The "whisper number" is the market's *true* expectation, often above the stale published consensus (companies guide analysts low to engineer beats).

- Stocks beating the **Earnings Whisper number** closed higher by an average **+1.8%** and were up **60%** of the time; stocks that beat consensus but **missed the whisper** closed **lower 55% of the time, average -0.3%** ([EarningsWhispers methodology page](https://www.earningswhispers.com/about-whispers)).
- A Bloomberg study: whisper numbers missed actuals by 21% vs. 44% for published consensus; a Michigan/Indiana/Purdue academic study confirmed whispers were better predictors than consensus ([Wikipedia: Whisper number](https://en.wikipedia.org/wiki/Whisper_number)). EarningsWhispers claims its number is closer to actual than consensus ~70% of the time.
- A 2002 report: stocks beating the whisper gained **>2% on average in one day** ([WallStreetMojo](https://www.wallstreetmojo.com/whisper-number/)).
- Caveat: Regulation FD (2000) weakened classic "whisper" channels; modern whisper proxies = buy-side bogeys, options-implied skew, and crowd-sourced estimates (Estimize-style). The "stocks fall on a headline beat" phenomenon is almost always a whisper miss or weak guidance ([HeyGoTrade explainer](https://www.heygotrade.com/en/blog/whisper-numbers-vs-consensus-why-stocks-drop-on-beats/)).

**Bot angle**: judge the surprise against the *whisper/buy-side* expectation, not just consensus. A consensus beat + whisper miss is a short/avoid signal; consensus beat + whisper beat + raised guidance ("beat and raise") is the strongest long pattern.

### 3.3 Management guidance changes

Guidance often matters more than the reported quarter. Quantified evidence:

- **Negative guidance surprises**: ~**-5.9% abnormal return** around the forecast, followed by a **+1.9% correction** over the following ~2 months (initial overreaction). Positive surprises: **+1.9%** initial, **-1.7%** subsequent correction ([Das et al., "Management Earnings Forecasts and Subsequent Price Formation"](https://web-docs.stern.nyu.edu/salomon/docs/conferences/das%20et%20al.pdf)).
- Asymmetry is large: mean CAR around conservative/bad-news forecasts **-11.5%** vs. **+1.4%** for optimistic ones ([HBS, "When is Managers' Earnings Guidance Most Influential?"](https://www.hbs.edu/ris/Publication%20Files/00-042_4b511b76-c732-4afb-9bd1-e675530be523.pdf)).
- The **"beat and raise"** pattern (beat the quarter + raise full-year guidance) is the practitioner's canonical bullish event; serial beat-and-raise companies exhibit continuation across quarters ([HeyGoTrade beat-and-raise guide](https://www.heygotrade.com/en/blog/How-To-Trade-Beat-And-Raise-Cycle-Earnings-Whispers/)).

**Bot angle**: parse the press release / 8-K for guidance changes (new midpoint vs. prior midpoint vs. consensus for next Q and FY). A guidance *cut* is the single most reliable bearish trigger; note the documented tendency to overreact then partially mean-revert — i.e., short the announcement reaction fast or not at all; don't chase a -15% gap two days later.

### 3.4 Conference call tone (NLP)

- Conference-call tone (Loughran-McDonald financial dictionary word counts; now FinBERT/LLMs) has **highly significant explanatory power for both the initial reaction and the subsequent drift**, and tone predicts drift-window CARs *better than the numeric surprise itself* (Price, Doran, Peterson & Bliss 2012, *Journal of Banking & Finance*: ["Earnings conference calls and stock returns: The incremental informativeness of textual tone"](https://www.sciencedirect.com/science/article/abs/pii/S0378426611002901)).
- A 2024 NLP study: sentiment from call transcripts predicted post-earnings direction with **60-65% accuracy**; firms using more uncertain/negative language underperformed by **1.5-3% over the next quarter** (per search synthesis of [recent NLP literature](https://www.researchgate.net/publication/257211915_Earnings_Conference_Calls_and_Stock_Returns_The_Incremental_Informativeness_of_Textual_Tone)).
- Earnings-specific custom dictionaries beat generic sentiment lexicons substantially. Audio features (manager vocal tone) add further signal ([UT Dallas working paper on textual and audio disclosures](https://accounting.utdallas.edu/files/2023/02/PhD-U-of-Houston-Ozer_Erdem_AudioFeatures5.pdf)).
- Negative call tone also predicts elevated **crash risk** the following year ([Journal of Business Ethics](https://link.springer.com/article/10.1007/s10551-019-04326-1)).

**Bot angle**: this is highly automatable with an LLM: pull the transcript (available within hours), score management tone + Q&A evasiveness, and use the score to (a) filter PEAD longs (require non-negative tone) and (b) flag "beat with nervous call" shorts. Focus on the **Q&A section** — it's less scripted than prepared remarks.

### 3.5 The earnings announcement premium (pre-positioning)

- Stocks earn abnormally high returns **during their announcement months/weeks**: Frazzini & Lamont (2007) estimate the premium at **>7%/yr**; strategies harvesting it earned **7-18% annualized** with a value-weighted Sharpe of **0.94** (equal-weighted **2.38**) vs. ~0.35 for the market (Savor & Wilson; [Barber et al., "The Earnings Announcement Premium Around the Globe"](https://www.sciencedirect.com/science/article/abs/pii/S0304405X12002188); [Alpha Architect summary](https://alphaarchitect.com/introducing-the-global-earnings-announcement-premium/)).
- **However**: Heitz et al. (2020) document the premium **has largely disappeared in the US** in recent years, attributed to more frequent 8-K disclosure after 2004 ([The Disappearing Earnings Announcement Premium](https://www-2.rotman.utoronto.ca/userfiles/seminars/files/G_%20Narayanamoorthy%20paper.pdf)). Treat pre-announcement long positioning as a weak, decayed edge — useful as a tilt, not a standalone strategy.

---

## 4. Analyst Revision Strategies

### What it is
Trade in the direction of analyst actions: earnings-estimate revisions, recommendation upgrades/downgrades, and price-target changes. Prices **underreact to revisions** just as they underreact to earnings.

### Evidence / numbers
- **Womack (1996, Journal of Finance)**: post-recommendation drift — upgrades earn **+2.4%** abnormal over the following ~1 month, downgrades **-9.1% over six months**. The asymmetry (downgrades matter more, last longer) is robust ([summarized in UCLA Anderson working paper](https://www.anderson.ucla.edu/documents/areas/fac/accounting/trueman_ratings.pdf)).
- Early access to recommendation changes generates **two-day returns of 1.02% (upgrades) / 1.50% (downgrades) after transaction costs** for institutional clients — i.e., much of the immediate move goes to whoever sees the note first ([same literature](https://www.bayes.citystgeorges.ac.uk/__data/assets/pdf_file/0009/681939/Flake_20220318.pdf)).
- **Estimate-revision momentum**: prices drift in the direction of consensus-estimate revisions for 1-6 months; the effect is *stronger in low-dispersion stocks* (when analysts agree, the revision is more informative) — Dische (2002) ([SSRN](https://papers.ssrn.com/sol3/Delivery.cfm/SSRN_ID270036_code010523600.pdf?abstractid=270036&mirid=1)); see also the role of analyst forecasts in momentum ([ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1057521916301314)).
- Bradshaw et al.: recommendation *changes* (not levels) are what carry information; drift follows revisions, with reversals when the revision contradicts the prior price move ([Bayes Business School working paper](https://www.bayes.citystgeorges.ac.uk/__data/assets/pdf_file/0009/681939/Flake_20220318.pdf); [Trueman et al., "Profiting from Analyst Recommendations: Levels versus Changes"](https://www.anderson.ucla.edu/documents/areas/fac/accounting/trueman_ratings.pdf)).

### How pros execute
- Classic quant factor: **3-month estimate-revision breadth** = (# up revisions − # down revisions) / total analysts, or % change in consensus FY1+FY2 EPS. Ranked monthly, long top decile / short bottom decile. This is a staple in multi-factor institutional models (often labeled "Earnings Revisions" or "Analyst Sentiment").
- Event desks trade **the morning of** a bulge-bracket upgrade/downgrade, fading or following depending on whether the move is already crowded; the durable edge for slower players is the **multi-week drift after downgrades**.
- Combine with PEAD: a positive earnings surprise *followed by* a wave of up-revisions is the highest-conviction continuation setup (the revision wave is literally the mechanism by which PEAD resolves).

### Bot implementation
1. Daily: ingest estimate and recommendation changes (vendor feed or scraping aggregators).
2. Score each stock: revision breadth, magnitude of consensus change, recency-weighted; bonus weight for revisions within 5 days after an earnings report.
3. Enter on day of signal or next open; hold 4-12 weeks; downgades are stronger short signals than upgrades are longs (Womack asymmetry).
4. Filter by dispersion: prefer low-dispersion names for revision momentum.

### Risks / pitfalls
- The first-day move is captured by clients with early access; entering at next open you only get the drift remainder.
- Analyst actions cluster after the price has already moved (analysts chase price) — control for the prior return or you're just buying price momentum (which you're excluding by mandate).
- Recommendation *levels* are useless (analysts are perennially bullish); only *changes* matter.
- Short side requires borrow; downgraded stocks often have high borrow fees.

---

## 5. Index Rebalancing, Splits, Dividends, Buybacks

### 5.1 S&P 500 / Russell index effect

**What it is**: ~$11T+ tracks S&P indices and ~$11T tracks Russell. When a stock is *announced* as an addition, index funds must buy it by the effective date; deletions must be sold. Historically the announcement caused an immediate pop (additions) or dump (deletions), plus further drift into the effective date.

**Numbers**:
- Additions 1990-2002: **+8.8% abnormal** from announcement to effective ([NY Fed staff report, "Is There an S&P 500 Index Effect?"](https://www.newyorkfed.org/medialibrary/media/research/staff_reports/sr484.pdf)).
- Deletions: **-4.6%** (1980s) → **-16.1%** (1990s) → **-12.4%** (2000s) → **-0.6%** (2010s — gone).
- **Greenwood & Sammon, "The Disappearing Index Effect" (NBER WP 30748, 2022/2023; later Journal of Finance)**: by the 2010s the average addition/deletion abnormal return was ~zero — killed by pre-positioning arbitrageurs, migrations from the MidCap 400 (already partly held), and deeper liquidity provision ([NBER paper](https://www.nber.org/system/files/working_papers/w30748/w30748.pdf); [HBS version](https://www.hbs.edu/ris/download.aspx?name=ssrn-4294297.pdf)).
- **Post-2020 partial comeback**: with the retail-trading revival, 2025 S&P 500 additions outperformed the equal-weight index by an average of **+7.4 points on announcement day** ([ETF Trends](https://www.etftrends.com/retail-revival-fuels-comeback-sp-500-index-inclusion-effect/)). The pop now happens *at the announcement* (within minutes), not into the effective date — the drift between announcement and effective date is what disappeared.
- **Russell reconstitution** (annual June): additions to the Russell 1000 historically earned **+10.9% cumulative** from late May to June 30; Russell 2000 Growth deletions lost **-6.6%** (1996-2001 sample). Hedge funds front-run predicted adds/deletes months ahead since Russell's rules are mechanical (rank by market cap on rank day) — the modern edge is in **predicting the adds/deletes before the preliminary lists are published**, and in providing liquidity to index funds on reconstitution day ([Russell Reconstitution Effect, ResearchGate](https://www.researchgate.net/publication/228259894_The_Russell_Reconstitution_Effect); [CME Group on the 2026 reconstitution — now semi-annual](https://www.cmegroup.com/articles/2026/the-2026-russell-reconstitution.html)).

**Bot angle**: (a) On an S&P inclusion announcement (usually ~5:15pm ET), buy in after-hours immediately — the day-1 pop is real again but you must be within minutes; (b) For Russell, replicate the published methodology to forecast adds/deletes from market-cap ranks ahead of rank day and position weeks before; exit into the reconstitution-day volume. Pitfall: S&P additions are committee-discretionary (not fully rule-based), so *predicting* them is probabilistic; trade the announcement, not the prediction, unless you build a proper candidacy model (eligible market cap, profitability rule, float).

### 5.2 Stock splits

- Split announcements carry **~+3.4% announcement abnormal return**, and classic studies found **post-split announcement drift**: Ikenberry, Rankine & Stice (1996) and Desai & Jain (1997) document ~**+7.9% excess in year 1, +12.2% over 3 years** post-split ([Ikenberry & Ramnath underreaction paper](http://www.econ.yale.edu/~shiller/behfin/2000-05/ikenberry.pdf); [ScienceDirect, information content of stock splits](https://www.sciencedirect.com/science/article/abs/pii/S0378426611000616)).
- **Modern caveat**: recent re-examinations find the drift concentrates in the **first 2-3 months** and the long-horizon drift has faded ([ScienceDirect, earnings management and post-split drift](https://www.sciencedirect.com/science/article/abs/pii/S0378426619300305)). Splits are a *signal of management confidence*, not a cash-flow event; with fractional shares the retail-affordability channel is weaker.
- **Bot angle**: small long tilt for 1-3 months after a split announcement, best treated as a confirming feature alongside earnings strength rather than a standalone trade.

### 5.3 Dividend initiations and omissions/cuts

**Michaely, Thaler & Womack (1995, Journal of Finance)** — the definitive study ([SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=420313); [NBER WP 4778](https://www.nber.org/papers/w4778)):
- 3-day announcement reaction: initiations **+3.4%**, omissions **-7.0%**.
- **Drift over the next 12 months**: initiations **+7.5%**, omissions **-11.0%** market-adjusted — the market underreacts *again*.
- The omission drift is **more pronounced than PEAD** and distinct from it.
- A long-initiations / short-omissions rule was profitable in **22 of 25 years**.

**Bot angle**: dividend omissions/cuts are rarer than earnings but the per-event edge is large and the direction unambiguous. Implementation: monitor dividend declarations vs. prior-quarter dividend; on a cut/omission, short (or avoid/sell) for 3-12 months; on an initiation, long for up to 12 months. Pitfall: omissions cluster in distressed sectors and recessions — hedge sector exposure or the short book becomes one big credit bet.

### 5.4 Buyback announcements

**Ikenberry, Lakonishok & Vermaelen (1995, Journal of Financial Economics)** — "Market Underreaction to Open Market Share Repurchases" ([SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=226564); [NBER WP 4965](https://www.nber.org/papers/w4965)):
- Average abnormal **4-year buy-and-hold return: +12.1%** after open-market repurchase announcements (1980-1990).
- For **value stocks** (high B/M, where undervaluation is the plausible motive): **+45.3%** abnormal over 4 years. For glamour stocks: **no drift**.
- Follow-ups (Peyer & Vermaelen 2009; Manconi, Peyer & Vermaelen on global data) confirm persistence, strongest for beaten-down small value names ([Manconi-Peyer-Vermaelen](https://www.shareholderforum.com/wag/Library/20140800_Manconi-Peyer-Vermaelen.pdf)).

**Bot angle**: long stocks announcing *new or upsized* open-market buybacks **where the stock has recently underperformed and trades at a low multiple**; ignore routine refreshes of existing programs and announcements by expensive glamour names. Hold months, not days — this is the slowest of all the event edges. Pitfall: announced ≠ executed (many programs are never completed); weight by authorization size as % of market cap (>5% is meaningful).

---

## 6. How Professionals Structure These Trades

### Pre-announcement vs. post-announcement
- **Pre-announcement positioning** (betting on the outcome) is reserved for funds with genuine informational edge: alternative data (credit-card panels, web traffic, app downloads), channel checks, whisper aggregation. Without that, holding through the print is *paying* the event risk, not harvesting it. The earnings-announcement premium that once compensated holders has largely disappeared in the US ([Disappearing Earnings Announcement Premium](https://www-2.rotman.utoronto.ca/userfiles/seminars/files/G_%20Narayanamoorthy%20paper.pdf)).
- **Post-announcement reaction trading** is where systematic funds operate: the surprise is public, the question is only whether the market has fully priced it. PEAD, revision momentum, guidance drift, and dividend/buyback drift are all post-announcement strategies. This should be the bot's home turf.

### The options-implied expected move (the professional yardstick)
- **Expected move ≈ price of the front-expiry ATM straddle** (roughly ±1σ, ~68% confidence band) ([Options AI expected move tool](https://tools.optionsai.com/expected-move); [Option Alpha IV crush explainer](https://optionalpha.com/learn/iv-crush)).
- Pre-earnings, ATM straddles run **8-12% of stock price vs. 2-4% normally**; IV peaks the day before the print and **collapses 30-50% within hours** of the announcement ("IV crush") ([MenthorQ guide](https://menthorq.com/guide/iv-crush-understanding-the-earnings-driven-volatility-spike-and-how-to-capitalize-on-it/); [SpotGamma](https://support.spotgamma.com/hc/en-us/articles/15249330755859-IV-Crush-Explained-What-It-Is-When-It-Happens-and-How-to-Trade-It)).
- Empirics: implied moves **overestimate** realized moves by ~15-20% on average; the stock stays inside the implied range **60-75%** of the time in large caps — a structural premium to vol *sellers*, but with fat-tailed loss risk ([ApexVol earnings trading guide](https://apexvol.com/learn/earnings-trading-guide); [TradingRiot vol-around-earnings](https://blog.tradingriot.com/p/volatility-trading-around-earnings)).
- The best predictor of earnings straddle P&L is the **gap between the current implied move and the stock's historical realized earnings moves** — sell vol when implied >> historical average move, buy when implied is unusually cheap.
- Even for a pure stock bot, the implied move is the right **risk unit**: a "2% surprise" means nothing in NVDA (implied move 8%) and everything in KO (implied move 2%). Normalize every reaction by the implied move: `reaction_score = actual_move / implied_move`. A stock that beat and moved only +0.3× its implied move has "underreacted" relative to expectations — a useful drift filter.

### Risk sizing around binary events
- Earnings are **gap risk**: stops don't protect you through an overnight print. Size so the *implied move* (and 2× the implied move) is survivable: if implied move is 8% and you risk max 50bp of NAV per event, position ≤ 6.25% NAV/2 ≈ ~3% (for a 2-sigma scenario).
- Professionals use **fractional Kelly** for repeated binary bets: full Kelly is fragile to edge-estimation error, so practitioners cap at **half-Kelly or less**, scaling down further when the edge estimate is uncertain ([Kelly criterion — Wikipedia](https://en.wikipedia.org/wiki/Kelly_criterion); [Alpha Theory on Kelly in practice](https://www.alphatheory.com/blog/kelly-criterion-in-practice-1); [Nick Yoder, Kelly Criterion](https://nickyoder.com/kelly-criterion/)). The common failure mode is not being wrong about direction — it's **betting too big when right about a noisy edge**.
- Portfolio construction for event books: many small uncorrelated event bets (PEAD positions across ~50-200 names) rather than a few concentrated ones; cap per-name risk at 1-2% of book; cap same-day earnings-event exposure (50 names reporting tonight is one correlated macro bet on "earnings season sentiment").
- Diversify across event *types* (PEAD + revisions + dividends + buybacks) — they share the underreaction mechanism but trigger at different times with different horizons.

---

## 7. Synthesis: Architecture for a News-Trading Bot

Ranked by (edge size × reliability × implementability for a small automated account):

| Priority | Strategy | Trigger | Direction | Hold | Expected gross edge (modern) |
|---|---|---|---|---|---|
| 1 | PEAD (SUE + EAR, long-biased) | T+2 after earnings | With surprise | 20-60 trading days | ~1-6% per event-quarter, higher in small caps |
| 2 | Guidance-cut avoidance/short | Guidance below consensus | Against | days-weeks (beware overreaction snap-back) | -5.9% event move; drift if cut is large |
| 3 | Analyst revision momentum | Post-earnings revision wave; downgrades | With revisions | 1-3 months | downgrade drift ~-9%/6mo (Womack-era); smaller now |
| 4 | Dividend omission short / initiation long | Dividend declaration change | With change | 3-12 months | ±7-11%/yr (classic est.) |
| 5 | Buyback long (value filter) | New/upsized authorization >5% mkt cap, cheap stock | Long | 6-12+ months | classic +12% / 4yr, +45% value subset |
| 6 | S&P 500 inclusion pop | Index announcement (after-hours) | Long adds | hours-days | ~+7% announcement-day (2025 data); speed-critical |
| 7 | Russell recon prediction | Rank-day forecasts | With predicted add/delete | weeks | crowded; rules-based forecastable |
| 8 | Split-announcement tilt | Split announcement | Long | 1-3 months | small; confirming feature only |

Cross-cutting bot requirements:
- **Point-in-time data** (estimates, actuals, announcement timestamps) — survivorship and restatement bias destroy event backtests.
- **Normalize all reactions by the options-implied expected move.**
- **LLM transcript scoring** (tone, guidance language, Q&A evasiveness) as a filter on every earnings trade — the academically strongest "soft" overlay.
- **Fractional-Kelly sizing**, hard per-event and per-day risk caps, no stops relied upon through prints.
- Expect realistic net performance for a modern multi-event book in the **mid-single to low-double digit annualized** range with Sharpe ~0.5-1.0, not the 35% of the Bernard-Thomas era.

---

## 8. Sources

**PEAD / earnings surprise**
- Ball & Brown (1968), "An Empirical Evaluation of Accounting Income Numbers," *Journal of Accounting Research* — via [Wikipedia: Post-earnings-announcement drift](https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift)
- Bernard & Thomas (1989), "Post-Earnings-Announcement Drift: Delayed Price Response or Risk Premium?" *JAR*; (1990) *JAE* — [Semantic Scholar](https://www.semanticscholar.org/paper/POST-EARNINGS-ANNOUNCEMENT-DRIFT-DELAYED-PRICE-OR-Bernard-Thomas/01354e373f23983ac962c8b133e668332ec26da9)
- Fink (2021), "A review of the Post-Earnings-Announcement Drift" — [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S2214635020303750)
- Brandt, Kishore, Santa-Clara & Venkatachalam, "Earnings Announcements are Full of Surprises" — via [Quantpedia PEAD strategy](https://quantpedia.com/strategies/post-earnings-announcement-effect)
- [Quantpedia: 50 Years in PEAD Research](https://quantpedia.com/50-years-in-pead-research/); [Quantpedia: PEAD with NLP](https://quantpedia.com/how-to-improve-post-earnings-announcement-drift-with-nlp-analysis/); [Quantpedia: Reversal in PEAD](https://quantpedia.com/strategies/reversal-in-post-earnings-announcement-drift)
- Martineau (2021), "Rest in Peace Post-Earnings Announcement Drift" — [ResearchGate](https://www.researchgate.net/publication/362596818_Rest_in_Peace_Post-Earnings_Announcement_Drift)
- [UCLA Anderson Review: Is PEAD a Thing? Again?](https://anderson-review.ucla.edu/is-post-earnings-announcement-drift-a-thing-again/) (Subrahmanyam working paper)
- [CFA Institute: Can Generative AI Disrupt PEAD? (2025)](https://blogs.cfainstitute.org/investor/2025/04/22/can-generative-ai-disrupt-post-earnings-announcement-drift-pead/)
- Earnings surprise measure + attention (2024) — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1057521924003922)
- SUE definitions: [Breaking Down Finance](https://breakingdownfinance.com/trading-strategies/standardized-unexpected-earnings-sue/), [QuantConnect](https://www.quantconnect.com/research/15369/standardized-unexpected-earnings/), [Stockopedia](https://www.stockopedia.com/ratios/scaled-earnings-surprise-latest-5375/), [AAII](https://www.aaii.com/journal/article/17795-how-to-analyze-earnings-surprises)
- Philadelphia Fed WP 21-07, "PEAD.txt: Post-Earnings-Announcement Drift Using Text" — [PDF](https://www.philadelphiafed.org/-/media/frbp/assets/working-papers/2021/wp21-07.pdf)

**Announcement reaction / whispers / guidance / calls**
- [Wikipedia: Whisper number](https://en.wikipedia.org/wiki/Whisper_number); [EarningsWhispers methodology](https://www.earningswhispers.com/about-whispers); [WallStreetMojo](https://www.wallstreetmojo.com/whisper-number/); [HeyGoTrade: whisper vs consensus](https://www.heygotrade.com/en/blog/whisper-numbers-vs-consensus-why-stocks-drop-on-beats/) and [beat-and-raise](https://www.heygotrade.com/en/blog/How-To-Trade-Beat-And-Raise-Cycle-Earnings-Whispers/)
- Bagnoli, Beneish & Watts — whisper forecasts accuracy; Reg FD effects — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0882611005210076)
- Das, Kim & Patro, "Management Earnings Forecasts and Subsequent Price Formation" — [NYU Stern PDF](https://web-docs.stern.nyu.edu/salomon/docs/conferences/das%20et%20al.pdf)
- HBS, "When is Managers' Earnings Guidance Most Influential?" — [PDF](https://www.hbs.edu/ris/Publication%20Files/00-042_4b511b76-c732-4afb-9bd1-e675530be523.pdf)
- Price, Doran, Peterson & Bliss (2012), "Earnings conference calls and stock returns: The incremental informativeness of textual tone," *JBF* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0378426611002901)
- Loughran & McDonald (2011) financial sentiment dictionary — applications via [ResearchGate](https://www.researchgate.net/publication/257211915_Earnings_Conference_Calls_and_Stock_Returns_The_Incremental_Informativeness_of_Textual_Tone) and [FinBERT evaluation](http://arno.uvt.nl/show.cgi?fid=157347)
- Call tone and crash risk — [Springer, Journal of Business Ethics](https://link.springer.com/article/10.1007/s10551-019-04326-1)
- Audio features in earnings calls — [UT Dallas PDF](https://accounting.utdallas.edu/files/2023/02/PhD-U-of-Houston-Ozer_Erdem_AudioFeatures5.pdf)
- Jumps after earnings announcements (speed of price discovery) — [arXiv](https://arxiv.org/pdf/2601.08962)
- Pre/post-market announcement price impact — [MDPI](https://www.mdpi.com/1911-8074/18/2/75); overnight announcements & preopening price discovery — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0922142524000124)
- Retail trading and earnings reactions — [UCLA Anderson PDF](https://anderson-review.ucla.edu/wp-content/uploads/2023/01/RetailTrading-20221126.pdf)

**Earnings announcement premium**
- Frazzini & Lamont (2007); Savor & Wilson (2011/2016) — [Wharton draft](https://faculty.wharton.upenn.edu/wp-content/uploads/2012/04/Draft20111215p_edited.pdf)
- Barber, De George, Lehavy & Trueman, "The Earnings Announcement Premium Around the Globe," *JFE* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0304405X12002188); [Alpha Architect summary](https://alphaarchitect.com/introducing-the-global-earnings-announcement-premium/)
- Heitz, Narayanamoorthy et al., "The Disappearing Earnings Announcement Premium" — [Rotman PDF](https://www-2.rotman.utoronto.ca/userfiles/seminars/files/G_%20Narayanamoorthy%20paper.pdf)
- [Quantpedia: Earnings Announcement Premium](https://quantpedia.com/strategies/earnings-announcement-premium)

**Analyst revisions**
- Womack (1996), "Do Brokerage Analysts' Recommendations Have Investment Value?" *JF* — via [Trueman et al., UCLA PDF](https://www.anderson.ucla.edu/documents/areas/fac/accounting/trueman_ratings.pdf)
- Bradshaw et al., "Predictability of Analyst Stock Recommendation Revisions" — [Bayes PDF](https://www.bayes.citystgeorges.ac.uk/__data/assets/pdf_file/0009/681939/Flake_20220318.pdf)
- Dische (2002), "Dispersion in Analyst Forecasts and the Profitability of Earnings Momentum Strategies" — [SSRN](https://papers.ssrn.com/sol3/Delivery.cfm/SSRN_ID270036_code010523600.pdf?abstractid=270036&mirid=1)
- Analyst forecasts and momentum — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1057521916301314); recommendation-revision return correlations — [ResearchGate](https://www.researchgate.net/publication/350482859_The_correlation_between_stock_returns_before_and_after_analyst_recommendation_revisions)

**Index effects, splits, dividends, buybacks**
- Greenwood & Sammon, "The Disappearing Index Effect" — [NBER WP 30748](https://www.nber.org/system/files/working_papers/w30748/w30748.pdf); [HBS](https://www.hbs.edu/ris/download.aspx?name=ssrn-4294297.pdf)
- NY Fed Staff Report 484, "Is There an S&P 500 Index Effect?" — [PDF](https://www.newyorkfed.org/medialibrary/media/research/staff_reports/sr484.pdf)
- [Morningstar: The S&P 500 Bump That Doesn't Last](https://www.morningstar.com/funds/sp-500-bump-that-doesnt-last); [ETF Trends: Retail Revival Fuels Comeback of Inclusion Effect](https://www.etftrends.com/retail-revival-fuels-comeback-sp-500-index-inclusion-effect/)
- Madhavan, "The Russell Reconstitution Effect" — [ResearchGate](https://www.researchgate.net/publication/228259894_The_Russell_Reconstitution_Effect); [CME Group 2026 reconstitution](https://www.cmegroup.com/articles/2026/the-2026-russell-reconstitution.html); [CBOE insights](https://www.cboe.com/insights/posts/russell-reconstitution-volatility-and-strategy-benchmark-indices/)
- Ikenberry & Ramnath, "Underreaction" (splits) — [Yale PDF](http://www.econ.yale.edu/~shiller/behfin/2000-05/ikenberry.pdf); information content of splits — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0378426611000616); post-split drift duration — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0378426619300305)
- Michaely, Thaler & Womack (1995), "Price Reactions to Dividend Initiations and Omissions," *JF* — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=420313); [NBER WP 4778](https://www.nber.org/papers/w4778)
- Ikenberry, Lakonishok & Vermaelen (1995), "Market Underreaction to Open Market Share Repurchases," *JFE* 39:181-208 — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=226564); [NBER WP 4965](https://www.nber.org/papers/w4965); Manconi, Peyer & Vermaelen global follow-up — [PDF](https://www.shareholderforum.com/wag/Library/20140800_Manconi-Peyer-Vermaelen.pdf)

**Options / expected move / risk sizing**
- [Options AI: Expected Move](https://tools.optionsai.com/expected-move); [Option Alpha: IV Crush](https://optionalpha.com/learn/iv-crush); [SpotGamma: IV Crush Explained](https://support.spotgamma.com/hc/en-us/articles/15249330755859-IV-Crush-Explained-What-It-Is-When-It-Happens-and-How-to-Trade-It); [MenthorQ IV Crush guide](https://menthorq.com/guide/iv-crush-understanding-the-earnings-driven-volatility-spike-and-how-to-capitalize-on-it/)
- [ApexVol: Options Earnings Trading Guide](https://apexvol.com/learn/earnings-trading-guide); [TradingRiot: Volatility Trading Around Earnings](https://blog.tradingriot.com/p/volatility-trading-around-earnings)
- Dubinsky & Johannes (GSAM/Columbia), "Option Pricing of Earnings Announcement Risks" — [NYU Stern PDF](https://www.stern.nyu.edu/sites/default/files/assets/documents/NYU%20conference%20Johannes%20final.pdf)
- Volatility spread and earnings responses — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S0378426617300791)
- [Kelly criterion — Wikipedia](https://en.wikipedia.org/wiki/Kelly_criterion); [Alpha Theory: Kelly in Practice](https://www.alphatheory.com/blog/kelly-criterion-in-practice-1); [Nick Yoder: Kelly Criterion](https://nickyoder.com/kelly-criterion/)

**Quant/ML practice**
- "The New Quant: A Survey of LLMs in Financial Prediction and Trading" — [arXiv](https://arxiv.org/html/2510.05533v1)
- Multi-modal deep learning for earnings-day direction — [arXiv](https://arxiv.org/html/2605.25894v1)
