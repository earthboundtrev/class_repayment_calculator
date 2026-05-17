# Debt & Cashflow Visualizer

Personal finance dashboard for tracking **credit card and medical debt**, **employer education clawback**, **certification exam costs**, **income**, **Chime savings** (education repayment fund), and **HSA** contributions—with rolling projections and category filters.

**Live site:** [classrepaymentracker.netlify.app](https://classrepaymentracker.netlify.app)

---

## What it does

Single-page app (`index.html`) with no build step. All logic runs in the browser; data stays on your device unless you export it.

| Area | Purpose |
|------|---------|
| **Debt** | Credit cards and medical balances with APR, promo 0% end dates, and estimated minimum payments |
| **Education** | Company-reimbursed training and exams with voluntary-leave clawback (100% → 50% → 0%) |
| **Chime** | Separate high-yield account funded for education repayment; projected balance vs. required reserve |
| **HSA** | Employer per-paycheck deposits plus quarterly bonuses |
| **Cashflow** | Monthly money in vs. committed outflows (living expenses, Chime, HSA, debt mins, extra paydown) |
| **Certifications** | Cert study tracks, exam fees, reimbursed attempt limits per calendar year |

---

## Pages (sidebar)

### Dashboard
- Summary cards: total debt, gross monthly income, monthly debt service, Chime/HSA end-of-month balance, required reserve, surplus/shortfall
- **Debt & obligations** table (editable debts + education rows from expenses)
- **Income & cashflow** inputs (paycheck, other income, living expenses, extra debt payment, HSA)
- **What-if** Chime deposit slider (does not save settings; updates charts)
- **Certifications in progress** carousel (Education filter)
- Charts: Chime vs. required reserve; stacked debt payoff by category

### Cashflow
- Monthly **money in**, **committed out**, and **estimated surplus**
- Line-item income and outflows
- **Waterfall** chart (% of income per bucket)
- Respects the category filter (see below). Living expenses ($5k/mo default) appear when filter is **All**.

### Certifications
- Cert cards with status, exam fees, reimbursed attempts used (max **2 per exam per calendar year**)
- Log exam attempts; link to expenses

### Expenses
- Full table of incurred and **upcoming** education expenses
- Clawback %, amount owed today, 12- and 18-month milestone dates

### Chime & HSA
- Chime: balance, bi-weekly deposit amount, paychecks/month, APY, optional stop-deposit date
- HSA: balance, per-paycheck amount, paychecks/month, quarterly bonus, deposit schedule
- Projection summaries for current month

### Rolling Calendar
- 36–48 month horizontal month cards
- Chart: Chime balance, required reserve, total debt, HSA (toggle total debt line)
- What-if monthly deposit override (same idea as dashboard slider)

---

## Category filter

Sticky tabs on every page: **All** | **Education** | **Credit Cards** | **Health / Medical**

Filtering narrows summary cards, debt table, charts, and cashflow outflows. Income on the Cashflow page stays at full household gross; surplus notes explain when you are viewing a subset.

| Filter | Typical focus |
|--------|----------------|
| **All** | Full picture including $5k/mo living expenses on Cashflow |
| **Education** | Clawback reserve, Chime fund, upcoming exams |
| **Credit** | Card balances and minimum payments |
| **Health** | HSA balance vs. medical debt |

---

## Business rules

### Education clawback (voluntary leave)

Months counted from each expense **incurred date**:

| Elapsed time | You owe |
|--------------|---------|
| 0–12 months | 100% of cost |
| 12–18 months | 50% |
| 18+ months | $0 |

**Required reserve** = sum of clawback amounts across incurred education expenses (upcoming expenses do not count until their incurred date).

### Exam reimbursement
- **2 reimbursed attempts per exam per calendar year** (tracked on Certifications page)

### Income model
- **IT paycheck (bi-weekly)** — amount that lands in your main checking account (default **$3,651.65**; Chime transfers are separate)
- Gross monthly income = `biweekly × 2.166` + additional monthly income
- **Other monthly expenses** — living costs besides Chime, HSA, and debt (default **$5,000/mo**)
- **Extra monthly debt payment** — applied across balances in debt payoff projections

### Chime (education fund)
- Default: **$200** per paycheck, **2** paychecks/month → **$400/mo**, **3.5%** APY
- Bi-weekly deposit schedule from a configurable start date
- Surplus on dashboard = projected Chime balance − required education reserve

### HSA
- Default: **$300** per paycheck × **2**/month
- **$100** quarterly bonus at month-end in **March, June, September, December**
- Cashflow averages quarterly bonus as `bonus × 4 / 12` per month

### Debt simulation
- Card/medical debts accrue monthly interest (respects 0% promo end dates)
- Minimum payments estimated if not set manually
- Extra monthly debt payment reduces simulated balances in charts

---

## Default seed data

On first visit (empty `localStorage`), the app seeds example data:

| Debts | Balance | Notes |
|-------|---------|--------|
| AmEx Platinum | $14,774 | 18% APR |
| USAA Visa | $7,117 | 0% until 2025-12-18 |
| Medical — Urgent Care | $3,000 | $50/mo min |

Plus certifications (CISSP, AWS, Azure, GCP, Terraform, planned Cloud Cram tracks), historical exam expenses/attempts, and staggered **upcoming** exam dates.

You can edit, add, or delete everything after load.

---

## Data storage

All data is stored in the browser **`localStorage`** per device and origin.

| Key | Contents |
|-----|----------|
| `eduRepayment_expenses` | Education expenses (incurred + upcoming) |
| `eduRepayment_certifications` | Cert records |
| `eduRepayment_examAttempts` | Exam attempt log |
| `eduRepayment_debts` | Credit / health debts |
| `eduRepayment_income` | Paycheck, additional income, living expenses, extra debt pay |
| `eduRepayment_chime` | Chime account settings |
| `eduRepayment_hsa` | HSA settings |
| `eduRepayment_theme` | `light` / `dark` |
| `eduRepayment_dataVersion` | Schema version (currently **14**); migrations run on load |

### Export / import

Use **Export** / **Import** in the header (or footer controls) to back up or move data.

Export JSON shape:

```json
{
  "expenses": [],
  "certifications": [],
  "examAttempts": [],
  "debts": [],
  "incomeSettings": {},
  "chimeSettings": {},
  "hsaSettings": {}
}
```

Import replaces in-memory state and re-saves to `localStorage`.

---

## Tech stack

- [Tailwind CSS](https://tailwindcss.com/) (CDN)
- [Chart.js](https://www.chartjs.org/) 4.x
- [Lucide](https://lucide.dev/) icons
- Vanilla JavaScript, no bundler

---

## Run locally

```bash
# From repo root — any static server works
python -m http.server 8765
# Open http://localhost:8765/index.html
```

Or open `index.html` directly in a browser (some features work; `localStorage` is per-file if not served over HTTP).

---

## Deploy (Netlify)

This repo is configured for Netlify:

- **Publish directory:** `.` (repo root)
- **Build command:** none
- **Entry:** `index.html`

`netlify.toml` sets security headers (`X-Frame-Options`, `X-Content-Type-Options`).

Connect the GitHub repo to Netlify; pushes to `main` deploy automatically.

---

## Project layout

```
.
├── index.html      # Entire app (HTML + CSS + JS)
├── netlify.toml    # Netlify publish + headers
├── README.md
└── .gitignore
```

---

## Privacy

No backend, accounts, or analytics. Numbers never leave your browser except when you download an export file. Clear site data in the browser to reset.

---

## Repository

GitHub: [earthboundtrev/class_repayment_calculator](https://github.com/earthboundtrev/class_repayment_calculator)
