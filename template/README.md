# CV template — how to tailor and rebuild

Everything needed to produce a tailored, one-page A4 CV (and matching cover letter)
for a new job posting. Hand this whole folder to a fresh session.

## Files

| File | What it is |
|---|---|
| `CV_source.md` | **Source of truth** for CV content. Edit this first. |
| `CV_template.html` | The layout. Must be kept in exact sync with the `.md`. |
| `Cover_Letter_source.md` | Cover letter text. |
| `Cover_Letter_template.html` | Single-column letter layout, same design tokens as the CV. |
| `README.md` | This file. |

Both HTML files currently carry the **Asana** palette and the ordering tailored to the
Asana *Product Designer, Coordinate* posting. Re-order for the new posting and swap the
accent colour — see below.

---

## Ground rules for tailoring (carry these over)

1. **Do not confabulate.** Use only skills, tools, metrics, employers and
   responsibilities already present in `CV_source.md`. No new competencies.
   If the posting needs something that isn't there, **ask** — don't invent it.
2. **Permitted changes only:** reorder and reword/reframe existing content so the
   most job-relevant items lead. That means skill list order, bullet order, the
   Profile paragraph, and section emphasis.
3. **Preserve every bolded metric.** Currently: **5 years**, **13 000+ ships**,
   **30k+ daily active users**, **70%**, **70+ active data points**, **30%**,
   **500+ employees**, **1000+ students**, **400+ attendees**, **15%**.
   Never drop one to make the page fit — tighten spacing instead.
4. **Bullets follow the outcome formula:** *accomplished [X] as measured by [Y], by
   doing [Z]*. Lead with the result, not the activity.
5. Keep tense consistent: Nacos Marine ended Dec 2025, so both roles are past tense.

---

## Layout invariants — verify these after every render

Two alignments are deliberate and must survive any edit:

- **Contacts line 1 and Profile line 1 share a baseline.** Tuned via
  `.profile { padding-top }`. If you change the Profile's `line-height` or
  `font-size`, re-measure and re-tune this value.
- **`Skills` and `Experience` headings share a baseline.** This is automatic —
  both columns are cells of the same CSS grid row (`.grid.band`).

Column geometry: `--col-left: 163pt`, `--col-gap: 20pt`, page padding
`28pt 37pt 20pt 36pt` on a 595.28 × 841.89pt page.

---

## Design tokens (swap the accent per company)

```css
--coral: #F06A6A;  /* Asana coral — CHANGE THIS for a different company */
--ink:   #151B26;  /* headings, names, bold metrics */
--body:  #3A4149;  /* body text */
--muted: #6F7782;  /* dates, meta */
```

The accent drives: section headings, the role line under the name, the `|`
separators in job titles, and the `–` bullet dashes. Changing `--coral` alone
re-themes the whole document.

Font is **Carlito** (metric-compatible with Calibri), carried over from the
original CV. Install with:

```bash
apt-get install -y fonts-crosextra-carlito
```

---

## Build

Headless Chromium (same Blink print engine as Edge):

```bash
/opt/pw-browsers/chromium-1194/chrome-linux/chrome \
  --headless --disable-gpu --no-sandbox --no-pdf-header-footer \
  --print-to-pdf=Gabriela_Penarska_Product_Designer_<JOB>.pdf \
  "file://$PWD/CV_template.html"
```

## Validate — it MUST be exactly one page

```bash
strings Gabriela_Penarska_Product_Designer_<JOB>.pdf | grep -E "/Count"
# expect: /Count 1
```

Fuller check, including both alignment pairs and the bottom margin:

```bash
python3 - <<'EOF'
import pypdfium2 as pdfium, collections
pdf = pdfium.PdfDocument('Gabriela_Penarska_Product_Designer_<JOB>.pdf')
pg = pdf[0]; tp = pg.get_textpage()
rows = collections.defaultdict(list)
for j in range(tp.count_chars()):
    l, b, r, t = tp.get_charbox(j, loose=False)
    ch = tp.get_text_range(j, 1)
    if ch.strip(): rows[round(b, 1)].append((l, ch))
ys = sorted(rows, reverse=True)
def base(sub, maxx=1000):
    for k in ys:
        it = sorted(rows[k])
        if sub in ''.join(c for _, c in it).replace(' ', '') and it[0][0] < maxx:
            return k
print('pages           :', len(pdf))
print('contacts+profile:', base('abea.easaa'))   # both must return the SAME value
print('Skills+Experien.:', base('kill', 60))     # one row containing both headings
print('lowest baseline :', min(ys))              # keep above ~35pt
EOF
```

`pypdfium2` is the extractor to use — `pypdf` and `pdfplumber` fail in this
environment on a broken `cryptography` binding.

### If it spills to two pages

Never drop a metric bullet. Tighten in this order, re-rendering each time:

1. `ul.bullets { line-height }` — 1.30 → 1.26 (biggest single win, ~12pt)
2. `ul.list { line-height }` — the left column's skill lists, 1.32 → 1.26
3. `.item { margin-top }` — 6pt → 5.5pt
4. `h2 { margin-bottom }` — 5.5pt → 4.5pt
5. `.band { margin-top }` — 10pt → 8pt

Whichever column is taller is the constraint — measure both before tightening:
the left column is currently the taller one.

---

## Preview as an image

```bash
python3 -c "
import pypdfium2 as pdfium
pdfium.PdfDocument('OUT.pdf')[0].render(scale=2).to_pil().save('preview.png')"
```

---

## Suggested output folder per application

```
<Company>_<Job_Title>/
  <Company>_<Job_Title>_Job_Posting.md      # posting + extracted priorities
  Gabriela_Penarska_Product_Designer_<JOB>.md
  Gabriela_Penarska_Product_Designer_<JOB>.html
  Gabriela_Penarska_Product_Designer_<JOB>.pdf
  Gabriela_Penarska_Cover_Letter_<JOB>.{md,html,pdf}
```

---

## Known open questions

Carry these forward — they were flagged but not resolved:

- The Nacos "increased engineer productivity and scoped down long manuals" bullet
  is the only one without a number.
- MSc thesis says "a wearable button" — confirm the correct device name.
- "Figma Make" is listed under AI tools; confirm that's the intended product.
- CV states 5 years; May 2021 → Dec 2025 is 4 years 8 months.
- The cover letter carries a hardcoded date (`11 August 2026`) — update or remove
  it before sending.
