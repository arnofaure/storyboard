# Changelog

All notable changes to Storyboard are documented here.

## [0.8] - 2026-06-25

### Added
- Welcome overlay shown automatically on first visit, explaining the essentials and prominently flagging that nothing is saved automatically (Ctrl/Cmd+S reminder). Dismissing it (Got it, close, or Escape) suppresses it for ~2 months, after which it resurfaces as a periodic reminder.
- Link to the YouTube tutorials/updates playlist in the Help overlay

## [0.7.1] - 2026-06-24

### Fixed
- Phi grid lines disappeared inconsistently on some shots when Monochrome was also active. Caused by `filter: grayscale()` on the `<img>` isolating it into its own compositing layer, breaking the grid overlay's `mix-blend-mode`. Fixed by applying the grayscale filter to the image wrapper instead, keeping both in the same compositing layer.

## [0.7] - 2026-06-21

### Added
- Golden-ratio (phi) composition grid overlay on each shot, toggled with the new "Grid" button or the `G` shortcut
- Monochrome (black & white) view toggle, to check tonal/contrast dynamics, via the new "B&W" button
- Flag filter is now a custom dropdown showing the real colored flag icon per option (In Progress/Approved/Needs Revision/Rejected), replacing the native select with colored emoji

## [0.6] - 2026-06-20

### Added
- Favicon, Apple touch icons, Android icons, and MS tile icons, wired up via `manifest.json` and `browserconfig.xml`, organized under an `icons/` folder

### Changed
- Clean View now hides the entire shot footer bar (the "cartouche"), not just the flag and number
- The "Drop images here" zone collapses to a slim bar once the board has shots, and expands back to full size when emptied

## [0.5] - 2026-06-19

### Added
- Full keyboard shortcut set: Ctrl/Cmd+D (duplicate selection), Ctrl/Cmd+A (select all), Delete (delete selection), C (Clean View), T (Timeline), F (Show File Name), 1-4/0 (quick flag selection), Escape (deselect + close overlays) — shown next to their labels in the menu and toolbar

### Changed
- "Don't forget to save" reminder moved to a prominent red banner at the top of the screen, replacing the easy-to-miss bottom toast
- Shot count in the toolbar now reflects the active flag filter ("X of Y shots")
- Clean View now also hides the shot number badge, not just flags and highlights

## [0.4] - 2026-06-18

### Added
- "In Progress" (blue) flag, added before Approved (green) in the flag cycle: None → In Progress → Approved → Needs Revision → Rejected
- Flag filter in the toolbar — show only shots with a specific flag (or no flag), with a colored icon next to each option
- "Cinematic Shot Types" reference guide in the menu — an overlay covering shot sizes, camera angles, camera movements and a prompt-structure template
- Shot Details dropdowns (framing/angle/movement) expanded with more essential options (Full Shot, Cowboy Shot, Insert, Bird's/Worm's Eye, POV, OTS, Tracking, Arc, Zoom In/Out, Handheld, Crane, etc.)

### Changed
- "Show Duplicates" highlight redesigned: instead of a blue outline, duplicate shots are now dimmed with a "Duplicate Shot" label overlay (consistent with the Rejected flag's dimming style)

### Fixed
- Flag color outlines appeared thinner around the image than around the footer. Caused by the outline (and later box-shadow) being painted behind the image's own opaque background. Fixed with an absolutely-positioned overlay that always paints above the image, giving a perfectly even border on every shot.

## [0.3] - 2026-06-17

### Added
- Project name field — names your downloaded `.json` file and is remembered across saves
- Auto-save to your browser (IndexedDB) — your last session is restored automatically when you reopen the app; manual Save/Open still works as before for backup and sharing
- Duplicate shots — a duplicate button on each shot card, plus batch duplicate for multiple selected shots
- Drag-and-drop import at a specific position — drop image files directly onto an existing shot to insert the new shots right after it
- A dashed drop zone at the end of every scene — click it to import via the file picker, drag images onto it to add them at the end of that scene, or drag an existing shot onto it to move it to the end
- 3:5 and 9:16 ratios added to the ratio selector; the "Original" ratio option was removed
- Contact link (info@arnofaure.com) in the Help overlay and README

### Fixed
- It was previously impossible to drag a shot to become the very last shot of a scene — there was no valid drop target past the last card. Fixed by the new end-of-scene drop zone.
- Player window now properly resizes to fit the selected ratio when switching ratios
- Placeholder text in the floating player was nearly unreadable (too low contrast)

## [0.2] - 2026-06-16

### Added
- NLE-style shot timeline at the bottom of the board, with scene separators and thumbnails
- Keyboard navigation (← →) to step through shots when the timeline is open
- Floating player window — play/pause, prev/next, adjustable speed, drag to reposition, resize, reset to default size/position
- Player screen respects the selected aspect ratio and always fills with crop (no letterboxing)
- In-app Help overlay and "How to use it" section in the README

## [0.1] - earlier

- Initial public release: drag & drop storyboard builder, scenes, flags, themes, undo, export to images/PDF/FCPXML, save/open as JSON
