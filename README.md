# AC Unmatched → WATI Builder

Internal lead-ops tool for Be10x. Takes the **abandoned cart (AC) export** and the **paid leads export**, removes every AC lead that already paid, and downloads the remaining **unmatched leads** as a WATI-ready broadcast CSV with zoom link fields.

Everything runs in the browser. No server, no upload — lead data never leaves your machine.

## How it works

1. Load two files:
   - **AC data** (`.xlsx` or `.csv`) — abandoned cart export with phone + email columns
   - **Paid leads** (`.csv` or `.xlsx`) — all paid leads export
2. Phone and email columns are auto-detected (override with the dropdowns if needed).
3. Every AC lead is checked against the paid file:
   - **Phone match** — compared on the last 10 digits, so `919876543210`, `9876543210` and `+91 98765 43210` all match each other
   - **Email match** (optional, on by default) — case-insensitive; switch to "Phone only" if not wanted
4. Matched leads are **excluded**. Duplicates and invalid phones are dropped.
5. **Test row**: enter your own CC + phone + email — it goes in as **row 1** of the file so the broadcast reaches you first for checking.
6. Only the unmatched leads (plus your test row on top) are written in the exact WATI import format:

```
Name,CountryCode,Phone,AllowBroadcast,AllowSMS,emailid,wsdt,time,calendar,zoomads,zoomid
,91,9876543210,TRUE,TRUE,you@be10x.com,21st June  | 11 AM,11.00 AM,Calendar-W1-21st-June,WN_xxxx,937 5516 5011   <- your test row
,91,7032084215,TRUE,TRUE,lead1@gmail.com,21st June  | 11 AM,11.00 AM,Calendar-W1-21st-June,WN_xxxx,937 5516 5011
```

- `Name` is left blank, `AllowBroadcast`/`AllowSMS` are `TRUE`, line endings are CRLF
- Country code is split out properly, including international numbers (91, 971, 92, 1, 44, 880 ...)
- File is named `<date> <group> <count> With ZL.csv`, e.g. `21st June G1A 6731 With ZL.csv` — the **count covers leads only**, the test row is not counted

## Campaign fields — all manual

| Field | Example |
|---|---|
| Workshop date label | `21st June` (used in the filename) |
| Group tag | `G1A` (used in the filename) |
| wsdt | `21st June  | 11 AM` |
| time | `11.00 AM` |
| calendar | `Calendar-W1-21st-June` |
| zoomads | `WN_xxxxxxxxxxxxxxxxxx` |
| zoomid | `937 5516 5011` |

Nothing is auto-generated — you type exactly what should appear in the file. If any field is left empty, the tool tells you which ones after processing.

## Run it

Open `index.html` in any modern browser. That's it.

## Publish on GitHub Pages

1. Create a new repository and push these files (`index.html`, `README.md`).
2. Repo → **Settings → Pages** → Source: **Deploy from a branch** → Branch: `main`, folder `/ (root)` → Save.
3. The tool goes live at `https://<username>.github.io/<repo-name>/`.

```bash
git init
git add index.html README.md
git commit -m "AC unmatched → WATI builder"
git branch -M main
git remote add origin https://github.com/<username>/<repo-name>.git
git push -u origin main
```

## Notes

- Handles large files fine (tested on a 28k-row paid export).
- The paid file's `phoneNumber` column and the AC file's `Phone Number` column are picked up automatically; any column can be selected manually.
