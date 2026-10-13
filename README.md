# Certificate Generator - Part 1: Core Certificate Designer

A free, offline tool for placing text on a certificate background, previewing it,
and exporting a high-quality **PNG** and **PDF**. Design templates can be saved
and loaded as small JSON files.

It runs entirely on your computer (Python + Streamlit in your local browser).
No login, no cloud services, no paid APIs, no internet needed once installed.

> This is **Part 1 only**. Excel/CSV batch generation, certificate records,
> search and bulk ZIP export come in later parts (see `CHECKPOINT.md`).

---

## 1. Setup on Windows (VS Code)

You need **Python 3.10 or newer** from <https://www.python.org/downloads/>
(tick *"Add python.exe to PATH"* during installation).

1. Unzip `CertificateGenerator_Part1.zip` and open the `CertificateGenerator`
   folder in VS Code (**File > Open Folder...**).
2. Open a terminal in VS Code (**Terminal > New Terminal**).
3. Create a virtual environment and install the packages:

   ```bat
   python -m venv .venv
   .venv\Scripts\activate
   pip install -r requirements.txt
   ```

   If PowerShell refuses to run `activate`, either run
   `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` first, or switch the
   terminal to *Command Prompt* (the dropdown next to the **+** button).
   If you see a "Select interpreter" prompt in VS Code, choose the one inside `.venv`.

## 2. Launch the app

With the virtual environment active, in the project folder:

```bat
python -m streamlit run app.py
```

Your browser opens at <http://localhost:8501>. Press `Ctrl+C` in the terminal to stop.

Shortcut: double-click `run_app.bat` (uses whichever `python` is on your PATH, so
install the packages into it, or activate the venv first).

## 3. Quick tour (2 minutes)

1. Press **Load sample certificate** in the sidebar - a demo background with three fields appears.
2. Change a field's sample text, size, color or alignment and watch the preview update.
3. Press **Generate certificate** in section 4. Files go to the `output` folder (or any folder you type).

## 4. Using your own certificate

1. **Background** - upload PNG, JPG/JPEG or PDF in the sidebar. A PDF's first page is converted
   to a 300 DPI image (needs the `pymupdf` package, included in `requirements.txt`). The width,
   height and aspect ratio are shown.
2. **Text fields** - press **Add text field**, then set:
   field name, sample text, X, Y, font, font size, color, alignment, maximum width and
   *Shrink text to fit*.
3. **Preview** - shows your design with the text rendered exactly as it will be exported.
   Blue/red boxes and crosses are preview-only guides (untick *Show position guides* to hide them).
   Warnings appear if text is cut off or does not fit.
4. **Export** - choose PNG and/or PDF, an output folder, a file name and a DPI, then **Generate certificate**.
5. **Templates** - *Save template* writes a JSON file to `data/templates`; you can also download it,
   and later load it from the saved list or by uploading the file.

### Coordinate system

* Units are **pixels of the original background image**.
* **(0, 0) is the top-left corner**; X increases to the right, Y increases downward.
* **X** is the left edge (left alignment), the middle (center) or the right edge (right alignment) of the text.
* **Y** is the top of the text.
* To find a position, open your background in any image editor (Paint shows the cursor position
  at the bottom) and read the pixel coordinates - or just adjust X/Y and watch the preview.
  *Center horizontally* sets X to the middle of the image and alignment to center.

### Long names

Set a **Maximum width** and tick **Shrink text to fit**: the font is reduced (never below 6 px)
until the text fits. Without *Shrink to fit*, over-long text produces a warning instead.

### Fonts

Built-in choices: Sans Serif, Sans Serif Bold, Serif, Serif Bold, Serif Italic, Monospace
(Arial / Times New Roman / Courier New on Windows, with Liberation/DejaVu fallbacks on other systems).
To use your own, upload a `.ttf`/`.otf` under **Custom fonts**, or copy it into the `fonts` folder.
A template remembers the font *file name*, so keep the font in `fonts` when moving a template to another computer.

### Templates and the background file

A template JSON stores your fields plus a **reference** (path, file name and pixel size) to the background -
the image is **not** embedded, to keep the file small. The background file must stay available:
uploads are copied to `data/backgrounds`, and templates look there first. If the file is missing when a
template is loaded, the fields still load and you are asked to upload the background again.
If the new background has a different pixel size, a warning reminds you to check the positions.

## 5. Run the automated tests

```bat
pip install -r requirements-dev.txt
python -m pytest
```

## 6. Files

| File | Purpose |
|------|---------|
| `app.py` | Streamlit interface only (sidebar, field editor, preview, export). |
| `certificate_renderer.py` | Rendering engine: field model, alignment / fit-to-width / out-of-canvas layout, text drawing, preview scaling, background loading (PNG/JPG/PDF), PNG + PDF export, safe file names, sample data. No Streamlit code. |
| `template_manager.py` | Saving, loading and validating JSON templates; locating the referenced background. |
| `config.py` | Folder locations (calculated from the project folder, no hard-coded paths). |
| `requirements.txt` | Runtime packages: streamlit, pillow, reportlab, pymupdf. |
| `requirements-dev.txt` | Adds pytest for running the tests. |
| `run_app.bat` | Optional Windows double-click launcher. |
| `tests/test_renderer.py` | Automated tests for rendering, layout, loading, export and templates. |
| `tests/conftest.py` | Makes the project importable from the tests. |
| `CHECKPOINT.md` | Status report and instructions for continuing with Part 2. |
| `data/backgrounds/` | Uploaded backgrounds are stored here. |
| `data/templates/` | Saved templates. |
| `fonts/` | Your TTF/OTF fonts. |
| `output/` | Default export folder. |

## 7. Troubleshooting

* **"streamlit is not recognized"** - use `python -m streamlit run app.py` with the venv activated.
* **PDF background error** - run `pip install pymupdf`, or export your design as PNG/JPG instead.
* **Text uses a different font than expected** - the preview lists a warning when a font was not found
  and a fallback was used.
* **Permission denied on export** - close the file if it is open in another program, or pick another folder.
