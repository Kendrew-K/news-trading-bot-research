# News-Based Trading Research — Index

Research dossier on how professional traders, quants, and event-driven hedge funds trade **news and fundamentals** (no technical analysis), compiled to inform the design of an automated news-trading bot. Five parallel deep-dives, each with evidence, real numbers, bot implementation notes, and full source citations.

## The Files

| File | Domain | Headline techniques |
|---|---|---|
| [01-earnings-and-corporate-events.md](01-earnings-and-corporate-events.md) | Scheduled corporate news | PEAD (post-earnings announcement drift), earnings whisper numbers, guidance-cut asymmetry, analyst revision drift, index inclusion effect, dividend/buyback drift, options-implied expected move for sizing |
| [02-special-situations-and-filings.md](02-special-situations-and-filings.md) | Unscheduled / event-driven | Merger arbitrage, FDA/PDUFA catalysts and the biotech run-up effect, 13D activist drift, insider buying clusters, 8-K drift, short-seller report drift, spin-offs |
| [03-macro-news-and-central-banks.md](03-macro-news-and-central-banks.md) | Macro & central banks | Surprise-vs-consensus framework (CPI/NFP/FOMC), Fed statement word-diffing and hawk/dove scoring, nowcasting (GDPNow, Cleveland Fed CPI), geopolitical shock fade/ride rules, EPU/GPR risk-regime indices |
| [04-nlp-sentiment-and-llm-signals.md](04-nlp-sentiment-and-llm-signals.md) | Quant NLP / sentiment | RavenPack/Bloomberg-style relevance+novelty+sentiment scoring, fresh-vs-stale news (stale reactions reverse), Loughran-McDonald dictionary, FinBERT, GPT-4 signal studies and their decay, social-media crowding |
| [05-bot-implementation-data-and-infrastructure.md](05-bot-implementation-data-and-infrastructure.md) | Build it | News APIs ranked by latency/cost, the latency hierarchy (where retail can actually win), ingestion → dedup → ticker-mapping → signal → risk architecture, event-study backtesting, LLM look-ahead bias, halt/gap risk, kill switches, 4-month build path |

## Cross-Cutting Conclusions

These themes showed up independently in every domain:

1. **Don't race the spike — trade the drift.** The first milliseconds-to-seconds after news belong to co-located HFT firms paying six figures for machine-readable feeds. The durable, retail-accessible edges are *underreaction drifts* playing out over minutes to weeks: PEAD (days 2–60), analyst revision drift, 13D drift, short-report drift, post-FOMC repricing. Every file converges on this.
2. **Surprise is the signal, not the news itself.** Professionals trade the gap between the announcement and what was already priced in (consensus estimates, fed funds futures, whisper numbers, options-implied moves). A bot needs an *expectations layer*, not just a news feed.
3. **Novelty and relevance filtering is half the system.** Commercial systems (RavenPack et al.) earn their fees mostly by answering "is this new, and does it matter for this ticker?" Stale-news reactions tend to reverse; fresh-news reactions persist (Tetlock 2011).
4. **Negative news prices in slower than positive news** — short-side signals (guidance cuts, short reports, downgrade drift) are consistently stronger and slower, but carry borrow costs, squeeze risk, and negative skew.
5. **Edges decay when crowded.** 13D returns fell from ~16% to ~3%; the GPT-4 signal Sharpe fell from 3.28 to ~1.2 within two years; WSB predictability vanished post-GameStop; the index-inclusion effect went to zero then partially returned. Expect any published edge at a fraction of its paper numbers.
6. **Binary events demand binary-event sizing.** Pros risk 1–5% max on FDA decisions, earnings, and court rulings, sized off the options-implied expected move, with hard caps because gaps and halts make stop-losses fiction.
7. **Backtests of news strategies are usually overstated** — look-ahead bias (especially LLMs that "remember" history), survivorship bias, and unrealistic fill assumptions at the moment of news. Use point-in-time data and event-study methodology (detailed in file 05).

## Suggested Starting Point for the Bot

Per the build-priority rankings in files 01, 02, and 05, the highest edge-per-effort starting stack:

1. **Data (free tier):** Alpaca's free Benzinga news websocket + SEC EDGAR real-time feeds + Fed/BLS release calendars.
2. **First strategy:** PEAD / earnings-surprise drift — scheduled, well-documented, doesn't need speed, easy to backtest with event studies.
3. **Second strategy:** EDGAR filing signals (13D stakes, insider buying clusters) — near-free data, ~1-second latency achievable, drift horizon of weeks.
4. **Paper trade first** on Alpaca, with kill switches and per-event position caps from day one.

## Scope and disclaimer

This is a literature review, not a trading system and not advice. Every number
comes from the academic paper or practitioner source cited inside the file it
appears in; they are historical estimates from specific samples and periods,
not expected returns. Point 5 above is the important one: published edges decay
once they are published, and several of the ones documented here already have.
No code, no backtest results of my own, and nothing here has been traded.

*Compiled 2026-06-11.*
