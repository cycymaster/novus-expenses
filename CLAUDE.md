# Novus Expense Form app

Single-file mobile web app (`index.html`) used by Novus Aviation Capital employees to log travel expenses and send the Novus Expense Form PDF to accounting. Hosted on GitHub Pages from the `main` branch.

## User flow (keep it this simple)
1. **Scan receipt** (centre tab) opens the camera. The receipt is read on the phone (Tesseract OCR) and the form pre-fills amount, currency, date, category, merchant (highlighted "READ"). Employee adds the **reason** (required), optionally picks a folder, saves.
2. **Expenses** tab = unfiled expenses (no folder). **Select** → pick items → **Move to…** a folder (or create one, suggested name = current month, e.g. "Oct 2026") or **Delete**.
3. **Folders** tab = all folders, kept indefinitely, filterable by status. Each folder: total, period, manual status **Pending / Sent / Paid** (the app is not linked to accounting), **Create PDF** → saved on the phone and opened in the in-app viewer; each saved PDF has **View / Download / Send / Delete** and shows Up to date / Outdated, select items to move to another folder / back to Expenses / delete, rename, delete folder (its expenses go back to Expenses).
4. If a folder is changed after being marked Sent/Paid, a warning asks to recreate and resend the PDF.

## Rules for changes
- Keep everything in `index.html` (HTML + CSS + JS inline). No build step, no frameworks.
- External scripts only, all loaded on demand with exact versions: jsPDF 2.5.1 + jspdf-autotable 3.8.2 (jsDelivr → unpkg → cdnjs fallback, see PDF_SRC/ensurePDF), tesseract.js 5.1.1 (jsDelivr), pdfjs-dist 3.11.174 for the in-app viewer (jsDelivr → unpkg → cdnjs).
- Data stays on the employee's phone: IndexedDB `novus-expenses` v4 (stores `expenses`, `folders`, `pdfs` {id, folderId, name, created, size, buf ArrayBuffer, sig, count, total, ccy}; legacy `reports` kept and migrated into folders), localStorage `novus-profile`. OCR runs on the phone. Never add a server call, upload or analytics.
- Expense record: id, created, date, category, merchant, reason, desc, amount, currency, claim, claimCcy, photo, folderId (absent = unfiled). Folder record: id, name, no (EXP-<initials>-<YYMM>-<nn>), status pending|sent|paid, statusAt, created, pdfSig. Old records may only have `desc`: read via reasonOf()/merchantOf().
- Changing the IndexedDB name or record shape loses users' data: bump the version and migrate, never rename.
- Profile holds Name, Claim currency, FX rates and a finger-drawn signature (PNG in profile.sig), printed in the PDF SIGNATURE box.
- Brand: Novus navy #093C71, steel grey #97A4AF, logo embedded as base64 PNG in `.logo`. Don't replace the logo with a link.
- The PDF keeps the Novus Expense Form structure: logo + EXPENSE FORM + folder name and number; NAME / PERIOD FROM / PERIOD TO header; columns DATE, DESCRIPTION, EXPENSE CATEGORY, AMOUNT, <claim ccy> CREDIT, INITIAL, CURRENCY, RECEIPT; SUBTOTALS, GRAND TOTAL, SIGNATURE (employee signature image if saved) and blank APPROVED BY line; receipts two per page tagged R1, R2…
- Default FX rates: USD 3.6725, EUR 3.85 (to AED). Editable in Profile.
- Must work on iPhone Safari and Android Chrome, installed to home screen. Test at 390px width.
- `navigator.share` must run from a tap: keep PDF creation and Email as two separate buttons.
