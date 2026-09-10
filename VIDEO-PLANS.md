# Video Plans — Trading Series (all reels, research + build)

**Videos 1–4 = research phase** — the techniques Wall Street uses to find edge. Weighted toward
**modern (post-2000s)**, but keeping the classic edges where they still matter.
**Videos 5–6 = pivot** — which techniques got picked, and the pipeline on a whiteboard.
**Videos 7+ = build phase** — one pipeline stage per video, code included. These are the "Build reel"
folders in `Trading Videos/`; day numbers (Day 8, Day 9, …) are the on-camera series counter.

Style: bilingual EN/ID, personal journey, hook → explain → CTA teasing the next phase.
Format: 60–90s Reels. Casual "lo/gua" Indonesian.

**Arc:** event-driven drift (timeless) → alternative data (modern) → machine learning & quant (modern)
→ AI reads the news (modern frontier) → what I picked → the pipeline → building each stage for real.

**Where each video lives**

| # | Day | Title (short) | Folder | State |
|---|---|---|---|---|
| 1–4 | — | Research reels | `Trading Videos/Research reel 1–4` | shipped |
| 5 | Day 6 | What I'm building / killed 90% | `Trading Videos/Build reel 1` | shipped |
| 6 | Day 7 | The 5-stage pipeline | `Trading Videos/Build Reel 2` | shipped |
| 7 | Day 8 | Reading the news for free (ingestion) | `Trading Videos/Build Reel 3` | script only, no footage |
| 8 | Day 9 | Dedup + novelty | `Trading Videos/Build Reel 4` | script only, no footage |
| 9 | Day 10 | Ticker mapping | `Trading Videos/Build Reel 5` | script only, no footage |
| 10 | Day 11 | Signal rules before AI | `Trading Videos/Build Reel 6` | script only, no footage |
| 11+ | — | Risk gate + kill switch, then honest backtesting | not written yet | — |

Videos 7–10 below are the full shooting scripts. Each reel folder also holds the same script as
`SCRIPT.md` plus its edit-time detail (overlay slot names, ProRes/subtitle checklist).

---

## Video 1 — "How Wall Street Trades News Before You Even React"
*Covers: PEAD, Analyst Revision Momentum, Merger Arbitrage, FDA/PDUFA Run-Up, 13D Activist + Insider Clusters, Short-Seller Report Drift*

**Core idea:** The market doesn't fully price news the day it breaks. There's a decades-old playbook — still running today — that exploits the slow *drift* after an event. The HFT firms win the first milliseconds; the durable, retail-accessible edge is the drift that plays out over days to weeks. Don't race the spike, trade the drift.

**Hook (0–5s)**
> "Tau nggak, market nggak langsung price in berita pas keluar? Ada playbook yang exploit ini — dan masih jalan sampai sekarang."

**Explain (5–75s)**

**1. PEAD — Post-Earnings Announcement Drift (13s)**
- When a company beats earnings expectations, the stock doesn't just jump once — it keeps drifting up for ~60 days. Misses keep drifting down.
- Discovered in 1989, still works today. The most studied anomaly in all of finance.
- *"Ini bukan lucky. Ini systematic underreaction."*
- 📄 Original paper: Bernard & Thomas (1989) — [JSTOR](https://www.jstor.org/stable/2491062)
- 📖 Explainer: [DayTrading.com — PEAD Strategy](https://www.daytrading.com/post-earnings-announcement-drift-pead-strategy)

**2. Analyst Revision Momentum (10s)**
- When analysts upgrade a stock or raise price targets, it drifts up for weeks after. Downgrades are even more powerful — stocks fall further and faster than they rise on upgrades.
- Works because most fund managers can't act instantly on a revision — they need approval cycles.
- *"Berita lama buat lo, edge buat yang sabar nunggu drift-nya."*
- 📄 Original paper: Womack (1996) — [RepEc](https://ideas.repec.org/a/bla/jfinan/v51y1996i1p137-67.html)
- 📖 Explainer: [Motley Fool — Upgrades & Downgrades](https://www.fool.com/investing/how-to-invest/stocks/upgrades-downgrades/)

**3. Merger Arbitrage (12s)**
- Company A announces it's buying Company B at $50/share. Stock trades at $47. Arb funds buy at $47, wait for the deal to close, collect $3.
- ~90% of announced deals close. Returns aren't huge per trade, but they're consistent and uncorrelated to the market.
- *"Ini bukan gambling. Ini lebih kayak earning interest on a deal."*
- 📄 Original paper: Mitchell & Pulvino (2001) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=268144)
- 📖 Explainer: [Corporate Finance Institute — Merger Arbitrage](https://corporatefinanceinstitute.com/resources/valuation/merger-arbitrage/)

**4. FDA / PDUFA Run-Up (12s)**
- Biotech companies have government approval dates (PDUFA dates) — published months in advance on the FDA website.
- Stocks rally 20–40% in the 4–8 weeks BEFORE the decision, just from anticipation. The smart trade is buying the run-up and exiting BEFORE the binary result — not gambling on the outcome.
- *"Lo nggak perlu tau hasilnya. Lo cukup tau kapan semua orang bakal excited."*
- 📖 Explainer: [Motley Fool — What Is a PDUFA Date?](https://www.fool.com/terms/p/pdufa-date/) · [Dan Sfera — PDUFA Explained](https://dansfera.com/pdufa-explained)

**5. SEC Filing Signals — Activists & Insiders (12s)**
- When a hedge fund buys >5% of a company, they MUST file a 13D with the SEC within 10 days. Public info. Stocks drift up ~6–7% over the following weeks.
- Even stronger: when company INSIDERS buy in clusters (multiple insiders at once, not just one).
- *"The SEC makes them announce it. Kita tinggal follow."*
- 📄 Activist paper: Brav, Jiang, Partnoy & Thomas (2008) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=948907)
- 📄 Insider paper: Cohen, Malloy & Pomorski (2012) — [NBER](https://www.nber.org/papers/w16454)

**6. Short-Seller Reports (8s)**
- Firms like Hindenburg and Muddy Waters publish investigative reports exposing fraud. Stocks hit by these fall an average of −12% over the next 100 days; 67% are still lower a year later.
- The drift is tradeable — the initial crash often overreacts, then a slower decline follows.
- 📄 Academic paper: Brendel & Ryans (2021) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3850250)
- 📖 Explainer: [Fortune — Little Big Shorts](https://fortune.com/longform/short-selling-stock-market-bets-hindenburg-viceroy-muddy-waters/)

**On-screen source overlays (Reel 1 build — preview_v4):**
The reel only covers 4 of the 6 techniques above (PEAD, Analyst Revision, Merger Arb, FDA/PDUFA). Each gets a floating "source card" screenshot in the top band while it's discussed. Screenshots cached in `Trading Videos/Research reel 1/edit/sources/`.

| Technique | On-screen card | Source shown |
|---|---|---|
| PEAD | `crop_pead.png` | Bernard & Thomas (1989), *Post-Earnings-Announcement Drift* — Semantic Scholar (2,262 citations) |
| Analyst Revision | `crop_analyst.png` | Womack (1996), *Do Brokerage Analysts' Recommendations Have Investment Value?*, J. of Finance — RePEc/IDEAS (504 citations) |
| Merger Arbitrage | `crop_merger.png` | Mitchell & Pulvino (2001), *Characteristics of Risk and Return in Risk Arbitrage* — Semantic Scholar (691 citations) |
| FDA / PDUFA | `crop_fda.png` | U.S. FDA — *Prescription Drug User Fee Amendments* (fda.gov; no academic paper exists for this technique, so the official source is shown) |

*Note: JSTOR (Bernard & Thomas) and SSRN (Mitchell & Pulvino) hard-block headless screenshots (reCAPTCHA / Cloudflare), so the equivalent Semantic Scholar paper pages were captured instead — same paper, clean and readable.*

**CTA (75–88s)**
> "Tapi ini semua butuh nunggu berita keluar dulu. Gimana kalau ada cara tau hasilnya SEBELUM perusahaan ngumumin? Di video berikutnya — data rahasia yang dibeli hedge fund jutaan dolar. Follow dulu."

---



---

## Video 3 — "When AI Started Mining the Stock Market"
*Covers: The Factor Zoo & replication crisis, ML in the cross-section (Gu-Kelly-Xiu), LSTM/Transformer forecasting, Deep RL agents (FinRL & AlphaPortfolio), RL optimal execution, honest caveats*

**Core idea:** For 50 years academics "discovered" hundreds of return factors — most were just p-hacking. Modern machine learning flipped the script: instead of guessing factors one by one, it lets algorithms mine and combine all of them at once, and lets neural nets learn rules straight from raw price data. Roughly double the performance of classic models on paper — brutal to actually deploy.

**Hook (0–5s)**
> "Ada 400+ 'rumus rahasia' buat ngalahin pasar — dan kebanyakan BOHONG. Ini cara AI misahin yang asli dari yang cuma kebetulan."

**Explain (5–75s)**

**1. The Factor Zoo & Replication Crisis (13s)**
- Akademisi nge-klaim ratusan "factor" yang katanya prediksi return — value, momentum, size, dan ratusan lainnya.
- Hou, Xue & Zhang nguji 452 anomali: 65% GAGAL direplikasi alias hilang begitu diuji ulang dengan benar.
- *"Kebanyakan 'rahasia ngalahin market' itu cuma noise yang kebetulan rapi di data lama."*
- 📄 Original paper: Hou, Xue & Zhang (2020), *Replicating Anomalies* — [RFS](https://academic.oup.com/rfs/article-abstract/33/5/2019/5236964)
- 📖 Explainer: [Alpha Architect — Resolving the Factor Zoo](https://alphaarchitect.com/using-bayesian-solutions-to-resolve-the-factor-zoo/)

**2. ML in the Cross-Section — Gu, Kelly & Xiu (14s)**
- Daripada nebak factor satu-satu, kasih MESIN ~94 karakteristik saham sekaligus dan biarin dia nyari pola.
- Paper landmark ini nunjukin neural net & boosted trees ngegandain (sampai ~2x) kinerja strategi regresi linear klasik — out-of-sample R² & Sharpe jauh lebih tinggi.
- *"AI nggak butuh teori cantik — dia makan semua data dan nemuin interaksinya sendiri."*
- 📄 Original paper: Gu, Kelly & Xiu (2020), *Empirical Asset Pricing via Machine Learning* — [Chicago Booth PDF](https://dachxiu.chicagobooth.edu/download/ML.pdf) · [NBER](https://www.nber.org/papers/w25398)
- 📖 Explainer: [QuantPedia](https://quantpedia.com/exploring-the-factor-zoo-with-a-machine-learning-portfolio/)

**3. LSTM & Transformers — learning from raw prices (12s)**
- LSTM adalah jaringan saraf yang "punya memori," dibuat buat baca urutan kayak harga harian. Transformer pakai "attention" — mesin yang sama di balik ChatGPT — buat nimbang data mana yang penting.
- Fischer & Krauss tes LSTM di seluruh S&P 500 (1992–2015): return 0,46%/hari sebelum biaya di backtest — tapi edge-nya nyaris hilang setelah 2010.
- *"Mesin bisa belajar pola tanpa lo kasih rumus. Tapi pola lama cepet di-arbitrage abis."*
- 📄 Paper: Fischer & Krauss (2018) — [European Journal of Operational Research](https://www.sciencedirect.com/science/article/abs/pii/S0377221717310652)
- 📄 Transformers: Lim, Arik, Loeff & Pfister (2021) — [arXiv 1912.09363](https://arxiv.org/abs/1912.09363)

**4. Deep RL Agents — FinRL & AlphaPortfolio (13s)**
- Reinforcement learning: agent dikasih reward kalau cuan, -dihukum kalau rugi, terus belajar action sendiri. FinRL ngebungkus DQN, PPO, A2C, DDPG jadi library siap pakai.
- AlphaPortfolio langsung optimize bobot portofolio (bukan nebak harga) pakai deep RL + attention — Sharpe di atas 2 dan alpha ~13% out-of-sample di paper-nya.
- *"Lo gak nulis strategi — lo nulis reward, sisanya agent yang nyari."*
- 📄 FinRL: Liu et al. (2020) — [arXiv 2011.09607](https://arxiv.org/abs/2011.09607) · [GitHub](https://github.com/AI4Finance-Foundation/FinRL)
- 📄 AlphaPortfolio: Cong, Tang, Wang & Zhang (2021) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3554486)

**5. RL Optimal Execution (10s)**
- Punya sinyal bagus aja gak cukup — cara lo masukin order gede bisa gerakin harga sendiri. RL belajar mecah order biar slippage minimum.
- Nevmyvaka, Feng & Kearns (2006): pakai 1,5 tahun data limit order NASDAQ skala milidetik, RL ngalahin strategi eksekusi baseline.
- *"Strategi cuan di kertas bisa boncos di eksekusi — di sini duit beneran ilang."*
- 📄 Paper: Nevmyvaka, Feng & Kearns (2006) — [UPenn PDF](https://www.cis.upenn.edu/~mkearns/papers/rlexec.pdf)

**6. The Honest Caveats (8s)**
- Hasil paper itu "gross" — belum dipotong biaya transaksi; banyak gain ML hilang begitu lo beneran trading. Crowding → alpha decay.
- *"R² gede di backtest itu gampang. Yang susah: tetep cuan setelah fee, slippage, dan ribuan orang niru."*
- 📄 Context: Feng, Giglio & Xiu (2020), *Taming the Factor Zoo* — [Chicago Booth PDF](https://dachxiu.chicagobooth.edu/download/ZOO.pdf)

**CTA (75–88s)**
> "Tapi ini semua makan angka. Gimana kalau AI bisa baca BERITA dan ngomongin keputusan kayak tim hedge fund beneran? That's the frontier — video terakhir. Follow dulu."

---

## Video 4 — "How Wall Street Reads Every Headline in Milliseconds"
*Covers: Surprise-vs-consensus framework, Fed statement word-parsing, commercial sentiment + fresh-vs-stale news, the GPT-4 trading study, LLM trading agents (TradingAgents/FinMem/FinGPT), the reality check*

**Core idea:** Wall Street doesn't read news like humans — they score it for relevance, novelty, and sentiment in under a second. And in 2023–2025 it escalated: GPT-4 predicting stocks, then whole *teams* of AI agents debating like a hedge fund. The papers report insane numbers... then most of them die. Here's the real story.

**Hook (0–5s)**
> "Ada AI yang baca semua berita di dunia dalam hitungan millisecond, terus trade berdasarkan itu. Ini hasilnya — dan kenapa kebanyakan mati."

**Explain (5–75s)**

**1. The Surprise-vs-Consensus Framework (12s)**
- Markets don't react to data — they react to how much it DIFFERS from what was already priced in. Expected 250k jobs, got 200k → market reads it as bad, even though 200k is decent.
- Ada Citi Economic Surprise Index yang ngetrack ini. Sama logikanya buat CPI, GDP, PMI — tiap rilis macro.
- *"Lo nggak trade the number. Lo trade the gap."*
- 📄 Paper: Andersen, Bollerslev, Diebold & Vega (2007) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=949180)
- 📖 Explainer: [Investment Monitor — Trading Surprise Data](https://www.investmentmonitor.ai/comment/how-to-trade-surprise-economic-data/)

**2. Fed Statement Word-Parsing (12s)**
- Quant funds bandingin statement the Fed kata-per-kata sama yang sebelumnya. "Patient" jadi "cautious" → hawkish.
- Satu studi: 75–90% pergerakan suku bunga jangka panjang datang dari perubahan KATA, bukan keputusan rate-nya.
- *"Bukan angka rate-nya yang penting. Kata-kata yang berubah yang gerakin market."*
- 📄 Paper: Gürkaynak, Sack & Swanson (2005) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=633281)
- 📖 Explainer: [VoxSmart — Decoding Powell](https://www.voxsmart.com/post/decoding-powell-the-dovish-turn-in-fed-communications)

**3. Sentiment Scoring + the Fresh-vs-Stale Rule (13s)**
- RavenPack & Bloomberg jual sistem yang skor tiap headline instan: relevan ke saham ini? Beneran baru? Tone-nya gimana? Feed ini ke hedge fund terbesar, $100k–$300k+/tahun.
- Temuan paling penting: reaksi ke berita FRESH → lanjut searah; reaksi ke berita STALE (daur ulang) → BALIK arah.
- *"Kalau semua orang udah tau beritanya, jangan follow the move — fade it."*
- 📄 Fresh-vs-stale: Tetlock (2011) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=1018221)
- 📖 RavenPack: [News Sentiment white paper](https://www.ravenpack.com/research/news-sentiment-everywhere/)

**4. The GPT-4 Trading Study (12s)**
- 2023: dua akademisi kasih headline harian ke GPT-4, minta prediksi arah saham. Sharpe ratio 3,28. Fund profesional target 1,0. Absurd.
- 2024: Sharpe-nya jeblok ke 1,22. Kenapa? Kebanyakan orang nemuin dan crowd trade yang sama.
- *"Edge-nya mati dalam 2 tahun. But the proof of concept is clear."*
- 📄 Paper: Lopez-Lira & Tang (2023) — [SSRN](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=4412788) · [arXiv](https://arxiv.org/abs/2304.07619)
- 📖 Explainer: [CNBC](https://www.cnbc.com/2023/04/12/chatgpt-may-be-able-to-predict-stock-movements-finance-professor-says.html)

**5. LLM Trading Agents — the 2024–25 frontier (13s)**
- Bukan satu prompt — tapi TIM agen LLM: analis fundamental, sentimen, plus peneliti Bull vs Bear yang literally debat sebelum trader-nya mutusin (TradingAgents). FinMem nambahin memori & refleksi; FinGPT open-source pakai RAG yang ngutip langsung dari filing & earnings call.
- TradingAgents lapor return kumulatif 26,6% & Sharpe 8,21 di window AAPL Jun–Nov 2024.
- *"Bukan satu AI nebak — tim AI berdebat dulu, baru ambil keputusan."*
- 📄 TradingAgents: Xiao et al. (2024) — [arXiv 2412.20138](https://arxiv.org/abs/2412.20138)
- 📄 FinGPT: Yang et al. (2023) — [arXiv 2306.06031](https://arxiv.org/abs/2306.06031) · [GitHub](https://github.com/AI4Finance-Foundation/FinGPT)

**6. The Reality Check (8s)**
- Audit 2025 nge-tes ulang FinMem & FinAgent: dengan pemilihan saham yang adil, plain Buy-and-Hold malah menang Sharpe (0,703). Banyak "edge"-nya dari ticker & window yang dipilih-pilih.
- *"Sharpe 8 di paper itu cantik — tapi hati-hati: banyak yang cuma backtest cherry-picked."*
- 📄 Audit: Ouyang et al. (2025) — [arXiv 2505.07078](https://arxiv.org/html/2505.07078v2)

**CTA (72–88s)**
> "Jadi itu dia — semua teknik yang gua temukan: dari earnings drift, alt-data satelit, machine learning, sampai AI agent yang baca berita. Di video selanjutnya, gua bakal pilih teknik mana yang menurut gua paling viable buat bot gua... dan gua jelasin kenapa. Stay tuned."

---

## Video 5 — "Here's the Bot I'm Actually Building (and Why I Killed 90% of the Ideas)"
*Covers: why speed-racing is dead on arrival, the honest time-horizon map, picking PEAD + EDGAR filings as the starting strategies, why paper trading first*

**Core idea:** Videos 1–4 covered everything Wall Street does. This is the pivot video — picking the 2 techniques that are actually viable for a solo retail bot, and explaining why everything else (HFT speed, Bloomberg feeds, RavenPack) is out of reach on purpose, not by accident.

**Hook (0–5s)**
> "Abis riset 4 video, gua cuma mau pake 2 dari semua teknik itu buat bot gua. Ini kenapa."

**Explain (5–75s)**

**1. Why You Can't Race the Spike (15s)**
- HFT firms sit inside the exchange itself, reading machine feeds in microseconds. A home bot on residential internet is 4–6 orders of magnitude slower — that's not a gap you close with better code.
- The honest map: 0–5s after news = owned by co-located firms. 5min–hours = viable for LLM-assisted reading. Days–weeks = where the real retail edge lives (drift).
- *"Gua nggak lawan mereka di detik pertama. Gua main di hari-hari sesudahnya, di mana mereka udah nggak peduli."*
- 📄 Source: `Research/05-bot-implementation-data-and-infrastructure.md` §3 (latency hierarchy)

**2. Pick #1 — PEAD / Earnings Drift (12s)**
- Scheduled, well-documented, doesn't need speed — a stock that beats earnings keeps drifting up for weeks. Easiest to backtest correctly with event-study methodology.
- *"Ini yang paling gampang gua validasi sebelum taruh duit beneran."*
- 📄 Source: `Research/01-earnings-and-corporate-events.md`; drift mechanics from Video 1

**3. Pick #2 — SEC EDGAR Filing Signals (12s)**
- 13D activist stakes and insider-buying clusters: free, public, sub-second latency from SEC's own feed, and a multi-week drift horizon — nobody's racing this one.
- *"Data-nya gratis, dan hedge fund BESAR pun harus lapor ke SEC. Gua tinggal baca."*
- 📄 Source: `Research/02-special-situations-and-filings.md`; `Research/05-...md` §2.3

**4. What Got Cut, and Why (13s)**
- Macro/Fed word-parsing: needs sub-second infra to matter. LLM trading agents (TradingAgents, FinMem): 2025 audit found plain buy-and-hold beat them once ticker-picking bias was removed.
- *"Bukan karena teknik-nya jelek — tapi karena solo builder nggak punya infra atau data buat menang di situ. Yet."*
- 📄 Source: `Research/04-nlp-sentiment-and-llm-signals.md`; Ouyang et al. (2025) audit cited in Video 4

**5. Paper Trade First, For Months (13s)**
- Alpaca gives unlimited free paper trading on the identical API used live — just flip a base URL. Paper fills are optimistic, so it's an upper bound, not proof.
- *"Kalau nggak survive di paper, jangan harap survive pake duit beneran."*
- 📄 Source: `Research/05-...md` §4.6

**CTA (75–88s)**
> "Jadi rencananya: PEAD buat entry yang gampang divalidasi, EDGAR filing buat sinyal yang murah dan jarang di-race orang. Video berikutnya — gua bongkar arsitektur botnya, dari data masuk sampai kill switch. Follow biar nggak ketinggalan."

---

## Video 6 — "Building the Bot: Data In, Trade Out, Nothing Blows Up"
*Covers: the 5-stage pipeline, dedup/novelty filtering, rules-before-ML signal design, the risk gate and kill switches, the 4-month build path*

**Core idea:** A news bot isn't one script — it's 5 steps in a row, like a factory line. Skip any step and there's a specific way you lose money. This video walks the 5 steps and the "emergency brakes" that stop a bug from wiping the account.

**Hook (0–5s)**
> "Bot trading itu bukan satu script — ada 5 langkah, dan tiap langkah lo skip, ada cara spesifik lo rugi."

**Explain (5–75s)**

**1. The 5 Steps (12s)**
- Simple chain: **baca berita → buang berita duplikat → cari saham yang kena → putusin buy/sell → cek aman dulu → baru eksekusi** (semua dicatat).
- Every step exists because someone lost real money without it.
- *"Ini bukan ribet-ribetan — tiap kotak ada karena ada cara spesifik buat rugi kalau nggak ada."*
- 📄 Source: `Research/05-...md` §4

**2. Duplicate News Filter — same story shows up 5–50 times (12s)**
- One event gets republished everywhere: original wire, aggregators, rewrites. Without a filter, the bot thinks "new news!" 50 times and trades the same event over and over.
- Simple rule: **news that's truly new → price keeps moving. News that's recycled → price often reverses.** Mix them up and you lose money directly.
- *"Kalau semua orang udah tau, itu bukan sinyal baru — itu jebakan."*
- 📄 Source: `Research/04-...md`; Tetlock (2011) fresh-vs-stale

**3. Simple Rules First, AI Later (12s)**
- Start with dumb-but-clear if-then rules, e.g. "IF merger announcement AND small company → buy." No AI yet.
- Why: you can explain every single trade. Pro trading desks work the same way — rules first.
- *"Kalau gua nggak bisa jelasin kenapa bot-nya trade, gua nggak percaya bot-nya."*
- 📄 Source: `Research/05-...md` §4.4

**4. The Safety Check & Kill Switch (14s)**
- Before ANY order goes out, bot checks: position not too big? daily loss limit not hit? stock not halted? not a duplicate order? Fail any check → order blocked.
- (Big brokers are legally forced to do this — SEC rule. We copy it ourselves.)
- If daily loss limit hit → bot sells everything and stops itself. Plus one command to kill it manually. All tested with fake money first.
- *"Bukan kalau bot-nya salah — tapi KAPAN. Kill switch nentuin seberapa mahal itu."*
- 📄 Source: `Research/05-...md` §6; SEC 15c3-5

**5. The 4-Month Plan (13s)**
- Month 1: collect news + save it (free sources). Month 2: duplicate filter + rules + safety check, add AI layer. Months 2–4: test against saved news history — honestly. Month 4+: small real money, only for rules that survived testing.
- Cost: $0–100/month. Pro tools (Bloomberg) are $100k+/year — for speed a solo bot can't use anyway.
- *"Gua nggak buru-buru ke duit beneran. Tiap tahap harus lolos dulu sebelum naik level."*
- 📄 Source: `Research/05-...md` §8

**CTA (75–88s)**
> "Itu rencana lengkapnya — dari mana datanya, gimana filternya, dan kapan gua berhenti kalau salah. Video selanjutnya gua mulai build beneran, mulai dari langkah pertama: baca berita. Follow biar bisa liat progress-nya."

---

# BUILD PHASE — one pipeline stage per video

Pipeline: `[Ingestion] → [Dedup/Novelty] → [Ticker mapping] → [Signal] → [Risk gate] → [Execution] → [Logging]`
(`Research/05-...md` §4). Video 7 = stage 1, video 8 = stage 2, and so on.

**Build-phase rules (differ from research reels):**
- **NO citation panels** — these are own-build-plan reels, top-band graphics + intro sign only (EDITING_GUIDE §10.6).
- Full viral pass every reel (EDITING_GUIDE §10): cold-open hook, jump-cut silences, punch alternation, SFX layer, hybrid meme layer.
- New intro-sign theme per episode (§5.5), landing on the spoken "Day N".
- Runtime 55–75s. Bilingual `.ass` subs, white ID top / yellow EN bottom.

---

## Video 7 — "Building the Bot for Real, Step 1: Reading the News (for Free)" · Day 8
*Covers: free news sources, websocket vs polling, normalize + store everything, why Month 1 has no trading*
**Folder:** `Trading Videos/Build Reel 3` · **Pipeline stage:** ingestion (`Research/05-...md` §2, §4.1)

**Core idea:** The pivot from planning to building. Day 7 promised "gua mulai build beneran, mulai dari
langkah pertama: baca berita." This delivers the ingestion layer — the whole Month 1 of the roadmap.

**Hook (0–5s)** — cold open, biggest lever
> "Bloomberg's news feed is a hundred thousand dollars a year. Gua bikin yang sama, buat bot gua, pake nol rupiah. Ini caranya."

*(payoff word: "nol rupiah" — hook overlay + cash-register/vine-boom SFX)*

**Intro / DAY 8 sign (~5–8s)**
> "Day 8. Kemarin gua gambar pipeline-nya di whiteboard. Hari ini kita mulai ngoding — langkah pertama: baca berita."

**Explain**

**1. The free backbone (14s)**
- Lo nggak butuh Bloomberg. Dua sumber gratis yang cukup buat mulai: **Alpaca News API** (isinya feed Benzinga — sama yang dipake trader pro — lewat websocket, gratis di akun paper), dan **SEC EDGAR** (tiap 8-K, 13D, Form 4 keluar sub-detik dari sumbernya, gratis, tinggal declare User-Agent, limit 10 request/detik).
- *"Berita yang gerakin saham kecil sering telat diliput manusia. Di situ edge-nya, dan datanya gratis."*
- Money contrast for the meme layer: Bloomberg B-PIPE ~$2,000/bln → Alpaca websocket $0.
- 📄 Source: `Research/05-...md` §2.2, §2.3

**2. Websocket beats polling (12s)**
- Dua cara ambil berita: **polling** (nanya server tiap X detik, "ada yang baru?") atau **websocket** (satu koneksi nyala terus, server yang dorong berita ke lo detik itu juga).
- Websocket menang: latency paling rendah yang bisa diraih retail, satu koneksi. Yang perlu diurus cuma reconnect kalau putus.
- *"Jangan nanya terus-terusan. Buka satu pintu, biar beritanya yang dateng ke lo."*
- 📄 Source: `Research/05-...md` §4.1

**3. Normalize + store everything (13s)**
- Tiap sumber formatnya beda. Ubah semua ke satu bentuk: `{source, published_at, headline, tickers, hash}`.
- Simpan **semua** mentah-mentahan, append-only, di SQLite. Belum di-trade — ini bahan buat backtest point-in-time nanti.
- *"Aturan nomor satu: simpen semua beritanya. Data yang lo buang hari ini, itu yang lo butuhin buat tes bulan depan."*
- 📄 Source: `Research/05-...md` §4.1, §5.2

**4. This is Month 1 (8s)**
- Nggak ada trading di video ini. Bulan pertama cuma: nyambung ke feed, tampung berita, simpen. Titik.
- *"Belum ada duit yang gerak. Fondasi dulu — kalau ingestion-nya bocor, semua di atasnya ikut bocor."*
- 📄 Source: `Research/05-...md` §8 step 1

**CTA (~6s)**
> "Sekarang beritanya masuk semua, tapi satu kejadian bisa muncul 50 kali. Video berikutnya: filter duplikat, biar bot-nya nggak trade berita yang sama berulang-ulang. Follow dulu."

**Edit plan**
- **Intro sign `slot_pvz_day8`** — terminal boot / news-wire CRT: green-phosphor cursor types `> CONNECTING TO FEED...`, ticker tape wipes across, headline slot resolves to **"DAY 8"** in monospace.
- **Top band:** `slot_gfx_feeds` (ALPACA + SEC EDGAR pills with green FREE tags, struck-through "BLOOMBERG $100k/yr" card + **NOPE.** stamp) · `slot_gfx_stream` (dim "POLL?" bubbles left vs one green open pipe right) · `slot_gfx_schema` (record types out row by row, raw headlines drop into an append-only **SQLite** box) · `slot_gfx_month1` (5-month timeline, Month 1 "COLLECT + STORE" lit, 2–5 dimmed — reuse `slot_gfx_roadmap` from Build reel 2).
- **Meme layer:** **STONKS** when $0 beats the $100k feed; **NOPE.** on the Bloomberg price card; damped-wiggle after the hook pill.
- **SFX:** cash register on "$0" (`money.mp3`, trim ~1.6s) · vine boom on sign land · swoosh on card slides · pop/mario-coin on rows · emotional-damage or womp-womp on NOPE. · ding on Month-1 light-up.
- **Code angle (b-roll):** `alpaca-py` news websocket subscribe → store each headline; EDGAR latest-filings poll with declared User-Agent (≤10 req/s); normalize both, INSERT append-only into `news.sqlite`. Filming the terminal streaming live headlines is the strong screen-record beat.

---

## Video 8 — "One Story, 50 Times: Stopping the Bot From Trading the Same News Twice" · Day 9
*Covers: why one event arrives 5–50 times, exact-hash dedup, fuzzy/embedding similarity, novelty vs the story chain*
**Folder:** `Trading Videos/Build Reel 4` · **Pipeline stage:** dedup / novelty (`Research/05-...md` §4.2)

**Core idea:** The Day 8 archive is full of the same event repeated. Without a filter, the bot reads
50 signals where a human sees one story.

**Hook (0–5s)**
> "Satu berita masuk lima puluh kali. Kalau bot gua nggak nyaring, dia beli saham yang sama lima puluh kali, pake duit yang sama. Ini cara nyetopnya."

*(payoff: "lima puluh kali" — hook overlay + rapid-fire pop/stutter stack)*

**Intro / DAY 9 sign (~5–8s)**
> "Day 9. Kemarin beritanya udah masuk semua ke database. Masalahnya sekarang: isinya duplikat."

**Explain**

**1. The problem (10s)**
- Satu kejadian, misal 8-K merger, keluar dari wire, di-copy aggregator, ditulis ulang sama 20 situs. Feed lo nerima semuanya sebagai berita "baru".
- *"Buat manusia jelas itu berita yang sama. Buat bot, itu lima puluh alasan buat beli."*
- 📄 Source: `Research/05-...md` §4.2

**2. Layer 1 — exact hash (11s)**
- Normalize dulu: lowercase, buang tanda baca, buang query string di URL. Baru di-`hash`.
- Hash sama = udah pernah liat = buang. Murah, instan, nangkep semua yang copy-paste persis. Tapi ganti satu kata, hash-nya beda total.

**3. Layer 2 — fuzzy / embedding similarity (12s)**
- Yang ditulis ulang lolos dari hash. Judulnya beda, artinya sama.
- MinHash atau sentence embedding + cosine similarity, dalam rolling window per ticker (misal 24 jam terakhir). Di atas threshold = cerita yang sama.
- *"Layer satu nangkep yang copy-paste. Layer dua nangkep yang parafrase."*

**4. Layer 3 — novelty vs the story chain (13s)**
- Paling penting, paling sering dilewatin: **informasi baru atau lanjutan?** "Perusahaan X diakuisisi" = baru. "Saham X naik setelah diakuisisi" = follow-up, nol informasi baru, harganya udah gerak.
- Bandingin sama N berita terakhir untuk ticker itu. Cuma item pertama yang beneran baru yang boleh jadi sinyal.
- Vendor mahal jual ini namanya "novelty score". Versi gratisnya: simpen chain-nya sendiri.
- 📄 Source: `Research/05-...md` §4.2 layer 3; fresh-vs-stale from Video 4 (Tetlock 2011)

**5. Why it's a money bug (8s)**
- Tanpa filter ini: posisi lo jadi 50x lipat dari yang lo mau, ukuran risiko lo bohong, dan lo masuk pas harganya udah abis gerak.
- *"Bug duplikat itu bukan bug kosmetik. Itu bug yang ngabisin duit."*

**CTA (~6s)**
> "Sekarang beritanya bersih, satu kejadian satu sinyal. Tapi bot gua masih bingung: 'Apple' itu saham AAPL, atau buah? Video berikutnya: nyocokin berita ke ticker yang bener. Follow dulu."

**Edit plan**
- **Intro sign `slot_pvz_day9`** — photocopier / echo stack: one headline card duplicates into 2, 8, 30 offset copies with rising shutter-clack, then a filter bar sweeps and collapses the stack to ONE card reading **"DAY 9"**.
- **Top band:** `slot_gfx_flood` (copies fan out, counter ticks **1 → 50**, fake outlet names) · `slot_gfx_hash` (two identical rows → same `a3f9…` hash, second gets **DUPLICATE** stamp and greys out) · `slot_gfx_fuzzy` (two differently-worded headlines, similarity meter fills to **0.94**, threshold 0.85 turns red) · `slot_gfx_novelty` (story chain: first item green **NEW**, items 2–4 grey **FOLLOW-UP**, only green drops into **SIGNAL**).
- **Meme layer:** **THE SAME 3 HEADLINES** on the flood counter; **DUPLICATE** / **NOPE.** stamps; damped-wiggle after the hook pill.
- **SFX:** pop stack / typewriter clatter under the hook vocal · shutter-clack + vine boom on sign land · mario-coin on hash match · womp-womp on DUPLICATE · ding on novelty green light.
- **Code angle (b-roll):** `hashlib.sha256` on the normalized headline + `INSERT OR IGNORE` on a UNIQUE column; `datasketch` MinHash **or** `sentence-transformers` cosine over a per-ticker 24h window; a `story_chain` table flagging `is_novel` only on the first item. On-screen proof: "1,204 events ingested → 173 unique stories."

---

## Video 9 — "'Apple' or an Apple? Teaching the Bot Which Stock the News Is About" · Day 10
*Covers: why entity mapping is hard, the security master (ticker ↔ CIK ↔ aliases), validating vendor tags, NER + confidence, the drop rule*
**Folder:** `Trading Videos/Build Reel 5` · **Pipeline stage:** entity / ticker mapping (`Research/05-...md` §4.3)

**Core idea:** A clean, novel story is still useless until it points at the right ticker — and company
names are not IDs.

**Hook (0–5s)**
> "Bot bisa baca berita sempurna, terus beli saham yang salah. Bukan bug di kodenya, bug di namanya. Serius."

*(payoff: "saham yang salah" — hook overlay + record scratch)*

**Intro / DAY 10 sign (~5–8s)**
> "Day 10. Beritanya udah bersih, nggak ada duplikat. Sekarang: berita ini sebenernya tentang saham yang mana?"

**Explain**

**1. Why it's harder than it looks (13s)**
- "Apple" bisa perusahaan, bisa buah. "Meta" bisa Facebook, bisa istilah crypto. "Delta" bisa maskapai, bisa varian.
- Anak perusahaan pake nama induk. ADR punya ticker beda buat perusahaan yang sama. Dan ticker bekas perusahaan yang delisting **dipake ulang** sama perusahaan lain.
- *"Nama perusahaan itu bukan ID. Bot butuh ID beneran."*
- 📄 Source: `Research/05-...md` §4.3

**2. The security master (12s)**
- Satu tabel jadi sumber kebenaran: **ticker ↔ CIK ↔ nama resmi ↔ alias**.
- CIK itu nomor ID perusahaan di SEC, dan dia nggak pernah ganti walaupun namanya atau tickernya ganti. Itu jangkarnya. Gratis dari EDGAR: `company_tickers.json`.
- Wajib **point-in-time**: simpen tanggal berlaku, biar berita 2023 dipetain ke perusahaan yang megang ticker itu di 2023, bukan yang sekarang.
- 📄 Source: `Research/05-...md` §4.3, §5.2

**3. Vendor tags lie (12s)**
- Feed bagus (Benzinga lewat Alpaca, Polygon) udah ngasih tag ticker sendiri. Pake, tapi **jangan percaya buta** — tagging vendor salah cukup sering, dan di studi pipeline LLM, mapping ticker yang jelek berulang kali jadi titik gagal utama.
- Validasi: tag vendor harus cocok sama security master. Nggak cocok = jangan trade, tandain buat direview.

**4. NER + confidence for untagged text (12s)**
- Buat teks tanpa tag (exhibit EDGAR, RSS mentah): NER buat narik nama perusahaan, cocokin ke tabel alias.
- Kalau pake LLM, suruh output ticker **plus confidence**, JSON ketat. Jangan cuma ticker.
- *"Modelnya boleh nebak. Yang nggak boleh: nebak diem-diem."*
- 📄 Source: `Research/05-...md` §4.3, §4.4

**5. The drop rule (8s)**
- Confidence rendah atau dua kandidat? **Buang beritanya.** Berita yang kelewat itu murah. Trade di ticker yang salah itu mahal.

**CTA (~6s)**
> "Sekarang tiap berita nyambung ke saham yang bener. Video berikutnya: kapan berita itu jadi sinyal beli — dan kenapa gua pake aturan biasa dulu, bukan AI. Follow dulu."

**Edit plan**
- **Intro sign `slot_pvz_day10`** — ID badge scanner: headline card slides under a scanner beam, red **UNKNOWN ENTITY** blinks, beam sweeps again, badge stamps out as a ticker-style plate reading **"DAY 10"**.
- **Top band:** `slot_gfx_ambiguous` (**APPLE** splits to a stock-chart card and a fruit card, "?" pulsing) · `slot_gfx_master` (table builds `AAPL | CIK 0000320193 | Apple Inc. | aliases…`, CIK column tagged **NEVER CHANGES**) · `slot_gfx_validate` (vendor pill checked against master: green **MATCH**, red **MISMATCH → HOLD**) · `slot_gfx_confidence` (`{ticker:"NVDA", confidence:0.91}` PASS, `{ticker:"DAL", confidence:0.42}` **DROPPED**).
- **Meme layer:** **BOUGHT THE FRUIT.** on the ambiguity card; **MISMATCH** / **DROPPED** stamps; damped-wiggle after the hook pill.
- **SFX:** record scratch on "saham yang salah" · scanner beep + vine boom on sign land · soft pop per table row (cap the run) · ding on MATCH · buzzer/womp-womp on MISMATCH and DROPPED.
- **Code angle (b-roll):** load EDGAR `company_tickers.json` into a `security_master` table (ticker, cik, name, aliases, valid_from); resolver = vendor tag → master lookup → mismatch flag, untagged text → `spaCy` NER → alias match; everything unresolved logged to `needs_review`. Filming that review table filling up is honest b-roll.

---

## Video 10 — "Boring Rules Beat the AI: How My Bot Decides a Headline Is Worth a Trade" · Day 11
*Covers: why LLM-as-oracle fails on auditability, 5–10 high-precision rules, LLM as structured extractor, the materiality/novelty/liquidity gates*
**Folder:** `Trading Videos/Build Reel 6` · **Pipeline stage:** signal generation (`Research/05-...md` §4.4)

**Core idea:** Clean, novel, correctly-tickered news still isn't a trade. Rules decide — auditable ones —
and the LLM works below them as an extractor, not an oracle.

**Hook (0–5s)**
> "Semua orang mau bot-nya pake AI buat mutusin beli. Meja event di Wall Street kebanyakan masih pake if-else. Ada alasannya."

*(payoff: "if-else" — hook overlay + vine boom)*

**Intro / DAY 11 sign (~5–8s)**
> "Day 11. Beritanya bersih dan udah nyambung ke ticker yang bener. Sekarang: kapan dia jadi sinyal?"

**Explain**

**1. Everyone starts with AI (10s)**
- Godaannya: lempar tiap headline ke LLM, tanya "beli atau jual". Gampang dibikin, gampang bocor duitnya.
- Masalahnya bukan pinter atau nggak — masalahnya **nggak bisa diaudit**. Kalau rugi, lo nggak tau alasannya, jadi lo nggak bisa benerin.
- *"Kalau lo nggak bisa jelasin kenapa bot lo beli, lo nggak punya strategi. Lo punya slot machine."*
- 📄 Source: `Research/05-...md` §4.4

**2. Rules first (12s)**
- Aturan itu presisi tinggi, murah, dan tiap keputusan ada jejaknya. Mulai dari 5 sampai 10 aturan aja, bukan seratus.
- Tiap aturan lahir dari satu jenis kejadian yang emang punya sejarah gerak — itu isi video 1 sampai 4.

**3. What a rule actually looks like (13s)**
- **8-K item 1.01** + frasa "merger agreement" + small cap + ada premium → kandidat.
- **Guidance withdrawn / suspended** di rilis earnings → kandidat sisi bawah.
- **FDA approval keywords** di 8-K biotek → kandidat.
- Tiap aturan nulis alasannya sendiri ke log: `rule_id`, teks yang match, ticker, timestamp.
- *"Aturannya boring. Boring itu yang bisa lo tes."*
- 📄 Source: `Research/01-...md`, `Research/02-...md` for the event types

**4. Where the LLM actually goes (12s)**
- Tetep dipake, tapi sebagai **extractor**, bukan peramal. Output JSON ketat: `{ticker, event_type, direction, magnitude, confidence, half_life}` — itu bahan buat aturan, bukan pengganti aturan.
- Pin versi modelnya, log tiap prompt sama jawabannya. Model ganti diem-diem = strategi lo ganti diem-diem.
- 📄 Source: `Research/05-...md` §4.4; LLM look-ahead caveats §5.3

**5. The three gates (10s)**
- Sebelum sinyal lanjut: **materiality** (kejadiannya cukup besar), **novelty** (beneran baru, dari filter Day 9), **liquidity** (sahamnya cukup rame buat dimasukin dan dikeluarin). Gagal satu, sinyalnya mati di situ.

**CTA (~6s)**
> "Sekarang bot gua udah bisa bilang 'ini layak dibeli'. Tapi belum ada satu order pun yang gua biarin keluar. Video berikutnya: risk gate sama kill switch, alias rem daruratnya. Follow dulu."

**Edit plan**
- **Intro sign `slot_pvz_day11`** — split-flap departure board clacking through `IF … THEN … ELSE …`, all flaps settling at once into **"DAY 11"**. Mechanical and boring on purpose, matches the thesis.
- **Top band:** `slot_gfx_blackbox` (headline enters a black box labeled **AI**, "BUY" pops out, a **WHY?** bounces off unanswered) · `slot_gfx_rules` (three readable rule cards, each tagged green **AUDITABLE**) · `slot_gfx_json` (extraction schema types out, **FEEDS THE RULES** arrow pointing into a rule card, not an order ticket) · `slot_gfx_gates` (**MATERIALITY → NOVELTY → LIQUIDITY**, token passes two, stopped red at the third).
- **Meme layer:** **TRUST ME BRO** as the black box's answer to WHY?; red **BLOCKED** stamp at the liquidity gate; damped-wiggle after the hook pill.
- **SFX:** vine boom on "if-else" · split-flap clatter on sign land (build from a fast pop/typewriter stack if no flap SFX in `Meme Audio/`) · swoosh + pop on rule cards · ding on gate pass · buzzer/emotional-damage on gate block.
- **Code angle (b-roll):** `rules.py` with 5 predicate functions returning `(fires, rule_id, evidence)`; run them over the Day 8–10 archive and show how few of N stored events actually fire — the low number is the point. Optional LLM extractor returning schema-validated JSON with the model version pinned in the log row. Strong beat: scrolling a `signals` table where every row has a human-readable `evidence` string.

---

## Not written yet (build phase continues)

- **Video 11 — risk gate + kill switch.** Pre-trade checks run synchronously before every order (position size, daily loss, halted/haltable, duplicate-order suppression), automatic flatten-and-halt, one-command manual kill, heartbeat watchdog. Mirrors SEC Rule 15c3-5. LULD halts = why limit orders only. 📄 `Research/05-...md` §4.5, §6
- **Video 12 — honest backtesting.** Event-study method on your own archive, point-in-time data, and the big one: **LLM look-ahead bias** — feeding 2021 headlines to a 2025-trained model measures memorization, and prompt tricks do NOT fix it. 📄 `Research/05-...md` §5
- **Video 13 — paper trading for months.** Alpaca paper on the identical API, paper fills as an upper bound, comparing realized paper entries to backtest assumptions, then tiny live capital. 📄 `Research/05-...md` §4.6, §8

---

## Series Notes

**Post order:** 1 → 2 → 3 → 4 → 5 → 6 → 7 → 8 → 9 → 10 → …
- Research half starts with the most intuitive & timeless (event-driven drift) → modern alt-data → machine learning → AI/LLM frontier
- Build half follows the pipeline in execution order, one stage per video
- Each video ends teasing the next, creating a natural series flow. **The order is locked by the on-camera CTAs** — video N names video N+1's topic out loud, so reels can't be reshuffled after shooting.

**Series arc:**
- Videos 1–4: "Here's everything Wall Street does — old and new" (research phase)
- Videos 5–6: "Here's what I picked, and the pipeline" (pivot)
- Videos 7+: "Here's me building each stage for real" (build phase)

**Modern-vs-classic weighting per video:**
- V1: classic edges that STILL run (anchors the series in proven techniques)
- V2: fully modern (alt-data, ~2012–present)
- V3: modern (ML/quant, ~2018–present)
- V4: classic framework → modern frontier (macro/sentiment → LLM agents 2023–2025)

**Meme cut placement per video:**
- V1: After "Ini bukan lucky. Ini systematic underreaction." (PEAD)
- V2: After "Mereka tau dari luar angkasa, lo masih nunggu berita." (satellite)
- V3: After "Lo gak nulis strategi — lo nulis reward, sisanya agent yang nyari."
- V4: After "Edge-nya mati dalam 2 tahun." (GPT-4 study)

**Bilingual rhythm:** Hook in Indonesian → technical explanation can mix EN/ID → key punchy insight lines back in Indonesian → CTA in Indonesian.

**Honesty note:** All headline numbers (Sharpe ratios, % returns) are in-sample/backtest figures from the cited papers — present them as "what researchers found," not promises. The recurring theme across all 4 videos: *edges decay when they get crowded.*
