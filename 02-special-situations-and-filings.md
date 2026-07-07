# Special Situations & Filing-Driven News Trading
### How professional event-driven funds trade unscheduled, fundamental news — and how a bot could replicate it

*Research compiled June 2026. Pure fundamental/news-driven strategies — no technical analysis. Intended as a design input for an automated news-trading bot.*

---

## 1. Overview

Event-driven investing is the discipline of trading securities whose value is about to be re-set by a discrete corporate or regulatory event rather than by general market direction. The professional universe breaks down roughly into:

| Strategy | Trigger | Typical holding period | Return character |
|---|---|---|---|
| Merger arbitrage | M&A announcement | Weeks–months (avg ~3 months) | Many small wins, rare large losses (short-put-like) |
| Biotech catalysts | PDUFA dates, trial readouts, AdComs | Days–weeks (run-up) or instant (binary) | Extreme binary outcomes |
| Activist stakes (13D) | EDGAR filing | Minutes (announcement pop) to months (drift) | ~6–7% announcement-window abnormal return |
| Insider clusters (Form 4) | EDGAR filing | Weeks–months | ~0.8%/month alpha for "opportunistic" trades |
| 8-K material events | EDGAR filing | Minutes–days | Item-dependent; volume + drift documented |
| Spin-offs | Form 10 / completion | 1–3 years | ~10%/yr historical outperformance |
| Short-seller reports | Report publication | Minutes (gap) to 100 days (drift to −12%) | Fast crash + persistent drift |
| Distressed/bankruptcy | Chapter 11 filing | Event-day to reorg | Large negative equity jump, then noise |
| Contracts/lawsuits/CEO news | 8-K / press release | Day to weeks | Small but systematic CARs |

Two structural facts make all of these tradable:

1. **Markets underreact to complex, unscheduled news.** The initial price jump is usually directionally correct but incomplete; documented post-announcement drift exists for earnings surprises, 13D filings, 8-K items, spin-offs, and short reports. This is the same mechanism as post-earnings-announcement drift (PEAD), where drift persists for 60–90 trading days ([review in J. of Behavioral and Experimental Finance](https://www.sciencedirect.com/science/article/pii/S2214635020303750); [Wikipedia overview](https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift)).
2. **Disclosure is mechanical and machine-readable.** US securities law forces information into standardized SEC forms (8-K, 13D/G, Form 4, S-1) published on EDGAR within ~1 second of acceptance, so a bot can read the same primary source as a hedge fund analyst — often faster than a human can.

The general lifecycle of price reaction to a material news item:

```
t=0          announcement hits (filing/PR/wire)
t+0–5 min    initial jump: 60–90% of total move for simple, unambiguous news
t+1d–90d     drift: continued move in announcement direction for complex news
             (PEAD, 13D drift, short-report drift, spin-off seasoning)
sometimes    reversal: overreaction snaps back (small-cap biotech failures
             corrected upward long-term; bad-news order-flow reverses short-term)
```

A bot's edge therefore comes from one of three places: (a) **speed** — being in the first seconds of the jump; (b) **drift capture** — entering after the jump and holding for the documented under-reaction; or (c) **probability mispricing** — holding through a binary event when the market's implied odds are wrong (merger spreads, PDUFA).

---

## 2. Merger Arbitrage

### What it is
When an acquisition is announced at price P, the target's stock jumps but settles **below** P. The gap (the "deal spread") compensates for completion risk and time-to-close. The arb buys the target and earns the spread if the deal closes; if it breaks, the stock falls back toward the pre-announcement level.

- **Cash deals:** simply long the target; payoff is independent of the acquirer's stock ([Princeton merger arb lecture notes](https://www.princeton.edu/~markus/teaching/Eco467/08Lecture/08a_Merger_Arbitrage_Intro.pdf)).
- **Stock-for-stock deals:** long the target, **short the acquirer in the exchange ratio** ("setting the spread"). For floating-ratio deals the ratio is set off an average of the acquirer's price (often the 10 trading days before close), so the hedge must be actively rebalanced ([Street of Walls merger arb explainer](https://www.streetofwalls.com/articles/hedge-fund/learn-the-basics/merger-arbitrage-strategy-explained/), [Risk arbitrage — Wikipedia](https://en.wikipedia.org/wiki/Risk_arbitrage)).

### Why it works
The spread is essentially an insurance premium. Natural holders (index funds, retail) sell the target after the pop rather than carry deal-break risk; arbs are paid to warehouse it. Returns resemble **selling uncovered index puts**: Mitchell & Pulvino showed merger-arb beta is ~0 in flat/rising markets but jumps to ~0.50 when the market falls ≥4% in a month ("Characteristics of Risk and Return in Risk Arbitrage," *Journal of Finance*, 2001 — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=268144), [Kellogg summary](https://www.kellogg.northwestern.edu/academics-research/research/detail/2001/characteristics-of-risk-and-return-in-risk-arbitrage/)).

### The numbers
- Mitchell & Pulvino (2001), 4,750 deals 1963–1998: **~4%/yr excess return** after transaction costs; raw annualized returns ~6.2%.
- Baker & Savasoglu (2002), 1,901 mergers 1981–1996: **~9.6%/yr excess returns** for a diversified arb portfolio ([Wikipedia summary of both](https://en.wikipedia.org/wiki/Risk_arbitrage)).
- ~**90% of announced deals close**; a 2010 study of 2,182 mergers (1990–2007) found an **8.0% break rate** ([Wikipedia](https://en.wikipedia.org/wiki/Risk_arbitrage)).
- On announcement the median target jumps **~27%** and then trades at a **~3.5% spread** to the offer ([NY Fed staff report, "Merger Options and Risk Arbitrage"](https://www.newyorkfed.org/medialibrary/media/research/staff_reports/sr761.pdf)).
- Typical small/mid-cap spreads of 5–10%; a 10% spread closing in 6 months ≈ 20% annualized ([InsideArbitrage introduction](https://www.insidearbitrage.com/introduction-to-merger-arbitrage/)).
- The return distribution is **negatively skewed**: many small gains, occasional 20–40% losses on breaks.

### Deal-break risk assessment (what the spread is pricing)
Key determinants of completion ([Accelerate, "A Practitioner's Guide to Merger Arbitrage"](https://accelerateshares.com/wp-content/uploads/2020/02/A-Practitioner%E2%80%99s-Guide-to-Merger-Arbitrage-1.pdf)):
1. Target shareholder & board approval
2. **Regulatory approval** — FTC/DOJ antitrust (HSR second requests widen spreads sharply), EU/UK competition authorities, and **CFIUS** for foreign acquirers in sensitive sectors
3. Financing certainty (fully financed cash bid from a strategic > LBO with financing conditions)
4. Acquirer commitment / MAC-clause litigation risk

Case studies:
- **Microsoft–Activision** ($68.7B, Jan 2022 → Oct 2023): UK CMA initially *blocked* the deal in April 2023; spread blew out; deal closed only after the cloud-gaming rights divestiture to Ubisoft. A 21-month regulatory odyssey ([CNBC](https://www.cnbc.com/2023/10/13/microsoft-activision-blizzard-takeover-approved-by-uk-regulator-cma.html), [Bloomberg Law explainer](https://news.bloomberglaw.com/antitrust/microsoft-activision-deals-bumpy-regulatory-road-explained)).
- **Musk–Twitter** (2022): essentially zero regulatory risk, but buyer-walk risk drove the spread to **~47%** at the worst; arbs who held or added when Musk re-committed at $54.20 made outsized profits ([Nasdaq/InvestorPlace](https://www.nasdaq.com/articles/as-the-merger-arb-spread-widens-keep-an-eye-on-twitter-stock), [Accelerate trading-desk note](https://accelerateshares.com/blog/from-the-trading-desk-musks-twitter-buyout/), [Bloomberg](https://www.bloomberg.com/news/articles/2022-10-04/merger-arbitrage-traders-are-big-winners-in-musk-s-twitter-deal)).

### How pros operate (Paulson-style arb desks)
John Paulson's merger fund formalized the playbook (see his classic piece ["The 'Risk' in Risk Arbitrage"](https://www.valueplays.net/wp-content/uploads/paulson.pdf)): diversify across 30–60 announced deals, weight by completion probability and downside-if-break, lean into "definitive agreement" deals (not rumors), monitor regulatory milestones continuously and resize as confidence changes, and avoid deals where downside/spread ratio is poor. The market-implied completion probability can be backed out as:

```
implied_prob ≈ (current_price − break_price) / (deal_price − break_price)
```

The desk's alpha is having a *better* probability estimate than that — usually via antitrust/legal expertise.

### Bot implementation
- **Detection:** watch PR wires + 8-K filings for "definitive merger agreement" language; extract deal price, consideration type (cash/stock/mix), expected close, conditions. LLM extraction works well here.
- **Strategy A (announcement scalp):** on confirmed definitive cash deal, buy target instantly if it's still trading meaningfully below offer (it usually converges within minutes — speed-dependent).
- **Strategy B (spread harvesting):** maintain a portfolio of post-announcement spreads. Compute implied probability; take deals where a rules-based risk model (cash vs stock, strategic vs PE, HSR exposure, foreign buyer/CFIUS flag, financing condition flag) says implied break risk is overstated. Position sizing: cap each deal so a break (modeled as reversion to pre-announcement price) costs ≤0.5–1% of portfolio.
- **Strategy C (event updates):** trade spread changes on second requests, regulator statements, extension announcements — these are themselves news events with predictable spread impact.
- For stock deals the bot must support shorting the acquirer at the exchange ratio and rebalancing floating ratios.

### Risks / pitfalls
- Negative skew: one broken deal can erase months of carry. Diversification is not optional.
- Crowded trades: spreads gap wider when arb capital de-levers (2008, COVID March 2020) — exactly when the market falls (the nonlinear beta).
- Rumor-stage entries ("strategic alternatives" headlines) have far worse hit rates than definitive agreements.
- Borrow availability and cost when shorting acquirers.

---

## 3. Regulatory & FDA Catalysts (Biotech Binary Events)

### What it is
Small/mid-cap biotech valuations hinge on discrete regulatory events: **PDUFA dates** (FDA's statutory decision deadline on a drug application), **AdCom votes** (advisory committee meetings, usually ~2–3 months before PDUFA), and **clinical trial readouts** (Phase 2/3 topline data — these are *unscheduled* in the sense that exact dates are often only guided to a quarter). Outcomes are close to binary: approval/positive data vs CRL (complete response letter)/failure.

### The numbers
- **Run-up effect:** stocks systematically rise **20–40% in the 4–8 weeks before a PDUFA date** as speculative positioning builds ([BiopharmaWatch catalyst guide](https://www.biopharmawatch.com/blog/biotech-catalyst-trading-hedge-funds-insiders-fda-decisions), [Dan Sfera PDUFA explainer](https://dansfera.com/pdufa-explained)).
- Positive event studies: Fast Track designation announcements showed 5-day CARs of **+21.6%** for small biotechs ([Drug Discovery Today study](https://www.sciencedirect.com/science/article/abs/pii/S1359644623002878)).
- Failures are far more violent than successes: investors react strongly to failure announcements while success announcements often elicit muted responses; failed Phase 3s routinely produce **−40% to −80%** single-day moves (e.g. Lipocine −78%) ([event study, PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC9439234/), [PharmaVoice trial-flop roundups](https://www.pharmavoice.com/news/5-impactful-drug-trial-failures-from-the-last-year/634422/)).
- Base rates: only ~**61% of Phase 3 NME trials succeed**; ~39% fail ([Applied Clinical Trials](https://www.appliedclinicaltrialsonline.com/view/phase-iii-trial-failures-costly-preventable)).
- Small-cap failures show **overreaction then long-run upward correction**; large pharma barely moves on the same news ([UNI honors thesis on biotech overreaction](https://scholarworks.uni.edu/cgi/viewcontent.cgi?article=1364&context=hpt), [PMC event study](https://pmc.ncbi.nlm.nih.gov/articles/PMC9439234/)).

### How pros position
- **Run-up trade (most common systematic approach):** buy 6–8 weeks before a known PDUFA/AdCom date, **exit before the decision** — capture speculative flow without binary exposure.
- **Through-the-event trades:** only with differentiated research (KOL consultations, statistical re-analysis of Phase 2 data, FDA precedent analysis). Sizing is brutal: pros put **1–3% (max 5%) of the book** into any single binary event ([BiopharmaWatch](https://www.biopharmawatch.com/blog/biotech-catalyst-trading-hedge-funds-insiders-fda-decisions)).
- **Options-first expression:** defined-risk structures (call spreads, put spreads, straddle sales when implied vol overprices the move). Informed institutional positioning often shows up in options flow before catalysts.
- **Post-event drift/reversal:** fade extreme small-cap crashes (documented overreaction), or buy approvals that still drift as commercial estimates get upgraded.

### Bot implementation
- Ingest an FDA catalyst calendar (PDUFA dates, AdCom dates, guided readout windows — commercial sources: [BiopharmaWatch FDA calendar](https://www.biopharmawatch.com/fda-calendar), [Benzinga FDA calendar](https://www.benzinga.com/fda-calendar), RTTNews).
- **Systematic run-up module:** enter T−40 trading days, exit T−2, equal small weights across all qualifying catalysts (market cap < $5B, cash runway > 1 yr to avoid dilution news mid-trade).
- **Event-reaction module:** parse PR headlines for "FDA approves" / "complete response letter" / "did not meet primary endpoint" and trade the *first minutes* of the move (speed game), or trade next-day drift/reversal rules (failure overreaction fade for small caps).
- Never hold un-hedged through the binary unless explicitly designed for it; if so, model the position as a lottery ticket with a hard 1–2% book cap.

### Risks / pitfalls
- Halts: FDA-decision and data news frequently arrives with the stock halted; the reopen gap consumes the whole move — speed strategies fail here, run-up strategies don't.
- After-hours releases; dilution (offerings priced into strength right after good news); CRLs that leak via "approval delayed" language.
- The run-up effect decays if it becomes crowded; condition on float, short interest, and options skew.

---

## 4. SEC Filing Signals (EDGAR as a Real-Time Alpha Feed)

EDGAR publishes accepted filings essentially immediately; the SEC's own submissions API updates within **~1 second** of acceptance, and commercial wrappers (sec-api.io, FilingFirehose, EarningsFeed) deliver parsed JSON within seconds ([FilingFirehose](https://filingfirehose.com/), [sec-api.io](https://sec-api.io/), [edgartools Python library](https://github.com/dgunning/edgartools)). Quants poll the EDGAR full-text/RSS endpoints continuously and diff against known accession numbers.

### 4.1 Schedule 13D / 13G — activist stakes

**What:** crossing 5% ownership forces a filing — **13D** if the holder intends to influence the company (activist), **13G** if passive. Historically due within 10 days of crossing (recently shortened to 5 business days), which means the activist has already accumulated before you see it — but the *announcement effect* is still large and persistent.

**Evidence:**
- Brav, Jiang, Partnoy & Thomas, "Hedge Fund Activism, Corporate Governance, and Firm Performance" (*Journal of Finance*, 2008): announcement-window abnormal return of **~7%, with no reversal over the following year**; activists achieve success/partial success in ~2/3 of campaigns; targets see higher payouts, operating improvement, and CEO turnover ([SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=948907), [Wiley](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1540-6261.2008.01373.x); companion paper ["The Returns to Hedge Fund Activism"](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1111778); [updated tables through 2019 on Brav's Duke page](https://people.duke.edu/~brav/HFactivism_March_2019.pdf)).
- Crowding decay: average 13D filing-day abnormal returns fell from **15.9% (2001) to 3.4% (2006)** as the strategy got popular.
- 13D vs 13G: average post-filing return **+6.34% for 13D vs +0.59% for 13G** — intent is the signal, not the stake itself ([Columbia Law Review empirical study](https://columbialawreview.org/content/a-little-letter-a-big-difference-an-empirical-inquiry-into-possible-misuse-of-schedule-13g13d-filings/)). 13D filings routinely move prices **5–15% the day they hit** ([HedgeTrace](https://www.hedgetrace.com/learn/schedule-13d-explained)).
- Filer identity matters enormously: an Elliott/Starboard/Icahn/ValueAct 13D ≠ an unknown family office. Commercial feeds tag brand-name activists automatically ([FilingFirehose](https://filingfirehose.com/)).

**Bot implementation:** stream new 13D filings; parse filer CIK against a curated activist whitelist with historical campaign win rates; parse Item 4 (purpose) for language like "discussions with management," "strategic alternatives," "board representation"; buy at first print, hold for drift (weeks–months, since Brav et al. show no reversal). Trade 13G→13D *conversions* too — they signal a passive holder turning hostile.

### 4.2 Form 4 — insider trading clusters

**What:** officers/directors/10% holders must report trades within 2 business days. Open-market **purchases** are the signal (sales are noise — diversification, taxes, options).

**Evidence:**
- Cohen, Malloy & Pomorski, "Decoding Inside Information" (*Journal of Finance*, 2012): split insiders into **routine** (same calendar month every year — zero signal) vs **opportunistic** traders. A portfolio following opportunistic trades earns **value-weighted abnormal returns of ~82 bps/month (~10%/yr)**; opportunistic purchases show ~5.2% 6-month alpha ([NBER WP 16454](https://www.nber.org/system/files/working_papers/w16454/w16454.pdf), [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1692517), [AQR summary](https://www.aqr.com/Insights/Research/Journal-Article/Decoding-Inside-Information)).
- **Cluster buys** (≥2–3 distinct insiders buying within a short window) roughly **double** the excess return of single-insider buys ([MarketTriage insider-signal guide](https://markettriage.com/insider-trading-signals)).
- Strongest setups: CFO/CEO purchases, purchases after large price declines, small caps with thin analyst coverage.

**Bot implementation:** ingest Form 4s in real time (edgartools parses them natively); filter to open-market buys (transaction code P); maintain per-insider trade history to classify routine vs opportunistic per Cohen-Malloy-Pomorski; trigger on clusters (≥2 insiders, ≥$100k total, within 10 days); hold 1–6 months. This is a *slow* signal — latency doesn't matter, classification quality does.

### 4.3 Form 8-K — material current events

**What:** companies must file an 8-K within 4 business days of material events, coded by item number: 1.01 (material agreements), 1.03 (bankruptcy), 2.02 (results), 5.02 (officer/director departures), 7.01/8.01 (other). Many 8-Ks accompany a same-moment press release, but some (especially 5.02 departures and 1.02 contract terminations) are the *first* public disclosure.

**Evidence:** Lerman & Livnat, "The New Form 8-K Disclosures" (*Review of Accounting Studies*, 2009): all 8-K item types show abnormal volume and volatility at both event and filing dates, and **some items exhibit significant post-filing return drift**; 8-Ks are most informative for small, low-coverage firms ([paper PDF](https://pages.stern.nyu.edu/~jlivnat/f8k%20current.pdf), [Springer](https://link.springer.com/article/10.1007/s11142-009-9114-7)).

**Bot implementation:** subscribe to the 8-K stream; route by item code; apply an LLM to classify direction/materiality (e.g., 5.02: was the CFO fired "effective immediately" with no successor — bearish — or a planned retirement — neutral?); trade fast on unambiguous codes (1.03 bankruptcy = short/avoid; unexpected 5.02 = short small caps), drift-trade ambiguous ones. Prioritize filings that arrive **outside** a simultaneous press release.

### 4.4 S-1 / IPO-related signals

S-1s, S-1/As (pricing ranges), and lockup expirations are scheduled-ish but their *amendments* arrive unscheduled. Signals: insider participation in the offering, secondary vs primary mix, sudden withdrawal (RW filings). Secondary signal for a news bot rather than a core strategy; useful mainly to flag dilution risk on names held by other modules.

---

## 5. Other Special Situations

### 5.1 Spin-offs
- Cusatis, Miles & Woolridge, "Restructuring through spinoffs" (*Journal of Financial Economics*, 1993; sample 1965–1988): spun-off entities beat the market by **~30% over the first 3 years**, parents by ~18% ([ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/0304405X9390009Z), [paper PDF](https://longrunplan.com/wp-content/uploads/2018/09/restructuring-through-spinoffs.pdf)). Greenblatt's *You Can Be a Stock Market Genius* popularized the ~**10%/yr outperformance** figure ([summary](https://acquirersmultiple.com/2025/07/joel-greenblatt-how-spinoffs-and-special-situations-beat-the-market/)). McConnell's later JPM work finds the effect weaker but persistent ([Purdue PDF](https://business.purdue.edu/faculty/mcconnell/publications/The%20Stock%20Price...2013%20jpm.2015.42.1.143.pdf)).
- **Why:** forced selling — index funds and large holders dump the small spun-off entity regardless of value in the first weeks, creating a depressed entry; incentivized standalone management then outperforms.
- **Bot:** detect spin-off announcements (8-K/press release) and Form 10 filings; the systematic trade is to buy the spinco **after** the post-distribution forced-selling window (rule of thumb: wait 1–3 months or for selling-pressure exhaustion measured by volume normalization), hold 1–2 years. This is a slow capital-commitment strategy, not a latency strategy.

### 5.2 Bankruptcy / distressed news
- Chapter 11 filings hit equity hard but not to zero — e.g., QVC Group fell **−18.8% on filing day at 12x average volume** ([StockTitan](https://www.stocktitan.net/news/QVCGA/qvc-group-to-significantly-strengthen-financial-position-as-company-w84q5ms4oj2h.html)); >50% of bankrupt stocks still trade daily years into Chapter 11 ([J. of Financial Markets study](https://www.sciencedirect.com/science/article/abs/pii/S1386418112000407)).
- Equity in Chapter 11 is usually wiped out in reorganization; post-filing equity rallies are predominantly retail-driven noise ([Fidelity](https://www.fidelity.com/viewpoints/active-investor/stocks-and-bankruptcy), [FINRA](https://www.finra.org/investors/insights/what-corporate-bankruptcy-means-shareholders)). Professional distressed investing happens in the **debt**, which is out of scope for an equity news bot.
- **Bot:** treat bankruptcy/going-concern language as (a) a short/exit trigger on first headline (going-concern warnings in 10-K/10-Q, "evaluating strategic alternatives including restructuring," DIP financing news), and (b) a do-not-long filter. Shorting post-filing equity is intuitive but hard: borrow is scarce and squeezes (Hertz 2020!) are violent.

### 5.3 Short-seller reports (Hindenburg / Muddy Waters effect)
- Target stocks crash on publication and keep falling: CARs ≈ **−4% in the 20-day window** and **below −12% by 100 days** after campaign announcement (Appel & Fos, "Short and Report"/public short-selling research — [NYU AFA draft](https://www.law.nyu.edu/sites/default/files/short-activism-v03-013119_afa.pdf), [Harvard Law governance summary](https://corpgov.law.harvard.edu/2018/10/01/public-short-selling-by-activist-hedge-funds/)).
- A sample of 70 Hindenburg/Muddy Waters reports since 2015: **~67% traded lower a year later** ([Decoding Markets analysis](https://stocksoftresearch.com/can-you-make-money-with-short-seller-reports/)). Famous one-day/one-month moves: Nikola −40%, Super Micro −28% in a month, Adani group −$150B market value ([Hindenburg — Wikipedia](https://en.wikipedia.org/wiki/Hindenburg_Research)).
- Brendel & Ryans (*JAR* 2021) catalog allegations/responses: companies that respond substantively recover more; only ~30% of fraud allegations are ultimately confirmed, so reversals happen ([Wiley](https://onlinelibrary.wiley.com/doi/10.1111/1475-679X.12356)).
- **Bot:** monitor a fixed list of credible short-shop publication channels (their websites/X accounts) — Muddy Waters, Culper, Grizzly, Fuzzy Panda, Scorpion, etc. On detection: short (or buy puts) immediately — the drift evidence says the move continues for ~100 days, so even a minutes-late entry has positive expectancy. Score publisher track record; skip low-credibility shops. Cover into capitulation or on substantive company rebuttal. Mind borrow cost and halt risk.

### 5.4 CEO/CFO departures
Disclosed via 8-K Item 5.02. Market reaction depends on context: unexpected, immediate-effective, no-successor departures of CFOs at small caps are reliably negative; planned retirements are non-events. Often paired with insider-trading-window evidence (abnormally quiet insider activity precedes executive-change 8-Ks — [Guay, Kim & Tsui, Wharton](https://accounting.wharton.upenn.edu/wp-content/uploads/2021/04/Guay_Kim_Tsui_Determinants-of-Insider-Trading-Windows_4_22_21.pdf)). Bot: LLM classification on the 5.02 text (abruptness, stated reason, successor named, accompanying restatement language), short the unambiguous bad ones at small caps.

### 5.5 Lawsuits & settlements
- Litigation announcement: average decline ≈ **−5%**; securities class-action filings show significantly negative CARs in most studies, but reactions are two-sided (some lawsuits resolve uncertainty) ([EconStor event study](https://www.econstor.eu/bitstream/10419/148358/1/eventStudy2.pdf), [Benzinga overview](https://www.benzinga.com/money/how-does-a-lawsuit-impact-a-stocks-price)).
- Settlements/dismissals are usually positive catalysts (uncertainty removal). Patent verdicts produce sharp two-sided moves at the verdict timestamp ([Columbia patent-litigation event study](https://econ.columbia.edu/wp-content/uploads/sites/32/2018/03/zhang.pdf)).
- Bot: court-docket monitoring (PACER/CourtListener RSS) for verdicts and settlements in cases pre-tagged as material; trade direction = verdict sign for the named company. Niche but uncrowded.

### 5.6 Contract wins (incl. government contracts)
- Event studies of 1,963 government contract awards (1990–2000) show **positive, significant abnormal returns**, concentrated in **small caps** where a single award is material; mega-cap awards (Lockheed) barely move price ([ResearchGate](https://www.researchgate.net/publication/265493613_The_Determinants_and_Impact_of_Government_Contract_Award_on_the_Market_Value_of_the_Winning_Firms), [Economics Letters 2025](https://www.sciencedirect.com/science/article/abs/pii/S0165176525001727), [LevelFields practical guide](https://www.levelfields.ai/news/how-to-trade-big-government-contract-announcements)).
- Bot: scrape DoD daily contract announcements (defense.gov publishes ~5pm ET daily) and SAM.gov award feeds; compute award size / company revenue; long small caps where the ratio is large, at next open or instantly if after-hours liquidity exists.

### 5.7 Government policy announcements
Sector-wide shocks (tariffs, drug-pricing rules, energy subsidies, export controls) re-price whole baskets. These are the hardest to systematize because each is sui generis, but the pattern is: instant basket move, multi-day drift as analysts quantify exposure. A bot can pre-map ticker→policy-exposure baskets (e.g., "China revenue %", "Medicare exposure", "tariff-sensitive importers") and trade the basket on classified policy headlines. Treat as a v2 feature; per-event LLM judgment is required.

---

## 6. Execution: How Professionals Actually Trade This

### Speed tiers — match the strategy to the latency you can afford
| Tier | Latency need | Strategies | Infrastructure |
|---|---|---|---|
| 1. Race | < 1 s | 8-K/PR scalps, M&A announcement convergence, short-report first print | Machine-readable news (Bloomberg Event-Driven Feeds, Dow Jones Newswires ML, LSEG MRN), co-location, pre-built order templates ([LSEG hedge fund solutions](https://www.lseg.com/en/solutions/hedge-funds), [Institutional Investor on event data](https://www.institutionalinvestor.com/article/2btgf72gw5yeqzkbmjtvk/portfolio/hedge-funds-seeking-differentiation-look-to-event-data)) |
| 2. Fast | seconds–minutes | 13D pops, FDA headline reactions, contract awards | EDGAR polling (~1s), wire/RSS scrapers, market orders into the jump |
| 3. Drift | hours–days | PEAD-style drift, 13D hold, short-report drift, 8-K drift | No speed edge needed; entry at next open is fine |
| 4. Carry | weeks–months | Merger spread portfolio, insider clusters, spin-offs, PDUFA run-up | Research/classification quality is the entire edge |

A retail-grade bot **cannot win Tier 1 against HFT** on wire headlines, but Tier 2 on EDGAR is surprisingly open (EDGAR is free and ~1s), and Tiers 3–4 are fully accessible — the academic alphas above (Brav 7%, CMP 82bps/mo, spin-off 10%/yr, short-report −12% drift) were all measured at *daily* horizons, i.e., they survive slow entry.

### Position sizing for binary outcomes
- Pros size binary events at **1–3% of book, max 5%** ([BiopharmaWatch](https://www.biopharmawatch.com/blog/biotech-catalyst-trading-hedge-funds-insiders-fda-decisions)); merger arb desks size so a single break costs <1% of NAV.
- Kelly-style logic with heavy fractional discounting (¼ Kelly or less) because probability estimates are themselves uncertain.
- Prefer defined-risk option structures when IV doesn't already price the full binary move.
- Portfolio construction: many simultaneous uncorrelated events > few concentrated ones; the merger-arb literature's entire excess return depends on diversification across deals.

### Hedging
- Stock-deal arb: short acquirer at the exchange ratio.
- Sector/beta hedge: short XBI against long biotech catalysts, short sector ETF against single-name event longs, so the position isolates the event.
- Event hedges: puts as deal-break insurance on concentrated arb positions ([NY Fed: option prices embed deal outcomes](https://www.newyorkfed.org/medialibrary/media/research/staff_reports/sr761.pdf)).

### Price-reaction lifecycle (design contract for the bot)
1. **Jump (0–5 min):** most of the move for simple news. Only trade if you're genuinely fast and the news is unambiguous (definitive merger, FDA approval, Chapter 11).
2. **Drift (1–90 days):** the empirically fattest, most accessible edge — under-reaction to complex news (PEAD analog; 13D, 8-K, short reports, spin-offs) ([PEAD review](https://www.sciencedirect.com/science/article/pii/S2214635020303750)).
3. **Reversal:** overreaction fades — small-cap biotech crash recoveries, bad-news order-flow reversal in the short term ([asymmetric PEAD/order-flow study](https://www.sciencedirect.com/science/article/abs/pii/S1057521924002485)). Trade only with explicit small-cap/overreaction filters.

---

## 7. Bot Architecture Sketch (synthesis)

```
INGEST                       CLASSIFY                    DECIDE                      EXECUTE
─ EDGAR poller (1s)          ─ form-type router          ─ per-strategy modules:     ─ broker API
  · 8-K, 13D/G, Form 4,        (8-K item codes,            merger-arb spread book      (limit into jumps,
    S-1, Form 10               13D Item 4 LLM read,        13D activist follow          market for drift)
─ PR wires / RSS               Form 4 P-code +             Form 4 cluster follow     ─ position sizer
─ FDA catalyst calendar        routine/opportunistic)      FDA run-up calendar         (1–3% binary cap,
─ short-shop watchlist       ─ filer reputation DB         short-report follow         0.5–1% per-deal
─ DoD/SAM.gov awards         ─ materiality scorer          8-K event reactions         break-loss cap)
─ court dockets                (size vs mkt cap)           spin-off tracker          ─ hedger (ratio shorts,
                                                                                       sector ETF offsets)
```

Priority order for building (edge ÷ implementation difficulty):
1. **13D activist follow** — clean trigger, documented 6–7% non-reverting alpha, latency-tolerant.
2. **Form 4 opportunistic clusters** — ~10%/yr documented alpha, zero speed needed, pure classification.
3. **Merger-arb spread portfolio** — steady carry, well-understood risk model.
4. **Short-report follow** — strong drift, needs a publisher watchlist + borrow checks.
5. **PDUFA run-up calendar trade** — calendar-driven, exits before the binary.
6. **8-K item-coded reactions** — highest ceiling, needs LLM classification quality + Tier-2 speed.
7. Spin-offs, contracts, lawsuits, policy baskets — slower/niche, add later.

## 8. Cross-cutting risks
- **Alpha decay from crowding** — 13D returns fell 15.9%→3.4% (2001–2006) as the trade got popular; assume published numbers are upper bounds.
- **Negative skew everywhere** — merger breaks, binary biotech, short squeezes on short-follows. Survival = sizing discipline, not signal quality.
- **Halts and gaps** — much of this news prints into halted or closed markets; backtests must use realistic *achievable* entry prices (next trade/next open, not announcement-moment prints).
- **Classification errors** — an LLM misreading an 8-K is a direct loss generator; require confidence thresholds and human-review queues early on.
- **Legal line** — trading on *public* filings/news is legal; the bot must never ingest non-public information, and short-report following should avoid any coordination with publishers.
- **Regime change** — antitrust enforcement intensity, the 13D deadline shortening to 5 days, and EDGAR dissemination changes all shift the economics; revalidate annually.

---

## Sources

**Merger arbitrage**
- Mitchell, M. & Pulvino, T. (2001), "Characteristics of Risk and Return in Risk Arbitrage," *Journal of Finance* 56(6) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=268144) · [Kellogg](https://www.kellogg.northwestern.edu/academics-research/research/detail/2001/characteristics-of-risk-and-return-in-risk-arbitrage/) · [Wiley](https://onlinelibrary.wiley.com/doi/abs/10.1111/0022-1082.00401)
- Baker, M. & Savasoglu, S. (2002), risk-arb portfolio returns — summarized at [Risk arbitrage — Wikipedia](https://en.wikipedia.org/wiki/Risk_arbitrage)
- NY Fed Staff Report 761, "Merger Options and Risk Arbitrage" — [PDF](https://www.newyorkfed.org/medialibrary/media/research/staff_reports/sr761.pdf)
- Accelerate, "A Practitioner's Guide to Merger Arbitrage" — [PDF](https://accelerateshares.com/wp-content/uploads/2020/02/A-Practitioner%E2%80%99s-Guide-to-Merger-Arbitrage-1.pdf)
- Paulson & Co., "The 'Risk' in Risk Arbitrage" — [PDF](https://www.valueplays.net/wp-content/uploads/paulson.pdf)
- [InsideArbitrage merger-arb introduction](https://www.insidearbitrage.com/introduction-to-merger-arbitrage/) · [risk analysis](https://www.insidearbitrage.com/2025/04/merger-arbitrage-risk-analysis/)
- [Street of Walls merger arbitrage strategy](https://www.streetofwalls.com/articles/hedge-fund/learn-the-basics/merger-arbitrage-strategy-explained/) · [Princeton lecture notes](https://www.princeton.edu/~markus/teaching/Eco467/08Lecture/08a_Merger_Arbitrage_Intro.pdf)
- Microsoft–Activision: [CNBC](https://www.cnbc.com/2023/10/13/microsoft-activision-blizzard-takeover-approved-by-uk-regulator-cma.html) · [Bloomberg Law](https://news.bloomberglaw.com/antitrust/microsoft-activision-deals-bumpy-regulatory-road-explained) · [Wikipedia](https://en.wikipedia.org/wiki/Acquisition_of_Activision_Blizzard_by_Microsoft)
- Musk–Twitter: [Nasdaq](https://www.nasdaq.com/articles/as-the-merger-arb-spread-widens-keep-an-eye-on-twitter-stock) · [Accelerate desk note](https://accelerateshares.com/blog/from-the-trading-desk-musks-twitter-buyout/) · [Bloomberg](https://www.bloomberg.com/news/articles/2022-10-04/merger-arbitrage-traders-are-big-winners-in-musk-s-twitter-deal)

**Activism / 13D & insiders / SEC filings**
- Brav, A., Jiang, W., Partnoy, F. & Thomas, R. (2008), "Hedge Fund Activism, Corporate Governance, and Firm Performance," *Journal of Finance* — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=948907) · [Wiley](https://onlinelibrary.wiley.com/doi/abs/10.1111/j.1540-6261.2008.01373.x) · ["The Returns to Hedge Fund Activism" (SSRN)](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1111778) · [updated tables 2019 (Duke)](https://people.duke.edu/~brav/HFactivism_March_2019.pdf)
- Columbia Law Review, "A Little Letter, A Big Difference" (13D vs 13G returns) — [link](https://columbialawreview.org/content/a-little-letter-a-big-difference-an-empirical-inquiry-into-possible-misuse-of-schedule-13g13d-filings/)
- Cohen, L., Malloy, C. & Pomorski, L. (2012), "Decoding Inside Information," *Journal of Finance* — [NBER WP 16454](https://www.nber.org/system/files/working_papers/w16454/w16454.pdf) · [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1692517) · [AQR](https://www.aqr.com/Insights/Research/Journal-Article/Decoding-Inside-Information)
- Lerman, A. & Livnat, J. (2009), "The New Form 8-K Disclosures," *Review of Accounting Studies* — [PDF](https://pages.stern.nyu.edu/~jlivnat/f8k%20current.pdf) · [Springer](https://link.springer.com/article/10.1007/s11142-009-9114-7)
- Guay, Kim & Tsui, "Determinants of Insider Trading Windows" — [Wharton PDF](https://accounting.wharton.upenn.edu/wp-content/uploads/2021/04/Guay_Kim_Tsui_Determinants-of-Insider-Trading-Windows_4_22_21.pdf)
- EDGAR tooling: [edgartools (GitHub)](https://github.com/dgunning/edgartools) · [sec-api.io](https://sec-api.io/) · [FilingFirehose](https://filingfirehose.com/) · [EarningsFeed API](https://earningsfeed.com/api) · [HedgeTrace 13D explainer](https://www.hedgetrace.com/learn/schedule-13d-explained) · [MarketTriage Form 4 guide](https://markettriage.com/insider-trading-signals)

**Biotech / FDA**
- [BiopharmaWatch: hedge funds & FDA catalyst trading](https://www.biopharmawatch.com/blog/biotech-catalyst-trading-hedge-funds-insiders-fda-decisions) · [FDA calendar](https://www.biopharmawatch.com/fda-calendar) · [Dan Sfera PDUFA explainer](https://dansfera.com/pdufa-explained) · [Benzinga FDA calendar](https://www.benzinga.com/fda-calendar)
- "The reaction of sponsor stock prices to clinical trial outcomes: an event study" — [PMC](https://pmc.ncbi.nlm.nih.gov/articles/PMC9439234/)
- Fast Track designation event study, *Drug Discovery Today* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1359644623002878)
- "Biotech fever: market overreaction to FDA clinical trials" — [UNI](https://scholarworks.uni.edu/cgi/viewcontent.cgi?article=1364&context=hpt)
- Phase III failure rates — [Applied Clinical Trials](https://www.appliedclinicaltrialsonline.com/view/phase-iii-trial-failures-costly-preventable) · trial-flop examples — [PharmaVoice](https://www.pharmavoice.com/news/5-impactful-drug-trial-failures-from-the-last-year/634422/)

**Short sellers / distressed / other special situations**
- Appel & Fos, "Public Short Selling by Activist Hedge Funds" / "Short and Report" — [Harvard Law summary](https://corpgov.law.harvard.edu/2018/10/01/public-short-selling-by-activist-hedge-funds/) · [NYU AFA draft](https://www.law.nyu.edu/sites/default/files/short-activism-v03-013119_afa.pdf)
- Brendel & Ryans (2021), "Responding to Activist Short Sellers," *Journal of Accounting Research* — [Wiley](https://onlinelibrary.wiley.com/doi/10.1111/1475-679X.12356)
- Hindenburg/Muddy Waters outcomes — [Hindenburg Research — Wikipedia](https://en.wikipedia.org/wiki/Hindenburg_Research) · [Decoding Markets 70-report study](https://stocksoftresearch.com/can-you-make-money-with-short-seller-reports/) · [Fortune longform on activist shorts](https://fortune.com/longform/short-selling-stock-market-bets-hindenburg-viceroy-muddy-waters/)
- Cusatis, Miles & Woolridge (1993), "Restructuring through spinoffs," *JFE* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/0304405X9390009Z) · [PDF](https://longrunplan.com/wp-content/uploads/2018/09/restructuring-through-spinoffs.pdf) · [McConnell JPM follow-up](https://business.purdue.edu/faculty/mcconnell/publications/The%20Stock%20Price...2013%20jpm.2015.42.1.143.pdf) · [Greenblatt summary](https://acquirersmultiple.com/2025/07/joel-greenblatt-how-spinoffs-and-special-situations-beat-the-market/)
- "Investing in Chapter 11 stocks," *J. of Financial Markets* — [ScienceDirect](https://www.sciencedirect.com/science/article/abs/pii/S1386418112000407) · [Fidelity on bankruptcy stocks](https://www.fidelity.com/viewpoints/active-investor/stocks-and-bankruptcy) · [FINRA](https://www.finra.org/investors/insights/what-corporate-bankruptcy-means-shareholders) · [QVC example (StockTitan)](https://www.stocktitan.net/news/QVCGA/qvc-group-to-significantly-strengthen-financial-position-as-company-w84q5ms4oj2h.html)
- Government contracts: [ResearchGate event study (1,963 awards)](https://www.researchgate.net/publication/265493613_The_Determinants_and_Impact_of_Government_Contract_Award_on_the_Market_Value_of_the_Winning_Firms) · [Economics Letters 2025](https://www.sciencedirect.com/science/article/abs/pii/S0165176525001727) · [LevelFields guide](https://www.levelfields.ai/news/how-to-trade-big-government-contract-announcements)
- Lawsuits: [EconStor securities-litigation event study](https://www.econstor.eu/bitstream/10419/148358/1/eventStudy2.pdf) · [Columbia patent-litigation event study](https://econ.columbia.edu/wp-content/uploads/sites/32/2018/03/zhang.pdf) · [Benzinga lawsuit impact overview](https://www.benzinga.com/money/how-does-a-lawsuit-impact-a-stocks-price)

**Market microstructure / drift**
- PEAD review, *J. of Behavioral and Experimental Finance* — [ScienceDirect](https://www.sciencedirect.com/science/article/pii/S2214635020303750) · [PEAD — Wikipedia](https://en.wikipedia.org/wiki/Post%E2%80%93earnings-announcement_drift) · [asymmetric PEAD & order-flow imbalance](https://www.sciencedirect.com/science/article/abs/pii/S1057521924002485)
- Event-data infrastructure: [LSEG hedge fund solutions](https://www.lseg.com/en/solutions/hedge-funds) · [Institutional Investor on event data](https://www.institutionalinvestor.com/article/2btgf72gw5yeqzkbmjtvk/portfolio/hedge-funds-seeking-differentiation-look-to-event-data)
