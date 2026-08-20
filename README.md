# cv2 — tailored CVs

Per-application, one-page A4 CVs built from a single source of truth.

## Layout

| Path | What it is |
|---|---|
| `template/CV_source.md` | Source of truth for CV content. |
| `template/CV_template.html` | Base layout (Asana palette, Asana ordering). |
| `template/README.md` | How to tailor, build and validate. Read this first. |
| `template/previous_versions/` | Previously shipped tailored CVs (NordVPN, HelloFresh). |
| `<Company>_<Job_Title>/` | One folder per application. |

## Applications

| Folder | Role | Accent | Pages | Bottom baseline |
|---|---|---|---|---|
| `Tripadvisor_Senior_Product_Designer/` | Senior Product Designer, Travel Mode | `#00AA6C` | 1 | 35.0pt |

## Build

```bash
cd <Company>_<Job_Title>
/opt/pw-browsers/chromium-1194/chrome-linux/chrome \
  --headless --disable-gpu --no-sandbox --no-pdf-header-footer \
  --print-to-pdf=Gabriela_Penarska_Product_Designer_<JOB>.pdf \
  "file://$PWD/Gabriela_Penarska_Product_Designer_<JOB>.html"
```

Then run the validation block in `template/README.md` — the result must be
`/Count 1`, with the contacts/profile and Skills/Experience baseline pairs
each returning a single shared row.

Dependencies in a fresh session:

```bash
apt-get install -y fonts-crosextra-carlito
pip install pypdfium2 pillow
```

## Tripadvisor build notes

Content fit needed more room than the base template gives, so the tightening
ladder from `template/README.md` was applied in full plus a small amount more.
Deltas against `template/CV_template.html`:

| Rule | Base | Tripadvisor |
|---|---|---|
| `ul.bullets { line-height }` | 1.30 | 1.26 |
| `ul.list { line-height }` | 1.32 | 1.26 |
| `.item { margin-top }` | 6pt | 5pt |
| `ul.bullets li { margin-top }` | 2.4pt | 2.0pt |
| `h2 { margin-bottom }` | 5.5pt | 4.5pt |
| `.band { margin-top }` | 10pt | 8pt |
| `section + section { margin-top }` | 10pt | 8.5pt |
| `.edu-desc / .sub-note { line-height }` | 1.38 | 1.32 |
| `.sub-note { margin-top }` | 4.5pt | 3.5pt |

No metric was dropped to make the page fit; all ten bolded figures are present
in the rendered PDF.
