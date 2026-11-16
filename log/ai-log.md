# AI-use log — sp500-vs-omxs30

## 2026-11-16 — History extended back to 1930 + margin-call ruin fix
- Asked: "Can you make the starting year from 1930?"; later "Test my website, validate it and modify it if you want" and "only for the S&P, modify it how you want".
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Read: index.html (SIM block), IDEA-CARD-less sources below, existing 1987-2024 arrays kept verbatim.
- Data sources actually opened and used for 1930-1986 (all cited in the page's methodology/references):
  1. A. Damodaran (NYU Stern), "Historical Returns on Stocks, Bonds and Bills: 1928-2024" — S&P 500 total return and 3-month T-bill, annual.
  2. D. Waldenström (2014), "Swedish stock and bond returns, 1856-2012", in Sveriges Riksbank, Historical Monetary and Financial Statistics for Sweden, vol 2, ch 6 — Tables A6.2 (Frennberg & Hansson stock price index + CPI index; dividends averaged ~4.2%/yr 1930-1986) and A6.3 (short-term government bill yield).
  3. Federal Reserve Bank of Minneapolis, "Consumer Price Index, 1913-" table — US CPI annual averages.
- Could not do: US average wages for 1951-1986 from SSA (site blocks automated downloads) and any Swedish wage series for 1930-1986 — so salary saving is honestly 0 before 1987 and this is disclosed in the slider help, assumptions and methodology. No numbers were invented anywhere.
- Changes to index.html (backups/backup-1.html = before, backup-2.html = after the data edit, both local only): YEARS extended to 1930-2024 (95 values/array); startYear slider min/default 1930; "Full history" preset → 1930; methodology, assumptions and references updated with the new sources and the 1987 splice seams (e.g. Damodaran 1987 S&P 500 TR 5.81% vs 5.25% in the modern table).
- Two model fixes found by edge-case testing: a leveraged pot that goes below zero now stays at 0 (margin call; card + takeaway say "WIPED OUT"), and the money-weighted return card shows "—" for a 0-year window (startYear 2024) instead of a meaningless solver bound.
- Validation: all script blocks pass Node syntax check; an independent Node recomputation of a 1930 lump-sum matched the page to the dollar ($7,637,517 US / $19,410,098 SE nominal); browser edge tests (200% leverage, startYear 2024, nominal/no-dividends) run with zero console errors.
- Deployment: committed and pushed to Emil200222/sp500-vs-omxs30; Vercel auto-deployed from the push (verified a new Ready production deployment appeared without a manual deploy command).

## 2026-11-16 — Model quality upgrade (second modification, "make the model as good as it can be")
- Asked: improve the simulation model as much as possible.
- Model: GLM-5.3-Flash Free (opencode), via OpenCode.
- Done (all inside the SIM block; backups/backup-3.html taken before the change):
  1. Swedish dividends 1930-1986 now use the year-by-year yields actually recorded in Riksbank vol 2, Table A6.2 (1.8-6.4%/yr, avg ~4.2%) instead of a constant estimate; the slider now applies only to 1987-2024 (help text and "Nominal dollars" preset updated).
  2. Salary contributions use the mid-year convention (they earn on average half of that year's return); the IRR solver was updated to match.
  3. New "Yearly fee" slider (default 0.2%, deducted from both pots; set 0 for pure market returns).
  4. New "Worst single year" card: worst calendar year incl. dividends + biggest peak-to-trough fall in real terms, per market.
  5. New "If history were reshuffled" card: 1,000 i.i.d. bootstrap runs of the chosen window's own years (fixed seed 2026, reproducible) — how often the US beats Sweden when the order of the years changes.
- Bugs found and fixed during verification: the bootstrap win-count compared US dollars with Swedish kronor, and printed the kronor median with a $ sign — both caught by an independent Node replica of the whole model (including the page's seeded RNG) before deployment, then fixed and re-verified.
- Validation: page matches the independent Node replica exactly on all 8 cards for three scenarios (default 1930, 200% leverage, single-year 2024 window) and the fee=0 baseline reproduces the previously verified $7,637,517; all script blocks pass syntax check; zero console errors in the browser.
- Deployment: push auto-deployed on Vercel (new Ready production build, no manual deploy); live site re-verified with the new cards.
