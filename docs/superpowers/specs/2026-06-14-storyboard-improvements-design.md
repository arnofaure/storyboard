# Storyboard improvements — design

## Context

`storyboard.html` is a single-file, offline storyboard tool: import images, reorder by drag-and-drop, select/delete, resize cards, export to PNG/PDF. This spec covers four improvements:

1. A canvas background color picker (white / grey / dark grey)
2. A toggle to show each shot's file name
3. Save/Load the whole storyboard as a project file
4. Translate the UI from French to English

## 1. Background color picker

Three small swatch buttons in the toolbar, labeled by color (White / Grey / Dark grey). Clicking one sets the background of the canvas area only — `#main` and `#grid`. The toolbar itself keeps its current dark styling regardless of selection.

Colors:
- White: `#ffffff`
- Grey: `#4a4a4a`
- Dark grey: `#0d0d0d` (current background — default/active on load)

The active swatch is visually highlighted (border/selection state matching existing toolbar button conventions).

`#dropzone` currently uses `var(--border)` (a faint white overlay) and `var(--text-muted)`/`var(--text-dim)` for its border and text, which are tuned for a dark background and would be nearly invisible on white/grey. Introduce a `--canvas-fg`-style neutral mid-grey (works against all three backgrounds) for the dropzone border, icon, and text, driven by the active background choice via a CSS class on `#main` (e.g. `bg-white`, `bg-grey`, `bg-dark`).

Frame cards keep their existing dark surface styling (`--surface`, `--surface2`) unchanged — they remain dark "frames" regardless of canvas color, which is an intentional look (like mattes on a light table).

## 2. File name toggle

A toggle button labeled "Names" in the toolbar, alongside the size slider. When active:

- Each card's footer shows the shot number on top (as today) and the file name on a second line below it, in a smaller/muted style.
- Long file names are truncated with an ellipsis (`text-overflow: ellipsis`, `overflow: hidden`, `white-space: nowrap`).

When inactive, the footer looks exactly as it does today (just the shot number + delete button).

## 3. Save / Load project

**Image storage change:** image import switches from `URL.createObjectURL(file)` to `FileReader.readAsDataURL(file)`, so each frame's `url` is a data URL. This makes frames directly serializable (no extra conversion step at save time), at the cost of slightly larger in-memory representation — acceptable for typical storyboard sizes. All existing code paths that use `fr.url` (rendering, export images, export PDF) work unchanged with data URLs.

**Save ("Save" button):** serializes the current state to JSON and triggers a download named `storyboard-project.json`:

```json
{
  "version": 1,
  "frames": [ { "id": "...", "name": "...", "url": "data:image/...;base64,..." } ],
  "settings": {
    "cardSize": 220,
    "background": "dark",
    "showNames": false
  }
}
```

**Load ("Load" button):** opens a hidden file input (`accept=".json"`). On selection, parses the JSON, validates it has a `frames` array, then:
- Replaces the current `frames` array and clears `selectedIds`.
- Restores `settings` (card size slider position + CSS variable, active background swatch, name-toggle state).
- Re-renders.

Loading replaces the current storyboard entirely — no merge logic. A toast confirms success, or reports an error if the file isn't a valid project JSON.

## 4. English translation

All user-facing strings are translated from French to English, including:
- Toolbar buttons: "Importer" → "Import", "Supprimer" → "Delete", "Désélectionner" → "Deselect", "Images" → "Images", "PDF" → "PDF"
- Selection bar: "N sél." → "N selected"
- Dropzone: "Déposer des images ici ou cliquer pour importer" → "Drop images here or click to import"; the helper text about ratios
- Shot count: "N shot(s)" → "N shot(s)" (already English, unchanged)
- Toasts: "Téléchargement en cours...", "Aucune image à exporter.", "PDF exporté.", "Erreur export PDF." → English equivalents
- Spinner label: "Export en cours..." / "Export PDF — i / n" → English equivalents
- `lang="fr"` on `<html>` → `lang="en"`

New UI elements (background swatches, "Names" toggle, "Save"/"Load" buttons) are written in English from the start.

## Out of scope

- No persistence beyond the explicit Save/Load project file (no auto-save to browser storage).
- No merge/append behavior on Load — it's a full replace.
- No per-frame custom backgrounds — background choice is global to the canvas.
