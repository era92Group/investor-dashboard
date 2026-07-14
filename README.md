# era92 Fund — Investor Dashboard

Interactive dashboard for era92 Fund investors, showing H1 2026 performance for **era92 Finance** (microfinance) and **era92 Ventures** (92 Cafe, Car Wash & Detailing, 92 Active, era92 Hub).

**Live:** https://era92group.github.io/investor-dashboard/

---

## ⚠️ Data privacy — read before changing the data source

This dashboard reads from a **dedicated aggregate feed sheet** that contains **no borrower names and no loan-level records**. That is deliberate.

**Do NOT point `SHEET_ID` at the H1 master workbook.** The master contains 145 named borrowers with their loan balances and arrears status. Publishing it — which is what a live Google Sheets feed requires — would expose personal financial data of identifiable individuals and is a breach under Uganda's Data Protection and Privacy Act 2019.

| Sheet | Contents | Sharing |
|---|---|---|
| **Dashboard Feed** (`1SmVk45f...`) | Aggregates only | Anyone with link → **Viewer** |
| **H1 Master Workbook** (`1BLm3dCv...`) | Borrower-level detail | **Restricted** |

---

## Setup

The feed sheet must be readable by the gviz endpoint or the dashboard falls back to its embedded snapshot.

1. Open the **Dashboard Feed** sheet
2. **Share → General access → Anyone with the link**
3. Set the role to **Viewer** (never Editor)
4. Reload the dashboard — the status dot should turn green and read "Live"

If it stays amber ("Embedded snapshot"), the footer will tell you why. The figures shown are still correct as at the report date; they just aren't refreshing.

## Deploying

Already on GitHub Pages. To publish changes:

```bash
git add index.html README.md
git commit -m "Update H1 2026 data"
git push
```

Then **Settings → Pages** → source: `main` branch, root.

## Updating the data

Edit the **feed sheet** — not the HTML. Rows are `section,label,v1..v8`:

| Section | Shape |
|---|---|
| `META` | `label, value` — report period, valuation date, USD rate |
| `KPI` | `label, value` — headline metrics |
| `MONTH` | `month, cafe, wash, active, hub, trading, total, cogs, net` |
| `COHORT` | `cohort, loans, principal, interest, due, collected, outstanding, pct` |
| `PRODUCT` | `product, loans, principal, due, collected, outstanding, share` |
| `STATUS` | `status, loans, outstanding, overdue, share` |
| `AGEING` | `bucket, loans, overdue, share` |
| `INTEREST` | `period, value` |

The `FALLBACK` constant in `index.html` mirrors the feed exactly. If you change the feed's **schema** (not just values), update `parseFeed()` and `FALLBACK` too.

Run `node test.js` after any change — it extracts the real parser from `index.html` and asserts every figure against the verified H1 workbook.

## Known open items

These are surfaced in the UI rather than hidden. Don't remove the banners without resolving the underlying issue.

- **Loan interest income doesn't reconcile.** The Group Income Statement reports UGX 347,841,005 for H1 2026; the 145-loan book supports UGX 532,841,076 on an accrual basis. Variance: **185,000,071** — suspiciously close to a round 185M, which suggests a journal entry rather than a modelling difference. Unresolved.
- **Hub costs are not tracked separately.** Hub income is captured, but its payroll/utilities/establishment costs sit in pooled group expense lines. Hub contribution is stated *before* overhead and is not comparable to the trading-venture net margin. Fix: enable class tracking per venture in QuickBooks.
- **Period mismatch.** Financials run to 30 Jun 2026; the loan book is valued at 31 May 2026. June loan activity is not reflected.
- **Figures are unaudited** management accounts, corrected for variances against the Group Income Statement (92 Active was missing entirely — UGX 81.7M).

## Built with

- Chart.js 4.5 — visualisations
- Google Sheets gviz CSV endpoint — live data
- Pure HTML/CSS/JS, single file, no build step

Brand palette (from era92.com): teal `#0092b4` · orange `#fc5e27` · charcoal `#1f2027` · slate `#747a96`

---

*era92 Fund — Democratising Access to Capital*
*Prepared for Angelo Development Foundation · Confidential*
