# Porter's Forecast — self-updating NVDA page

A single static page (`index.html`) that shows the Nvidia "Porter Price," with the **current price** and **potential upside** refreshing about every 15 minutes. No server to run — GitHub Pages hosts the page, and a scheduled GitHub Action keeps the price fresh.

## How it works
- **GitHub Pages** serves `index.html` and `price.json`.
- **The Action** (`.github/workflows/update-price.yml`) runs on a schedule, calls the Fiscal.ai API using your key (stored as a repo secret), and writes the latest price into `price.json`.
- **The page** re-reads `price.json` every 15 minutes in the browser, so visitors see the number update without reloading. Intrinsic Value and Porter Price stay fixed (they only change at earnings).

## One-time setup (about 5 minutes)
1. **Create a repo** and add these files (keep the folder structure — the workflow must stay at `.github/workflows/update-price.yml`).
2. **Add your Fiscal.ai key as a secret:** repo **Settings → Secrets and variables → Actions → New repository secret**. Name it exactly `FISCAL_API_KEY`, paste your key as the value. *(Never put the key in any file — only here.)*
3. **Turn on Pages:** **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `/ (root)`.** Note the URL it gives you.
4. **Seed the first price:** go to the **Actions** tab, enable workflows if prompted, open **"Update NVDA price," → Run workflow.** It should update `price.json`.
5. **Open your Pages URL.** The page shows the live price and will refresh every ~15 minutes; the Action refreshes the data every ~15 minutes during US market hours.

## Notes
- **`endpoint` is already set** to the relative path `price.json`, which works when the page and JSON are served from the same site (the normal case). If you host `index.html` on a *different* domain than the repo, change `endpoint` in `index.html` to the absolute Pages URL of `price.json`.
- **Custom domain (portersforecast.com):** **Settings → Pages → Custom domain**, then add the DNS records GitHub shows. The relative `price.json` path keeps working.
- **If the Action fails to parse a price:** open the failed run's log — it prints Fiscal.ai's raw response (price data only, never your key). Adjust the `jq` line (`.price // .close // .last`) to match the real field name, or send it to me and I'll fix it.
- **Timing:** GitHub's scheduled Actions are best-effort and can run a few minutes late — fine for a marketing page, just not to-the-second.
- **Change the ticker / target:** edit `companyKey=NASDAQ_NVDA` in the workflow, and `PORTER_PRICE` / `FALLBACK_PRICE` in `index.html`.

## Heads-up: the upside crossover
Potential upside is measured against the Porter Price (**$261.20**). If NVDA ever trades **above** that, the upside turns negative and the page's "undervalued / bargain" framing no longer fits. Decide how you want to handle that before it happens (accept it, swap the messaging, or pause the campaign).
