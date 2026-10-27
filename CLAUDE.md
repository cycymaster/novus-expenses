# Novus Expense Form app

Single-file mobile web app (`index.html`) used by Novus Aviation Capital employees to log travel expenses and send the Novus Expense Form PDF to accounting. Hosted on GitHub Pages from the `main` branch.

## User flow (keep it this simple)
1. **Scan receipt** (centre tab) opens the camera directly.
2. The receipt is read on the phone (Tesseract OCR) and the form pre-fills amount, currency, date, category, merchant. Pre-filled fields are highlighted "READ".
3. Employee checks, adds the **reason** (required), taps **Add to report**.
4. **In progress** tab = the one current report. **Send report** → Create PDF → Email (share sheet). On success the report moves to **Sent** and a new empty report starts.
5. **Sent** tab = history of sent reports (number EXP-<initials>-<YYMM>-<nn>, PDF re-download, email again, "move back to In progress" if never sent).

## Rules for changes
- Keep everything in `index.html` (HTML + CSS + JS inline). No build step, no frameworks.
- External scripts only: jsPDF 2.5.1 and jspdf-autotable 3.8.2 (cdnjs), tesseract.js 5.1.1 (jsDelivr, loaded lazily; it fetches its wasm core and English data from jsDelivr). Pin exact versions.
- Data stays on the employee's phone: IndexedDB `novus-expenses` v2 (stores `expenses`, `reports`), localStorage `novus-profile`. OCR runs on the phone. Never add a server call, upload or analytics.
- Expense record: id, created, date, category, merchant, reason, desc, amount, currency, claim, claimCcy, photo, reportId (absent = in progress). Old records may only have `desc`: read via reasonOf()/merchantOf().
- Changing the IndexedDB name or record shape loses users' data: bump the version and migrate, never rename.
- Profile holds only Name, Claim currency and FX rates.
- Brand: Novus navy #093C71, steel grey #97A4AF, logo embedded as base64 PNG in `.logo`. Don't replace the logo with a link.
- The PDF keeps the Novus Expense Form structure: logo + EXPENSE FORM + report number; NAME / PERIOD FROM / PERIOD TO header; columns DATE, DESCRIPTION, EXPENSE CATEGORY, AMOUNT, <claim ccy> CREDIT, INITIAL, CURRENCY, RECEIPT; SUBTOTALS, GRAND TOTAL, blank SIGNATURE and APPROVED BY lines; receipts two per page tagged R1, R2…
- Default FX rates: USD 3.6725, EUR 3.85 (to AED). Editable in Profile.
- Must work on iPhone Safari and Android Chrome, installed to home screen. Test at 390px width.
- `navigator.share` must run from a tap: keep PDF creation and Email as two separate buttons.
