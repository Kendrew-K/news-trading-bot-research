# Video Plans — Trading Series (4 Research Reels)

These 4 videos are the RESEARCH SECTION — presenting the techniques Wall Street uses to find edge.
Weighted toward **modern (post-2000s) techniques**, but incorporating the classic edges where they still matter.
After these, separate videos will pick the best techniques and explain each in detail.

Style: bilingual EN/ID, personal journey, hook → explain techniques → CTA teasing the next phase.
Format: 60–90s Reels. Casual "lo/gua" Indonesian.

**Arc:** event-driven drift (timeless) → alternative data (modern) → machine learning & quant (modern) → AI reads the news (modern frontier).

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

**Core idea:** A news bot isn't one script — it's five stages that each fail in their own specific way if you skip them. This video walks the actual architecture, and the guardrails that stop a bug from becoming a blown account.

**Hook (0–5s)**
> "Bot trading itu bukan satu script — ada 5 tahap, dan tiap tahap punya cara gagalnya sendiri kalau lo skip."

**Explain (5–75s)**

**1. The Pipeline (12s)**
- Ingestion → Dedup/Novelty → Ticker mapping → Signal → Risk gate → Execution → Logging. Every stage exists because skipping it caused someone real losses.
- *"Ini bukan over-engineering — tiap kotak ini ada karena ada cara spesifik buat rugi kalau nggak ada."*
- 📄 Source: `Research/05-...md` §4

**2. Dedup & Novelty — the same story hits 5–50 times (12s)**
- Wire → aggregators → rewrites all republish the same news. Without novelty filtering, the bot "reacts" to the same event repeatedly.
- Fresh news → trend continues. Stale (recycled) news → often reverses. Confusing the two is a direct P&L bug.
- *"Kalau semua orang udah tau, itu bukan sinyal baru — itu jebakan."*
- 📄 Source: `Research/04-...md`; Tetlock (2011) fresh-vs-stale

**3. Rules Before ML (12s)**
- Start with high-precision, auditable rules — "8-K merger agreement + small cap" — before any ML or LLM layer. Most professional event desks are rules-heavy for exactly this reason: you can explain every trade.
- *"Kalau gua nggak bisa jelasin kenapa bot-nya trade, gua nggak percaya bot-nya."*
- 📄 Source: `Research/05-...md` §4.4

**4. The Risk Gate & Kill Switches (14s)**
- Every order passes a synchronous check first: position size caps, daily loss limit, halt detection, duplicate-order suppression. This is the same standard regulators force on broker-dealers (SEC Rule 15c3-5) — replicated client-side.
- Automatic flatten-and-halt on loss breach, plus a one-command manual kill. Tested in paper before it's trusted live.
- *"Bukan kalau bot-nya salah — tapi KAPAN. Kill switch nentuin seberapa mahal itu."*
- 📄 Source: `Research/05-...md` §6; SEC 15c3-5

**5. The Build Path (13s)**
- Weeks 1–2: ingestion + archive (Alpaca free news websocket + EDGAR poller). Weeks 3–4: dedup + rules. Month 2: LLM layer + risk gate. Months 2–4: honest evaluation against the archive. Month 4+: tiny live capital, only on what survived paper.
- Total starter cost: $0–100/month — the pro stack (Bloomberg/RavenPack) is $100k+/year for speed a solo bot can't use anyway.
- *"Gua nggak buru-buru ke duit beneran. Tiap tahap harus lolos dulu sebelum naik level."*
- 📄 Source: `Research/05-...md` §8

**CTA (75–88s)**
> "Itu rencana lengkapnya — dari mana datanya, gimana filternya, dan kapan gua berhenti kalau salah. Video selanjutnya gua mulai build beneran, mulai dari ingestion layer-nya. Follow biar bisa liat progress-nya."

---

## Series Notes

**Post order:** 1 → 2 → 3 → 4
- Starts with the most intuitive & timeless (event-driven drift) → escalates to modern alt-data → machine learning → AI/LLM frontier
- Each video ends teasing the next, creating a natural series flow

**Series arc:**
- Videos 1–4: "Here's everything Wall Street does — old and new" (research phase)
- Videos 5+: "Here's what I'm actually building into my bot and why" (build phase)

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
