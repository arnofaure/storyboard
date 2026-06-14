# Storyboard Improvements Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a canvas background color picker (white/grey/dark grey), a per-shot file name display toggle, and Save/Load of the whole storyboard as a JSON project file, and translate the existing French UI to English.

**Architecture:** `storyboard.html` is a single self-contained file (HTML + CSS + vanilla JS, no build step, no dependencies besides jsPDF/html2canvas via CDN). All work happens in this one file. The four features are layered as independent, sequential edits to the same file: translation first (lowest risk, touches the most lines but changes nothing structurally), then the two additive UI features (background picker, name toggle), then the data model change (data URLs instead of blob URLs) that the final feature (Save/Load) depends on.

**Tech Stack:** Vanilla HTML/CSS/JS, no test framework or package manager. There are no automated tests — verification is manual: open `storyboard.html` directly in a browser (e.g. `open storyboard.html` on macOS) after each task and check the described behavior. This is not a git repository, so steps that would normally be "commit" are replaced with a manual checkpoint (re-open the file, confirm behavior, then move to the next task).

---

### Task 1: Translate UI strings from French to English

**Files:**
- Modify: `storyboard.html`

This task only changes user-visible text and the `lang` attribute. No structural or behavioral change.

- [ ] **Step 1: Change the document language**

In `storyboard.html`, change:

```html
<html lang="fr">
```

to:

```html
<html lang="en">
```

- [ ] **Step 2: Translate the toolbar Import button**

Find:

```html
  <button class="tb-btn primary" onclick="document.getElementById('file-input').click()">
    <svg width="13" height="13" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path d="M12 5v14M5 12l7-7 7 7"/></svg>
    Importer
  </button>
```

Replace with:

```html
  <button class="tb-btn primary" onclick="document.getElementById('file-input').click()">
    <svg width="13" height="13" fill="none" stroke="currentColor" stroke-width="1.5" viewBox="0 0 24 24"><path d="M12 5v14M5 12l7-7 7 7"/></svg>
    Import
  </button>
```

- [ ] **Step 3: Translate the selection bar**

Find:

```html
  <div id="sel-bar">
    <span id="sel-count">0 sél.</span>
    <button class="tb-btn danger" onclick="deleteSelected()">Supprimer</button>
    <button class="tb-btn" onclick="clearSelection()">Désélectionner</button>
  </div>
```

Replace with:

```html
  <div id="sel-bar">
    <span id="sel-count">0 selected</span>
    <button class="tb-btn danger" onclick="deleteSelected()">Delete</button>
    <button class="tb-btn" onclick="clearSelection()">Deselect</button>
  </div>
```

- [ ] **Step 4: Translate the size slider label**

Find:

```html
    <div class="size-wrap">
      <span class="size-label">Taille</span>
      <input type="range" id="size-range" min="120" max="380" value="220" step="10" oninput="updateSize(this.value)">
    </div>
```

Replace with:

```html
    <div class="size-wrap">
      <span class="size-label">Size</span>
      <input type="range" id="size-range" min="120" max="380" value="220" step="10" oninput="updateSize(this.value)">
    </div>
```

- [ ] **Step 5: Translate the export button titles**

Find:

```html
    <button class="tb-btn" onclick="exportImages()" title="Exporter chaque frame en PNG">
```

Replace with:

```html
    <button class="tb-btn" onclick="exportImages()" title="Export each frame as PNG">
```

Find:

```html
    <button class="tb-btn" onclick="exportPDF()" title="Exporter en PDF">
```

Replace with:

```html
    <button class="tb-btn" onclick="exportPDF()" title="Export as PDF">
```

- [ ] **Step 6: Translate the dropzone text**

Find:

```html
    <p>Déposer des images ici ou cliquer pour importer</p>
    <small>PNG, JPG, WEBP — les ratios originaux sont préservés</small>
```

Replace with:

```html
    <p>Drop images here or click to import</p>
    <small>PNG, JPG, WEBP — original ratios are preserved</small>
```

- [ ] **Step 7: Translate the spinner label**

Find:

```html
<div id="spinner">
  <div class="spin-ring"></div>
  <span id="spinner-label">Export en cours...</span>
</div>
```

Replace with:

```html
<div id="spinner">
  <div class="spin-ring"></div>
  <span id="spinner-label">Exporting...</span>
</div>
```

- [ ] **Step 8: Translate the per-frame delete button title**

Find:

```javascript
    del.className = 'frame-del';
    del.title = 'Supprimer';
```

Replace with:

```javascript
    del.className = 'frame-del';
    del.title = 'Delete';
```

- [ ] **Step 9: Translate the selection count text**

Find:

```javascript
function updateSelBar() {
  const bar = document.getElementById('sel-bar');
  const sc = document.getElementById('sel-count');
  if (selectedIds.size > 0) {
    bar.classList.add('visible');
    sc.textContent = selectedIds.size + ' sél.';
  } else {
    bar.classList.remove('visible');
  }
}
```

Replace with:

```javascript
function updateSelBar() {
  const bar = document.getElementById('sel-bar');
  const sc = document.getElementById('sel-count');
  if (selectedIds.size > 0) {
    bar.classList.add('visible');
    sc.textContent = selectedIds.size + ' selected';
  } else {
    bar.classList.remove('visible');
  }
}
```

- [ ] **Step 10: Translate the export-images toasts**

Find:

```javascript
// --- EXPORT IMAGES ---
async function exportImages() {
  if (!frames.length) { toast('Aucune image à exporter.'); return; }
  toast('Téléchargement en cours...');
```

Replace with:

```javascript
// --- EXPORT IMAGES ---
async function exportImages() {
  if (!frames.length) { toast('No images to export.'); return; }
  toast('Download starting...');
```

- [ ] **Step 11: Translate the export-PDF toasts and progress label**

Find:

```javascript
async function exportPDF() {
  if (!frames.length) { toast('Aucune image à exporter.'); return; }
```

Replace with:

```javascript
async function exportPDF() {
  if (!frames.length) { toast('No images to export.'); return; }
```

Find:

```javascript
      spinnerLabel.textContent = `Export PDF — ${i + 1} / ${frames.length}`;
```

Replace with:

```javascript
      spinnerLabel.textContent = `Exporting PDF — ${i + 1} / ${frames.length}`;
```

Find:

```javascript
    pdf.save('storyboard.pdf');
    toast('PDF exporté.');
  } catch(err) {
    console.error(err);
    toast('Erreur export PDF.');
  } finally {
```

Replace with:

```javascript
    pdf.save('storyboard.pdf');
    toast('PDF exported.');
  } catch(err) {
    console.error(err);
    toast('Error exporting PDF.');
  } finally {
```

- [ ] **Step 12: Verify**

Open `storyboard.html` in a browser. Confirm:
- All toolbar buttons, the dropzone text, and the size slider label are in English.
- Import a few images, select one (checkbox), confirm the selection bar reads "1 selected" with "Delete"/"Deselect" buttons.
- Click "Images" with no frames loaded yet (or after deleting all) — toast reads "No images to export."
- Click "PDF" with frames loaded — spinner shows "Exporting PDF — 1 / N" then toast "PDF exported."

- [ ] **Step 13: Checkpoint**

No git repository is present, so there is nothing to commit. Confirm Step 12 passes before moving to Task 2.

---

### Task 2: Canvas background color picker (White / Grey / Dark grey)

**Files:**
- Modify: `storyboard.html`

Adds three swatch buttons to the toolbar that change the background of `#main`/`#grid` only. The toolbar itself is unaffected. The dropzone's colors are driven by new `--canvas-fg`/`--canvas-fg-dim`/`--canvas-hover-bg` variables so it stays visible on all three backgrounds.

- [ ] **Step 1: Replace the `#main` CSS rule with background-aware version**

Find:

```css
  /* MAIN */
  #main {
    padding: 20px;
  }
```

Replace with:

```css
  /* MAIN */
  #main {
    padding: 20px;
    background: var(--canvas-bg);
    transition: background 0.15s;
  }
  #main.bg-white { --canvas-bg: #ffffff; --canvas-fg: #888888; --canvas-fg-dim: #aaaaaa; --canvas-hover-bg: rgba(0,0,0,0.04); }
  #main.bg-grey  { --canvas-bg: #4a4a4a; --canvas-fg: #dddddd; --canvas-fg-dim: #bbbbbb; --canvas-hover-bg: rgba(255,255,255,0.06); }
  #main.bg-dark  { --canvas-bg: #0d0d0d; --canvas-fg: #666666; --canvas-fg-dim: #444444; --canvas-hover-bg: rgba(255,255,255,0.04); }
```

- [ ] **Step 2: Make the dropzone use the canvas-aware colors**

Find:

```css
  /* DROPZONE */
  #dropzone {
    border: 1px dashed var(--border);
    border-radius: var(--radius-lg);
    padding: 32px 20px;
    text-align: center;
    cursor: pointer;
    transition: border-color 0.15s, background 0.15s;
    margin-bottom: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
  }
  #dropzone:hover, #dropzone.hover {
    border-color: var(--border-hover);
    background: var(--surface);
  }
  #dropzone svg { color: var(--text-dim); margin-bottom: 4px; }
  #dropzone p { font-size: 13px; color: var(--text-muted); }
  #dropzone small { font-size: 11px; color: var(--text-dim); }
  #file-input { display: none; }
```

Replace with:

```css
  /* DROPZONE */
  #dropzone {
    border: 1px dashed var(--canvas-fg-dim);
    border-radius: var(--radius-lg);
    padding: 32px 20px;
    text-align: center;
    cursor: pointer;
    transition: border-color 0.15s, background 0.15s;
    margin-bottom: 20px;
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 6px;
  }
  #dropzone:hover, #dropzone.hover {
    border-color: var(--canvas-fg);
    background: var(--canvas-hover-bg);
  }
  #dropzone svg { color: var(--canvas-fg-dim); margin-bottom: 4px; }
  #dropzone p { font-size: 13px; color: var(--canvas-fg); }
  #dropzone small { font-size: 11px; color: var(--canvas-fg-dim); }
  #file-input { display: none; }
```

- [ ] **Step 3: Add swatch button styles**

Find:

```css
  .size-label { font-size: 11px; color: var(--text-dim); }
```

Replace with:

```css
  .size-label { font-size: 11px; color: var(--text-dim); }

  .bg-wrap { display: flex; align-items: center; gap: 6px; }
  .bg-swatch {
    width: 16px;
    height: 16px;
    border-radius: 50%;
    border: 1.5px solid var(--border-hover);
    cursor: pointer;
    padding: 0;
    transition: border-color 0.15s, transform 0.1s;
  }
  .bg-swatch:hover { transform: scale(1.1); }
  .bg-swatch.active { border-color: var(--text); box-shadow: 0 0 0 2px var(--bg), 0 0 0 3px var(--text); }
  .bg-swatch-white { background: #ffffff; }
  .bg-swatch-grey { background: #4a4a4a; }
  .bg-swatch-dark { background: #0d0d0d; }
```

- [ ] **Step 4: Add the swatch buttons to the toolbar and the `bg-dark` class to `#main`**

Find:

```html
    <div class="size-wrap">
      <span class="size-label">Size</span>
      <input type="range" id="size-range" min="120" max="380" value="220" step="10" oninput="updateSize(this.value)">
    </div>
    <div class="tb-sep"></div>
```

Replace with:

```html
    <div class="size-wrap">
      <span class="size-label">Size</span>
      <input type="range" id="size-range" min="120" max="380" value="220" step="10" oninput="updateSize(this.value)">
    </div>
    <div class="tb-sep"></div>
    <div class="bg-wrap">
      <span class="size-label">Background</span>
      <button class="bg-swatch bg-swatch-white" data-bg="white" onclick="setBackground('white')" title="White"></button>
      <button class="bg-swatch bg-swatch-grey" data-bg="grey" onclick="setBackground('grey')" title="Grey"></button>
      <button class="bg-swatch bg-swatch-dark active" data-bg="dark" onclick="setBackground('dark')" title="Dark grey"></button>
    </div>
    <div class="tb-sep"></div>
```

Now find:

```html
<div id="main">
  <div id="dropzone"
```

Replace with:

```html
<div id="main" class="bg-dark">
  <div id="dropzone"
```

- [ ] **Step 5: Add the `setBackground` function and `currentBg` state**

Find:

```javascript
let frames = [];
let dragSrc = null;
let selectedIds = new Set();
```

Replace with:

```javascript
let frames = [];
let dragSrc = null;
let selectedIds = new Set();
let currentBg = 'dark';

function setBackground(name) {
  currentBg = name;
  const main = document.getElementById('main');
  main.classList.remove('bg-white', 'bg-grey', 'bg-dark');
  main.classList.add('bg-' + name);
  document.querySelectorAll('.bg-swatch').forEach(b => b.classList.toggle('active', b.dataset.bg === name));
}
```

- [ ] **Step 6: Verify**

Open `storyboard.html` in a browser. Confirm:
- The "Dark grey" swatch is shown as active by default and the canvas area is the same dark color as before.
- Clicking the "White" swatch turns the area behind the grid (and the dropzone) white, while the toolbar stays dark. The dropzone border/text/icon remain clearly visible.
- Clicking "Grey" turns that area mid-grey, dropzone still visible.
- Clicking back to "Dark grey" restores the original look and marks that swatch as active (highlighted ring).
- Frame cards themselves are unchanged (still dark surfaces) regardless of the canvas color.

- [ ] **Step 7: Checkpoint**

Confirm Step 6 passes before moving to Task 3.

---

### Task 3: Per-shot file name display toggle

**Files:**
- Modify: `storyboard.html`

Adds a "Names" toggle button to the toolbar. When on, each card's footer shows the file name on a second line below the shot number, truncated with an ellipsis if too long. The footer markup is restructured into two rows (number+delete, then name) so the name line can be shown/hidden purely with CSS.

- [ ] **Step 1: Restructure the footer CSS into two rows and add the name-line style**

Find:

```css
  /* FOOTER */
  .frame-footer {
    padding: 7px 10px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    border-top: 0.5px solid var(--border);
  }
  .shot-num {
    font-size: 11px;
    font-weight: 500;
    color: var(--text-dim);
    letter-spacing: 0.06em;
    text-transform: uppercase;
  }
```

Replace with:

```css
  /* FOOTER */
  .frame-footer {
    padding: 7px 10px;
    display: flex;
    flex-direction: column;
    gap: 3px;
    border-top: 0.5px solid var(--border);
  }
  .frame-footer-row {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }
  .shot-num {
    font-size: 11px;
    font-weight: 500;
    color: var(--text-dim);
    letter-spacing: 0.06em;
    text-transform: uppercase;
  }
  .frame-name {
    display: none;
    font-size: 10px;
    color: var(--text-dim);
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
  #grid.show-names .frame-name { display: block; }
```

- [ ] **Step 2: Add the "Names" toggle button to the toolbar**

Find:

```html
    <div class="tb-sep"></div>
    <div class="bg-wrap">
      <span class="size-label">Background</span>
      <button class="bg-swatch bg-swatch-white" data-bg="white" onclick="setBackground('white')" title="White"></button>
      <button class="bg-swatch bg-swatch-grey" data-bg="grey" onclick="setBackground('grey')" title="Grey"></button>
      <button class="bg-swatch bg-swatch-dark active" data-bg="dark" onclick="setBackground('dark')" title="Dark grey"></button>
    </div>
    <div class="tb-sep"></div>
```

Replace with:

```html
    <div class="tb-sep"></div>
    <div class="bg-wrap">
      <span class="size-label">Background</span>
      <button class="bg-swatch bg-swatch-white" data-bg="white" onclick="setBackground('white')" title="White"></button>
      <button class="bg-swatch bg-swatch-grey" data-bg="grey" onclick="setBackground('grey')" title="Grey"></button>
      <button class="bg-swatch bg-swatch-dark active" data-bg="dark" onclick="setBackground('dark')" title="Dark grey"></button>
    </div>
    <div class="tb-sep"></div>
    <button class="tb-btn" id="names-toggle" onclick="toggleNames()" title="Show file names on each shot">
      Names
    </button>
    <div class="tb-sep"></div>
```

- [ ] **Step 3: Update `render()` to build the two-row footer with a name line**

Find:

```javascript
    // Footer
    const footer = document.createElement('div');
    footer.className = 'frame-footer';
    const num = document.createElement('span');
    num.className = 'shot-num';
    num.textContent = String(i + 1).padStart(2, '0');
    const del = document.createElement('button');
    del.className = 'frame-del';
    del.title = 'Delete';
    del.innerHTML = `<svg width="12" height="12" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M18 6L6 18M6 6l12 12"/></svg>`;
    del.onclick = e => { e.stopPropagation(); deleteFrame(fr.id); };

    footer.appendChild(num);
    footer.appendChild(del);
    card.appendChild(check);
    card.appendChild(img);
    card.appendChild(footer);
```

Replace with:

```javascript
    // Footer
    const footer = document.createElement('div');
    footer.className = 'frame-footer';
    const row = document.createElement('div');
    row.className = 'frame-footer-row';
    const num = document.createElement('span');
    num.className = 'shot-num';
    num.textContent = String(i + 1).padStart(2, '0');
    const del = document.createElement('button');
    del.className = 'frame-del';
    del.title = 'Delete';
    del.innerHTML = `<svg width="12" height="12" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path d="M18 6L6 18M6 6l12 12"/></svg>`;
    del.onclick = e => { e.stopPropagation(); deleteFrame(fr.id); };
    const name = document.createElement('div');
    name.className = 'frame-name';
    name.textContent = fr.name;
    name.title = fr.name;

    row.appendChild(num);
    row.appendChild(del);
    footer.appendChild(row);
    footer.appendChild(name);
    card.appendChild(check);
    card.appendChild(img);
    card.appendChild(footer);
```

- [ ] **Step 4: Add `showNames` state and `toggleNames` function**

Find:

```javascript
let currentBg = 'dark';

function setBackground(name) {
```

Replace with:

```javascript
let currentBg = 'dark';
let showNames = false;

function toggleNames() {
  showNames = !showNames;
  document.getElementById('grid').classList.toggle('show-names', showNames);
  document.getElementById('names-toggle').classList.toggle('primary', showNames);
}

function setBackground(name) {
```

- [ ] **Step 5: Verify**

Open `storyboard.html` in a browser. Confirm:
- Import a few images with distinct, somewhat long file names.
- By default, each card's footer shows only the shot number (01, 02, ...), same as before.
- Click "Names" — the button highlights (same style as an active toolbar toggle), and each card now shows its file name on a line below the shot number, truncated with "..." if it's long. Hovering the name shows the full name as a tooltip.
- Click "Names" again — the name lines disappear and the button un-highlights.
- Reordering, selecting, and deleting cards still work normally with names shown or hidden.

- [ ] **Step 6: Checkpoint**

Confirm Step 5 passes before moving to Task 4.

---

### Task 4: Switch image storage from blob URLs to data URLs

**Files:**
- Modify: `storyboard.html`

This is a prerequisite for Task 5: `URL.createObjectURL` results can't be serialized to JSON and don't survive a page reload. Switching to `FileReader.readAsDataURL` makes `fr.url` a string that can be saved directly. Order must be preserved even though each file is read asynchronously, so results are written into a pre-sized array and only pushed into `frames` once all reads complete (same single-render-at-the-end behavior as today).

- [ ] **Step 1: Rewrite `handleFiles` to use `FileReader`**

Find:

```javascript
function handleFiles(files) {
  const imgs = [...files].filter(f => f.type.startsWith('image/'));
  let loaded = 0;
  imgs.forEach(f => {
    const id = uid();
    const url = URL.createObjectURL(f);
    frames.push({ id, url, name: f.name });
    loaded++;
    if (loaded === imgs.length) render();
  });
  if (!imgs.length) return;
}
```

Replace with:

```javascript
function handleFiles(files) {
  const imgs = [...files].filter(f => f.type.startsWith('image/'));
  if (!imgs.length) return;
  const newFrames = new Array(imgs.length);
  let loaded = 0;
  imgs.forEach((f, idx) => {
    const reader = new FileReader();
    reader.onload = e => {
      newFrames[idx] = { id: uid(), url: e.target.result, name: f.name };
      loaded++;
      if (loaded === imgs.length) {
        frames.push(...newFrames);
        render();
      }
    };
    reader.readAsDataURL(f);
  });
}
```

- [ ] **Step 2: Verify**

Open `storyboard.html` in a browser. Confirm:
- Importing multiple images at once (via the file picker and via drag-and-drop) still shows them in the same order as selected/dropped, with no console errors.
- The images render correctly (data URLs work the same as blob URLs in `<img src>`).
- "Images" export still downloads each frame as a file.
- "PDF" export still produces a PDF with all frames.
- Open the browser console and run `frames[0].url.slice(0, 30)` — it should start with `data:image/`.

- [ ] **Step 3: Checkpoint**

Confirm Step 2 passes before moving to Task 5.

---

### Task 5: Save / Load project as JSON

**Files:**
- Modify: `storyboard.html`

Adds "Save" and "Load" buttons to the toolbar. "Save" serializes `frames` plus the current settings (card size, background choice, show-names state) to a downloaded `storyboard-project.json`. "Load" reads such a file back, replacing the current storyboard and restoring settings.

- [ ] **Step 1: Add Save/Load buttons and a hidden file input to the toolbar**

Find:

```html
  <input type="file" id="file-input" accept="image/*" multiple onchange="handleFiles(this.files)">
```

Replace with:

```html
  <input type="file" id="file-input" accept="image/*" multiple onchange="handleFiles(this.files)">

  <button class="tb-btn" onclick="saveProject()" title="Save the storyboard as a project file">
    Save
  </button>
  <button class="tb-btn" onclick="document.getElementById('project-input').click()" title="Load a previously saved project file">
    Load
  </button>
  <input type="file" id="project-input" accept="application/json" onchange="loadProject(this.files[0]); this.value='';">
```

- [ ] **Step 2: Add `saveProject` and `loadProject` functions**

Find:

```javascript
// --- TOAST ---
```

Replace with:

```javascript
// --- SAVE / LOAD PROJECT ---
function saveProject() {
  const data = {
    version: 1,
    frames: frames.map(f => ({ id: f.id, name: f.name, url: f.url })),
    settings: {
      cardSize: parseInt(document.getElementById('size-range').value, 10),
      background: currentBg,
      showNames: showNames
    }
  };
  const blob = new Blob([JSON.stringify(data)], { type: 'application/json' });
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url;
  a.download = 'storyboard-project.json';
  a.click();
  URL.revokeObjectURL(url);
  toast('Project saved.');
}

function loadProject(file) {
  if (!file) return;
  const reader = new FileReader();
  reader.onload = e => {
    let data;
    try {
      data = JSON.parse(e.target.result);
    } catch (err) {
      toast('Invalid project file.');
      return;
    }
    if (!data || !Array.isArray(data.frames)) {
      toast('Invalid project file.');
      return;
    }

    frames = data.frames.map(f => ({ id: f.id || uid(), name: f.name || '', url: f.url }));
    selectedIds.clear();

    const settings = data.settings || {};
    if (settings.cardSize) {
      document.getElementById('size-range').value = settings.cardSize;
      updateSize(settings.cardSize);
    }
    setBackground(settings.background || 'dark');
    showNames = !!settings.showNames;
    document.getElementById('grid').classList.toggle('show-names', showNames);
    document.getElementById('names-toggle').classList.toggle('primary', showNames);

    render();
    toast('Project loaded.');
  };
  reader.readAsText(file);
}

// --- TOAST ---
```

- [ ] **Step 3: Verify**

Open `storyboard.html` in a browser. Confirm:
- Import a few images, set the size slider to a non-default value, switch the background to "Grey", and turn "Names" on.
- Click "Save" — a `storyboard-project.json` file downloads. Open it in a text editor and confirm it has `version`, `frames` (with `id`, `name`, `url` starting with `data:image/`), and `settings` (`cardSize`, `background: "grey"`, `showNames: true`).
- Reload `storyboard.html` (fresh state — empty grid, default dark background, default size, names off).
- Click "Load" and select the saved `storyboard-project.json`.
- Confirm: the same frames reappear in the same order, the size slider and card size match what was saved, the background is "Grey" (and that swatch is active), and "Names" is on with file names showing.
- In the browser console, run `loadProject(undefined)` — it should do nothing (no error).
- Save a file containing `{"foo": "bar"}` as `bad.json` and try loading it — confirm a "Invalid project file." toast appears and the current storyboard is unchanged.

- [ ] **Step 4: Checkpoint**

Confirm Step 3 passes. This completes the plan — all four features (background picker, name toggle, save/load, English translation) are implemented in `storyboard.html`.
