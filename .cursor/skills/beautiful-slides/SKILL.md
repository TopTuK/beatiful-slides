---
name: beautiful-slides
description: Create beautiful, animation-rich HTML presentations from scratch or by converting PowerPoint files. Use when the user wants to create a presentation, pitch deck, slides for a talk, or convert PPT/PPTX to web. Helps non-designers pick styles via visual previews.
---

# Beautiful Slides

Create zero-dependency, animation-rich HTML presentations that run entirely in the browser. This skill helps non-designers discover their preferred aesthetic through visual exploration ("show, don't tell"), then generates production-quality slide decks.

## Core Philosophy

1. **Zero Dependencies** — Single HTML files with inline CSS/JS. No npm, no build tools.
2. **Show, Don't Tell** — People don't know what they want until they see it. Generate visual previews, not abstract choices.
3. **Distinctive Design** — Avoid generic "AI slop" aesthetics. Every presentation should feel custom-crafted.
4. **Production Quality** — Code should be well-commented, accessible, and performant.
5. **Viewport Fitting (CRITICAL)** — Every slide MUST fit exactly within the viewport. No scrolling within slides, ever. See [REFERENCE.md](REFERENCE.md) for mandatory CSS and content density rules.

---

## Phase 0: Detect Mode

Determine what the user wants:

- **Mode A: New Presentation** — User wants to create slides from scratch → Phase 1.
- **Mode B: PPT Conversion** — User has a .ppt/.pptx file to convert → Phase 4.
- **Mode C: Existing Presentation Enhancement** — User has an HTML presentation to improve. Read the file, then enhance while maintaining viewport fitting. When adding content, check density limits in REFERENCE.md; if adding an image to a full slide, split into two slides. After any change, verify overflow is not introduced.

---

## Phase 1: Content Discovery (New Presentations)

Before designing, gather content. Use the **AskQuestion** tool. Batch related questions in one call when possible.

### Step 1.1: Presentation Context

Ask in one AskQuestion (or two if the tool limits options):

**Title:** "Presentation setup"

**Questions:**

1. **Purpose** — "What is this presentation for?"  
   Options: Pitch deck | Teaching/Tutorial | Conference talk | Internal presentation

2. **Length** — "Approximately how many slides?"  
   Options: Short (5-10) | Medium (10-20) | Long (20+)

3. **Content** — "Do you have the content ready, or do you need help structuring it?"  
   Options: I have all content ready | I have rough notes | I have a topic only

4. **Images** — "Do you have images to include?"  
   Options: No images | ./assets | Other (let user type/paste folder path)

5. **Inline editing** — "Edit text in the browser after generation?"  
   Options: Yes (recommended) | No  

Remember the inline-editing choice; it controls whether edit-related HTML/CSS/JS is included in Phase 3.

If the user has content, ask them to share it (text, bullets, etc.).

### Step 1.2: Image Evaluation (skip if "No images")

If the user provided an image folder:

1. **Scan** — List image files (`.png`, `.jpg`, `.jpeg`, `.gif`, `.svg`, `.webp`) in the folder.
2. **View** — Use the Read tool to see each image (agent is multimodal).
3. **Evaluate** — For each: filename, dimensions, what it shows, **USABLE** or **NOT USABLE** (with reason), content signal (e.g. "chat_ui.png" → "conversational interface"), shape, dominant colors.
4. **Propose outline** — Usable images inform the slide structure from the start (e.g. 3 screenshots → 3 feature slides). Present evaluation and proposed slide outline with image assignments.
5. **Confirm** — Use AskQuestion: title "Outline", question "Does this slide outline and image selection look right?", options: "Looks good, proceed" | "Adjust images" | "Adjust outline".

If "No images", skip the image pipeline entirely; use CSS-generated visuals only and go to Phase 2.

---

## Phase 2: Style Discovery

**Show, don't tell:** generate mini-previews and let the user pick.

### How users choose

- **Guided:** Answer mood → generate 3 preview HTML files → user picks one.
- **Direct:** User names a preset (e.g. "Use Bold Signal") → skip to Phase 3.

**Preset list (short):** Bold Signal, Electric Studio, Creative Voltage, Dark Botanical, Notebook Tabs, Pastel Geometry, Split Pastel, Vintage Editorial, Neon Cyber, Terminal Green, Swiss Modern, Paper & Ink. Full details in [STYLE_PRESETS.md](STYLE_PRESETS.md).

### Step 2.0: Style path

AskQuestion — Title: "Style"; Question: "How would you like to choose your presentation style?"  
Options: "Show me options" (generate 3 previews) | "I know what I want" (pick preset directly).

- If "I know what I want" → show preset picker (AskQuestion with preset names as options), then Phase 3.
- If "Show me options" → Step 2.1.

### Step 2.1: Mood (guided)

AskQuestion — Title: "Vibe"; Question: "What feeling should the audience have?"  
Options: Impressed/Confident | Excited/Energized | Calm/Focused | Inspired/Moved. Allow multiple (up to 2).

### Step 2.2: Generate style previews

Create **3 distinct style previews** as mini HTML files in `slide-previews/` (or `.cursor/slide-previews/`):

```
slide-previews/
├── style-a.html
├── style-b.html
└── style-c.html
```

Each: self-contained, single title slide, typography + palette + animation feel, ~50–100 lines. Pick 3 presets from STYLE_PRESETS.md based on mood (e.g. Impressed → Bold Signal, Electric Studio, Dark Botanical). Never use purple-on-white gradients, Inter/Roboto, or generic blue. If user provided a logo (USABLE), embed it (e.g. base64) in each preview.

Present paths to the user, then AskQuestion — Title: "Style"; Question: "Which style preview do you prefer?"  
Options: "Style A: [Name]" | "Style B: [Name]" | "Style C: [Name]" | "Mix elements". If "Mix elements", ask for specifics.

---

## Phase 3: Generate Presentation

Generate the full presentation from Phase 1 content and Phase 2 style.

- **No images:** Generate HTML with CSS-only visuals. Skip image pipeline.
- **With images:** Process images per [REFERENCE.md](REFERENCE.md) (Pillow: crop_circle, resize_max, add_padding as needed). Use **relative paths** in HTML (e.g. `assets/logo_round.png`), not base64. Place images per outline from Step 1.2.

**File layout:** `presentation.html` plus `assets/` if images; or `[name].html` and `[name]-assets/` for multiple decks.

**HTML/JS:** Include viewport-fitting base CSS, theme variables, SlidePresentation controller (keyboard, touch, wheel, progress bar, nav dots), Intersection Observer for `.visible` and `.reveal` animations. Full viewport CSS and checklist in [REFERENCE.md](REFERENCE.md). If user opted in for inline editing, add the edit button flow from REFERENCE.md (JS hover with delay timeout, not CSS sibling selector).

**Code quality:** Comments per section, semantic HTML, reduced-motion support, no negated CSS functions (use `calc(-1 * clamp(...))`). See REFERENCE.md for gotchas.

---

## Phase 4: PPT Conversion

When the user has a PowerPoint file:

1. **Extract** — Use Python `python-pptx` to extract slides, text, and images. Full extraction code in [REFERENCE.md](REFERENCE.md). Save images to `assets/`.
2. **Confirm** — Present extracted content (slide titles, content summary, image count) to the user.
3. **Style** — Run Phase 2 (style path + mood/previews or preset pick).
4. **Generate** — Build HTML from extracted content and chosen style, preserving text, images, order, and notes (as comments or separate file).

---

## Phase 5: Delivery

1. **Clean up** — Remove `slide-previews/` or `.cursor/slide-previews/` if it exists.
2. **Open** — Run `start presentation.html` (Windows) or `open presentation.html` (macOS) or `xdg-open presentation.html` (Linux) to open in browser.
3. **Summarize** — Tell the user file path, style name, slide count, navigation (arrows, space, scroll, dots). If inline editing was enabled, mention hover top-left or E key to edit and save.

---

## Troubleshooting

- **Fonts not loading** — Check Fontshare/Google Fonts URL and font names in CSS.
- **Animations not triggering** — Ensure Intersection Observer runs and `.visible` is applied.
- **Scroll snap broken** — `scroll-snap-type` on html; each `.slide` needs `scroll-snap-align: start`.
- **Mobile** — Disable heavy effects at 768px; test touch; reduce particles if used.
- **Viewport overflow** — See REFERENCE.md: density limits, mandatory CSS, and "when content doesn't fit".

---

## Additional resources

- **Viewport CSS, density table, edit button, animations, PPT extraction code:** [REFERENCE.md](REFERENCE.md)
- **Preset specs, fonts, colors, CSS gotchas:** [STYLE_PRESETS.md](STYLE_PRESETS.md)
