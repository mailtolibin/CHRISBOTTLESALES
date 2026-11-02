# Daily Sale Manager

A single-page app (`index.html`, no build step) that replaces the daily-sale Excel workbook of
Chris Bottle Crafters: bill entry, pending bills, cash count, expenses, staff advances,
incentive records, dashboard and printable PDF reports.

> **Private repository recommended.** The app holds no data itself, but never commit exports
> of sales, customers, expenses or staff advances. `.gitignore` already blocks common export types.

## Business rules built in

| Entry type | Counts in daily sale / cash | Counts for salesman incentive |
|---|---|---|
| `SALE` (paid in full) | yes | yes |
| `ADV` (advance / part payment) | yes | **no** |
| `INV` | no | yes |

* Payment modes: CASH, DLAKSHMI, G PAY, PHPAY, YB POS, CARD, YB ACCOUNT, HDFC,
  **PAID EARLIER** (already counted earlier: ignored everywhere), **NP** (not paid: goes to Pending bills).
* **Settle** on a pending bill records the last payment as `SALE` and earlier advances as `INV`,
  so the salesman's incentive gets the full bill amount.
* Retail and wholesale are kept separate for every salesperson (separate incentive records).
* One bill can be split over several payment modes (several rows with the same invoice number).
* Expenses carry three separate choices: type (Company / Personal / Advance + staff),
  category (RENT, WATER, LABOUR, BATA, OVERTIME, STATIONERY, DONATION, PARTY FUND,
  DIESEL (PICKUP), OTHER) and paid from (CASH IN HAND, DLAKSHMI, CUPBOARD, ANNA).
  Only CASH IN HAND reduces the day's cash in hand.

## Tabs

Dashboard (with salesperson pie chart) · Entry · Day report · Pending bills · Incentive ·
Month · Report (PDF downloads: day report, bill-wise sale, staff advance statement,
retail/wholesale incentive record, expense report).

## Where the data lives

The app was built to run as a claude.ai artifact and uses its runtime (`claude.use('db')`,
`claude.use('downloads')`, `claude.use('user')`) for storage, PDF download and view-only access.
Collections: `entries`, `expenses`, `days`.

* **Inside claude.ai:** works fully (shared live data, view-only for viewers).
* **Opened as a plain web page** (for example from this repo): the claude runtime is missing, so it falls back
  to in-memory storage. Everything works, but data is lost when the page closes and PDF downloads are unavailable.

## Making it a standalone website (planned)

Replace the `db` / `downloads` / `user` calls with a real backend (for example Supabase or Firebase:
database + login with a view-only role) and host `index.html` on any static host. The data helpers are
isolated at the top of the script (`add`, `upd`, `del`, `setDay`, and the `onSnapshot` block at the bottom),
so the change is small.

## External scripts

`jsPDF 2.5.1` and `jspdf-autotable 3.8.2` are loaded from cdnjs for PDF reports.
