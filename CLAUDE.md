# Novus Expense Form app

Single-file mobile web app (`index.html`) used by Novus Aviation Capital employees to log travel expenses and send the Novus Expense Form PDF to accounting. Hosted on GitHub Pages from the `main` branch.

## Rules for changes
- Keep everything in `index.html` (HTML + CSS + JS inline). No build step, no frameworks.
- External scripts only: jsPDF 2.5.1 and jspdf-autotable 3.8.2 from cdnjs. Pin exact versions.
- Data stays on the employee's phone (IndexedDB for expenses + photos, localStorage for profile). Never add a server call or analytics.
- Brand: Novus navy #093C71, steel grey #97A4AF, logo embedded as base64 PNG in `.logo`. Don't replace the logo with a link.
- Profile holds only Name, Claim currency and FX rates (no dept, approver, accounting email or signature — removed by request).
- The PDF must keep the Novus Expense Form structure: NAME / PERIOD FROM / PERIOD TO header; columns DATE, DESCRIPTION, EXPENSE CATEGORY, AMOUNT, <claim ccy> CREDIT, INITIAL, CURRENCY, RECEIPT; SUBTOTALS, GRAND TOTAL, blank SIGNATURE and APPROVED BY lines (signed by hand); receipts two per page tagged R1, R2…
- Default FX rates: USD 3.6725, EUR 3.85 (to AED). Editable by the user in Profile.
- Must work on iPhone Safari and Android Chrome, installed to home screen. Test at 390px width.
- Changing the IndexedDB name or record shape loses users' data: migrate, never rename.
