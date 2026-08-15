# cv2

Tailored CVs for Gabriela Penarska, built from a shared one-page A4 template.

## Layout

```
template/                                  # the reusable kit (see template/README.md)
  CV_source.md                             # source of truth for content
  CV_template.html                         # layout, Asana palette
  README.md                                # how to tailor, build and validate

NordVPN_Product_Designer/                  # one folder per application
  NordVPN_Product_Designer_Job_Posting.md  # posting + extracted priorities
  Gabriela_Penarska_Product_Designer_NordVPN.md
  Gabriela_Penarska_Product_Designer_NordVPN.html
  Gabriela_Penarska_Product_Designer_NordVPN.pdf
  preview.png
```

## Rebuild an application PDF

```bash
apt-get install -y fonts-crosextra-carlito
pip install pypdfium2 pillow

cd NordVPN_Product_Designer
/opt/pw-browsers/chromium-1194/chrome-linux/chrome \
  --headless --disable-gpu --no-sandbox --no-pdf-header-footer \
  --print-to-pdf=Gabriela_Penarska_Product_Designer_NordVPN.pdf \
  "file://$PWD/Gabriela_Penarska_Product_Designer_NordVPN.html"
```

Then run the validation script in `template/README.md` — it must report one page and
identical baselines for the contacts/profile and Skills/Experience pairs.
