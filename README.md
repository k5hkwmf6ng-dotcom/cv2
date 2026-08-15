# CV template — how to tailor and rebuild

Everything needed to produce a tailored, one-page A4 CV (and matching cover letter)
for a new job posting. Hand this whole folder to a fresh session.

## Files

| File | What it is |
|---|---|
| `CV_source.md` | **Source of truth** for CV content. Edit this first. |
| `CV_template.html` | The layout. Must be kept in exact sync with the `.md`. |
| `Cover_Letter_source.md` | Cover letter text. *(not present in this repo yet)* |
| `Cover_Letter_template.html` | Single-column letter layout, same design tokens as the CV. *(not present yet)* |
| `reference/` | Previously shipped tailorings (NordVPN, Allegro) — useful as style reference. |
| `<Company>_<Job_Title>/` | One folder per application; see the layout at the bottom. |
| `README.md` | This file. |

`CV_template.html` still carries the **Asana** palette and the ordering tailored to the
Asana *Product Designer, Coordinate* posting; it is the untouched base. Copy it into a new
application folder, re-order for the new posting and swap the accent colour — see below.

### Applications built so far

| Folder | Accent | Notes |
|---|---|---|
| `Bending_Spoons_Product_Designer/` | `#F06A6A` | WeTransfer/Bending Spoons. Their identity is black & white (Brandfetch) and a monochrome build was tried, but the candidate preferred the coral. Posting is AI-native (Cursor, Claude Code, "start with an LLM"); the profile names the AI tools without echoing the posting's wording. |

Content carried forward from the `reference/` PDFs and **not** in `CV_source.md` (approved in
earlier sessions — keep reusing, don't re-derive): the IA metrics **4–5 levels of nested menus
to 2** and **22 functions into 10**, the Green Challenge **8 weeks**, the Languages block
(Polish native / English fluent), and the skills `A/B testing & experimentation`,
`Prototyping`, `Wireframing & sketching`, `UI & visual design`, `Design systems & components`.

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
6. **No em dashes** anywhere in the CV text. En dashes are fine (date ranges, bullet markers).
7. **Don't echo the posting verbatim in the Profile.** Use its vocabulary, but keep the paragraph
   in the candidate's own register, close to the NordVPN and Allegro profiles in `reference/`.
   Posting language belongs in the experience bullets, where it is backed by outcomes.

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
re-themes the whole document. Newer builds name the token `--accent` instead of
`--coral`; same role. Accents used so far: Asana `#F06A6A`, NordVPN blue,
Allegro orange, Bending Spoons `#F06A6A` (coral kept by request).

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
the left column is currently the taller one. (In the Bending Spoons build the **right**
column is taller, so bullet wording and `ul.bullets` spacing were the levers.)

Reference target for the bottom margin: both shipped PDFs in `reference/` land at a lowest
baseline of **35.7pt**. Anything below ~35pt looks cramped; the Bending Spoons build sits
at 35.0pt.

**Blink does not always fragment the grid.** If `.grid.band` exceeds the page by even a
fraction, Chromium may move the *entire* band to page 2 instead of splitting it — so a
"2 pages" result can look catastrophic when you are only ~10pt over. Measure the overflow
directly instead of guessing, and leave 10–15pt of headroom:

```bash
# inject a measuring script into a scratch copy, then read it back
# bandBottom must stay below 821.9pt (841.89 - 20pt bottom padding)
```

```js
const p = v => (v*0.75).toFixed(1);                       // px → pt
const b = document.querySelector('.band');
JSON.stringify({ bandBottom: p(b.getBoundingClientRect().bottom), limit: 821.9,
                 main: p(document.querySelector('.main').getBoundingClientRect().height),
                 aside: p(document.querySelector('.aside').getBoundingClientRect().height) });
```

Run it with `chrome --headless --dump-dom` after stashing the result in an attribute.

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
  it before sending. (The cover-letter files were not supplied to the Bending Spoons
  session, so no letter was produced for that application.)
- No consumer-mobile-at-scale experience is claimed anywhere; product-led consumer
  companies (Bending Spoons, and others like it) will look for it. Nothing to invent —
  it's a portfolio job.
- `A/B testing & experimentation` is listed as a skill but no result is claimed against
  it. If a number exists, it would be one of the strongest bullets on the page.
