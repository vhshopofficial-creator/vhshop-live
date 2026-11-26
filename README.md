# VH SHOP live set — v1.2

v1.2: the extension saves every code's colour list from Vrich (`vhshop_vc:{CODE}`) whenever the code shows on the Sale/Monitor page. Prep shows those colours to pick from, marks each photo ✓ connected / ⚠ not a Vrich colour, and warns before saving if a photo won't connect to stock.

v1.1: extension works on the Vrich **Sale** page as well as Monitor · shoe sizes written in the code (14D36 …) are read as one code (14D) with sizes · picture cards slide only through colours still in stock (photo names must match Vrich colour names, e.g. ខ្មៅ) · new **Banner size** option on the host screen.

| File | Who | When | Job |
|---|---|---|---|
| `prep.html` | Admin | Before the live | Save photos, price, label and photo names per code |
| `host.html` | Host / admin | During the live | Send codes, position/label, price edits, banner Promo/Normal, overlay design and look, small live preview |
| `overlay.html` | OBS | During the live | The stream overlay (1080 × 1920, transparent) |
| `txt.json` | — | — | 5,834 codes from txt_live (price, label, colour rows) |
| `extension/` | Admin on Vrich | During the live | Chrome extension: search a code in Vrich → card pops up → Ctrl+Enter sends it |

Everything is separate from VHM: only `vhshop_` keys in Firebase vhm-catalog / kv. VHM's `live_*` keys are never read or written.

Practice mode on every page: add `?demo=1` to the link. Nothing is sent to Firebase or OBS.

---

## 1. Put the pages online (once)
1. GitHub → **New repository** → name `vhshop-live` → Public → Create.
2. **uploading an existing file** → drag `prep.html`, `host.html`, `overlay.html`, `txt.json`, `README.md` (the `extension` folder does not need to go online) → **Commit changes**.
3. **Settings → Pages** → Source: Deploy from a branch → `main` / `(root)` → Save. Wait ~1 minute.
4. Links (replace YOUR-NAME with the name in your VHM page links):
   - `https://YOUR-NAME.github.io/vhshop-live/prep.html`
   - `https://YOUR-NAME.github.io/vhshop-live/host.html`
   - `https://YOUR-NAME.github.io/vhshop-live/overlay.html`

Updating later: **Add file → Upload files** with the new files (same names). Links stay the same.

## 2. OBS (once)
Sources → **+** → **Browser** → name "VH SHOP overlay":
- URL: the `overlay.html` link
- Width **1080**, Height **1920**
- Tick **Refresh browser when scene becomes active**
- Custom CSS: leave the default (keeps the background transparent)

Check it in Chrome first: `overlay.html?bg=dark&fit=1&guides=1` shows it on a dark background with the 2/6 and 3/6 lines.

## 3. Chrome extension (once per laptop that runs the live)
1. Copy the `extension` folder somewhere it will stay (e.g. Documents/vhshop-extension).
2. Chrome → `chrome://extensions` → turn on **Developer mode** (top right).
3. **Load unpacked** → choose the `extension` folder.
4. Open the VH SHOP Vrich **Sale** or **Monitor** page. A **VH SHOP** button appears bottom-right.

It only wakes up on pages that show Vrich's stock list; on other websites it does nothing.
Updating later: replace the folder's files, then press ↻ on the extension in `chrome://extensions`.

## Daily use
**Before the live — Prep (admin):** first search the code once on the Vrich Sale page (extension on) so Prep gets its colours → type code → **ប្រើ · Use** → add photos (drag from Telegram Desktop / Ctrl+V) → tap the colour chips → **Save & next code**. Before the live open **List** and fix anything yellow.

**During the live:**
- **Vrich + extension (admin):** type a code in the Vrich search → the card pops up → choose position (Alt+1 Auto · Alt+2 លើខ្លួន · Alt+3 លើដៃ · Alt+4 ខាងក្រោយ · Alt+5 ADMIN) or pick a label → **Ctrl+Enter**. Colours come from Vrich stock (sold-out colours left off). Picture cards hide the photos of sold-out colours; after sales press **Refresh stock** to update. The VH SHOP button bottom-right: what's on stream, ✕ to remove, **Refresh stock**.
- **Host screen:** see every card, change price or label/position, 📌 notes (host only), remove cards, Promo/Normal banner, design, tint, card size, slide motion, seconds per colour. Codes can also be sent from here (colours then come from the txt data).

Rules: code with photos (saved on Prep) → picture card (max 3, newest on top, oldest drops off). No photos → text card. If text cards reach the picture cards, the oldest text card hides. Codes with more than 6 colour rows show them in 2 columns.

## Data (Firebase vhm-catalog, collection kv)
- `vhshop_code:{CODE}` `{code, price, desc, prefix, rows, photos:[{id,v}], status, updatedAt}` — Prep
- `vhshop_img:{CODE}:{id}` `{img}` — one photo (~40 KB) — Prep
- `vhshop_index` `{codes:{CODE:{price,desc,n,status,thumb,updatedAt}}}` — Prep
- `vhshop_overlay_pics` `{items:[{code,label,price,rows,at}]}` — host + extension
- `vhshop_overlay_text` `{items:[{code,label,price,rows,at}]}` — host + extension
- `vhshop_settings` `{theme,tint,size,bnSize,motion,sec,banner,titles,badge}` — host
- `vhshop_vc:{CODE}` `{code, colors:[...], at}` — colours seen on Vrich — extension (read by Prep)
- `vhshop_host_notes` `{notes:{CODE:text}}` — host

Same document shape as VHM (`fields.data` = JSON string + `fields.updatedAt`).
