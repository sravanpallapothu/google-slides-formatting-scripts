![GitHub repo size](https://img.shields.io/github/repo-size/sravanpallapothu/google-slides-formatting-scripts)
![GitHub contributors](https://img.shields.io/github/contributors/sravanpallapothu/google-slides-formatting-scripts)
![Last Commit](https://img.shields.io/github/last-commit/sravanpallapothu/google-slides-formatting-scripts)


# Google Slides Formatter — Apps Script Collection

A collection of Google Apps Script functions that automate formatting and standardisation tasks across Google Slides presentations. Designed for teams that work with large decks built from slides copied across multiple sources, where fonts, colours, positions and sizes tend to drift from brand standards over time.

---

## What It Does

The collection contains 9 scripts (all scripts.txt) that can be run individually or all at once via a single master function:

| Script | Function | Description |
|--------|----------|-------------|
| 1 | `runAllScripts()` | Master runner — executes all scripts in the correct sequence |
| 2 | `formatTitles()` | Standardises title font, size, colour, position and removes bold/italic/underline |
| 3 | `formatBodyText()` | Left-aligns body text boxes to the title edge and trims width to fit within guides |
| 4 | `formatLayout()` | Evenly spaces images/icons within guide boundaries and aligns footnotes |
| 5 | `standardiseBackground()` | Sets a uniform background colour across all slides |
| 6 | `standardiseDimensions()` | Enforces a consistent slide size across the presentation |
| 7 | `standardiseLineSpacing()` | Applies consistent line spacing to all body text |
| 8 | `standardiseFont()` | Applies a single font family and size to all text elements |
| 9 | `standardiseTextBoxWidth()` | Trims oversized text boxes to a maximum width |

---

## Current Settings

These are the default values baked into the scripts. All are easy to change — each function has a clearly marked `YOUR RULES` section at the top:

- **Font:** Palatino Linotype
- **Title size:** 25pt | **Body size:** 18pt
- **Title colour:** #016976
- **Title position:** X = 0.43in, Y = 0.12in
- **Left guide:** 1.15in | **Right guide:** 12.18in
- **Max text box width:** 11.75in
- **Background:** #FFFFFF (white)
- **Slide dimensions:** 10 × 7.5 inches (standard 4:3)
- **Line spacing:** 100% (single)

---

## How to Use

1. Open your Google Slides presentation
2. Go to **Extensions → Apps Script**
3. Delete any existing code in the editor
4. Paste the contents of `slides_formatter.gs` into the editor
5. Click **Save** (💾)
6. Select `runAllScripts` from the function dropdown and click **Run** (▶️)
7. Grant permissions when prompted (first run only)
8. Check **View → Logs** for a full report of what was changed

To run a single script instead of all of them, select its function name from the dropdown before clicking Run.

---

## Best Results — Slide Complexity

These scripts work best on slides with **simple, flat layouts** — that is, slides where content is organised as discrete, independent elements: one text box for the title, one for the body, standalone images, and so on.

They will produce **partial or unpredictable results** on slides that use:
- **Grouped elements** — groups are treated as a single element; the script cannot reach inside them to reformat individual components
- **Tables** — Google Apps Script has a separate Table API that these scripts do not use; table cells will not be reformatted
- **Complex layered layouts** — slides where text boxes overlap, are stacked, or are used decoratively (e.g. coloured background boxes with text inside) may have their positions altered unexpectedly
- **Slides imported from PowerPoint** — element types and placeholder designations may differ from native Google Slides elements, causing some scripts to skip or mishandle them

As a rule of thumb: the more a slide resembles a standard title-and-body layout, the more reliably these scripts will work.

---

## Important Caveats

- **Changes can generally be undone** using Ctrl+Z (Edit → Undo) immediately after running a script. However, it is still strongly recommended to work on a copy of your deck for complex or large presentations, as undo history can be limited and may not reliably reverse every change across many slides.

- **Title detection prerequisite:** `formatTitles()`, `formatBodyText()`, `standardiseFont()`, `standardiseLineSpacing()`, and `standardiseTextBoxWidth()` all rely on correctly identifying the title element. The title is detected as the **topmost text-containing element within 1.5 inches of the top edge of the slide**. For this to work:
  - The title text box must have its top edge positioned within 1.5 inches from the top of the slide
  - The title must be a text box or shape containing actual text — empty shapes or images sitting above the title will be mistakenly identified as the title
  - If no text element is found within the top 1.5 inches, the slide is skipped entirely
  - Slides where the title sits below 1.5 inches from the top (e.g. slides with a large header image above the title) will not be processed correctly

- **Body text alignment prerequisite:** `formatBodyText()` positions all non-title text boxes to a left edge of 0.43 inches. Any element containing text that is not the title — including labels, footnotes, captions, and decorative text — will be moved to this X position. Review results carefully on slides with multi-column layouts.

- **Icon spacing prerequisite:** `formatLayout()` identifies icons as elements of type IMAGE. Any image on the slide — including logos, background graphics, and decorative images — will be treated as an icon and evenly spaced. If your slide contains images that should not be moved, do not run this script on those slides.

- **Icon label positioning** is intentionally excluded — label positions vary too much between slides for reliable automation.

- **Background colour assumption:** `standardiseBackground()` applies a solid fill to every slide without exception. Slides that intentionally use a non-white background (e.g. a dark cover slide or a section divider) will have their background overwritten. Consider running this script only on a selected range of slides if your deck uses deliberate background variation.

- **Slide dimensions warning:** `standardiseDimensions()` changes the page size at the presentation level, which affects all slides simultaneously. Google Slides does not proportionally rescale element positions when dimensions change — elements will stay in their original positions in points, which may place them outside the new slide boundaries if the size is reduced. Always review all slides after running this script.

- **Line spacing assumption:** `standardiseLineSpacing()` applies the same spacing value to every paragraph in every non-title text box. This includes bullet points, numbered lists, and any text with intentional varied spacing. If certain text blocks were deliberately set to a different spacing for readability (e.g. a tightly packed footnote or a widely spaced header), those will be overridden.

- **Font standardisation assumption:** `standardiseFont()` applies the body font size (18pt) to all non-title elements uniformly — it does not distinguish between body text, footnotes, captions, or labels. If your deck uses intentionally smaller text for footnotes or larger text for subheadings, those distinctions will be lost. Additionally, if the specified font is not available in the user's Google Slides font library, changes will silently fail for affected elements with no error shown on screen — only the logs will reflect this.

- **Text box width assumption:** `standardiseTextBoxWidth()` only trims boxes that are wider than the target width — it does not expand boxes that are narrower than expected. If some text boxes are unusually narrow and text is wrapping incorrectly, this script will not fix that. A separate script would be needed to enforce a minimum width.

- **Guide positions** cannot be set programmatically — this is a known limitation of the Google Apps Script Slides API. If slides copied from other sources have different guide positions, these must be corrected manually via View → Guides → Edit Guides.

---

## Customising the Scripts

Every script has a `// ── YOUR RULES ──` block near the top containing all the values you might want to change (font name, size, colour, position, etc.). You do not need to understand the rest of the code to customise these values.

---

## Requirements

- A Google account with access to Google Slides
- No installation required — runs entirely within Google Apps Script (built into Google Workspace)
- No external libraries or dependencies

---

## Known Limitations

- Does not handle slides built as native Google Slides tables
- Does not reposition elements vertically
- Does not detect or fix manually drawn text boxes that have no text content
- Image resizing for inconsistent sizes is not implemented in the current version

---

## Contributing

If you extend these scripts or fix edge cases, pull requests are welcome. Please document any new `YOUR RULES` variables clearly and include a description of what the script does and does not handle.
