# Accessibility Review: Iowa State Colored Convention Map (1868)

**File updated:** `index.html`
**Target standard:** WCAG 2.1 AA (the benchmark generally used for ADA / Section 508 compliance of web content)

---

## Summary

The original page was a visual-only Leaflet map. Several things blocked users of screen readers, keyboards, and zoom/magnification, and the key information (who came from where, how many delegates) existed only as colored markers and lines on a canvas. The updated page keeps the same look and data, and adds keyboard operation, screen reader names, a text/table alternative, better contrast, and reflow/zoom support.

---

## Issues found and changes made

| # | Issue | WCAG criterion | Change made |
|---|-------|----------------|-------------|
| 1 | **Pinch-zoom and browser zoom disabled** via `maximum-scale=1.0, user-scalable=no` in the viewport meta tag. | 1.4.4 Resize Text, 1.4.10 Reflow | Removed both restrictions. Viewport is now `width=device-width, initial-scale=1.0`. |
| 2 | **Map information had no text alternative.** Delegate counts, origins, and journey lines were only visual (markers plus a `<canvas>`). | 1.1.1 Non-text Content, 1.3.1 Info and Relationships | Added a **"View data as table"** button and panel. The table is generated from the same GeoJSON (community, delegate count, community info, with a total row) and has a caption and `scope` headers. Added a text summary that explains what the journey lines mean. |
| 3 | **Canvas journey lines exposed with no meaning** to assistive tech. | 1.1.1 | Canvas elements are marked `aria-hidden="true"` / `role="presentation"` (re-applied when the layer is toggled). Table carries the equivalent information. |
| 4 | **Markers had no accessible names or roles.** Screen readers would announce an unlabeled element. | 4.1.2 Name, Role, Value | Each marker now has `role="button"`, an `aria-label` (e.g. "Delegate origin: *Community*, 2 delegates. Press Enter for details."), `aria-haspopup="dialog"`, and a `title`. Names are re-applied if the layer is toggled off and on. Decorative Font Awesome icons inside markers are `aria-hidden`. |
| 5 | **Popups did not manage focus.** Opening a popup left keyboard focus on the marker, so screen reader and keyboard users never reached the content. Close button was an unlabeled "×". | 2.4.3 Focus Order, 4.1.2 | Popup now gets `role="dialog"` and `aria-labelledby` (its heading), receives focus when opened, and returns focus to its marker when closed. Close button has `role="button"` and `aria-label="Close popup"`, and also responds to the Space key. Scrollable popup content becomes keyboard-focusable. Escape closes the popup (built-in Leaflet behavior). |
| 6 | **Map container was unnamed** and gave no usage instructions. | 4.1.2, 3.3.2 Labels or Instructions | Map container has `role="region"` and a descriptive `aria-label` that explains keyboard controls (arrows pan, +/- zoom, Tab between markers, Enter opens details) and points to the table. |
| 7 | **No skip link / landmarks.** | 2.4.1 Bypass Blocks | Added a "Skip to map" link, a `<main>` landmark, and kept `<header>` as the banner landmark. |
| 8 | **Heading misuse.** `<h2>` was used for the credit line, and `<h3>`/`<h4>` were used for popup titles and body text, with `<br>` tags for spacing. | 1.3.1, 2.4.6 Headings and Labels | Credit line is now a `<p>`. Popup titles are a single `<h3>`; popup body text uses `<p>`. `<br>` spacing replaced with CSS margins. Table panel uses an `<h2>`. |
| 9 | **Header text contrast was unreliable.** CSS used the invalid value `color: 'black'` (quoted), so text fell back to the default black over a black background / photo header. | 1.4.3 Contrast (Minimum) | Header text now sits on a solid parchment panel (`#f2eecb`) with near-black text (`#1a1a1a`, about 14:1 contrast). Fixed the invalid color declarations. Credit links are underlined. |
| 10 | **Small, fixed pixel text** (12-13px). | 1.4.4, 1.4.12 Text Spacing | All text sizes are in `rem` with a 1rem minimum for body text, so it scales with browser settings. |
| 11 | **Fixed 100px header** with a fixed-position map. Enlarged text or narrow screens would clip or overlap the header. | 1.4.10 Reflow, 1.4.4 | Layout changed to a flex column: header grows with its content; map fills remaining space (`#map` is absolutely positioned inside `<main>`). |
| 12 | **No visible keyboard focus styling** on markers, links, or controls. | 2.4.7 Focus Visible, 1.4.11 Non-text Contrast | Added a 3px blue (`#0b3d91`) outline with a white halo for all focusable elements, including markers and the map container. |
| 13 | **Small touch targets** (layer checkboxes, popup close button). | 2.5.8 Target Size (Minimum, WCAG 2.2) | Checkboxes enlarged to 1.25rem with padded, clickable labels. Popup close button enlarged to 44x44px. New buttons have a 44px minimum size. |
| 14 | **Layer control not announced as a group; layer changes silent.** | 4.1.2, 4.1.3 Status Messages | Layer control has `role="group"` and `aria-label="Map layers"`. A polite live region announces "*Layer* shown / hidden" when overlays are toggled. |
| 15 | **Journey line color had lowish contrast** (`rgba(139,0,0,0.5)`) against the pioneer-style basemap. | 1.4.11 Non-text Contrast | Opacity raised from 0.5 to 0.85. Line width remains the visual encoding of delegate count, and the same counts are in the table, so meaning is not conveyed by appearance alone. |
| 16 | **Animations ignored the "reduce motion" setting.** | 2.3.3 Animation from Interactions | Leaflet zoom, fade, and marker animations are disabled when `prefers-reduced-motion: reduce` is set; CSS transitions/animations are also disabled. |
| 17 | **Silent failure if data files don't load.** | 3.3.1 Error Identification, 4.1.3 | Added a visible `role="alert"` message when the GeoJSON files fail to load. |
| 18 | **No page description.** | Best practice | Added a meta description. |

---

## Things I did not change (and why)

- **Basemap and historical raster tiles.** The Thunderforest Pioneer tiles and your TMS overlay are images; Leaflet marks them as decorative (`alt=""`). Text/label contrast inside those tiles can't be adjusted from code. The table provides the information; consider reviewing the tile contrast yourself.
- **Marker colors (green/red).** They are also distinguished by icon (home vs. place of worship), and now by text labels, so color is not the only cue.
- **Leaflet 1.5.1.** Kept the existing version. Newer Leaflet releases have more built-in accessibility improvements (for example, better default marker roles and close-button labels), so upgrading is worthwhile when convenient. The JavaScript above is written to work with 1.5.1.
- **Exposed Thunderforest API key.** Not an accessibility issue, but the key is visible in client-side code. Consider restricting it by domain in your Thunderforest account. The `apikey` option with angle brackets (`'<dd2dca...>'`) is also malformed, though harmless since the key is in the tile URL.
- **`Community_Info` HTML.** It is inserted as HTML in popups and the table, as in the original. If this data can ever come from an untrusted source, sanitize it.

---

## Testing notes

I updated the code but could not run it against your data files, images, or tile services here, so please verify in a browser. Recommended checks:

1. **Keyboard only:** Tab to the map, then to each marker; Enter opens the popup and moves focus in; Escape or the close button returns focus to the marker. Arrow keys pan; `+` / `-` zoom.
2. **Screen reader:** Test with NVDA or JAWS (Windows) and VoiceOver (macOS/iOS). Confirm marker names, popup announcements, and the table.
3. **Zoom/reflow:** Zoom the browser to 200% and 400%, and test at 320px width.
4. **Automated scan:** Run axe DevTools or Lighthouse for a baseline, then do manual checks. Automated tools only catch a portion of WCAG issues.
5. **Contrast:** Confirm focus outlines and journey lines are visible over the basemap at all zoom levels.

> Note: a formal ADA/WCAG compliance claim should be backed by a full audit, ideally including testing by users of assistive technology.
