# Beautiful Slides

A Cursor agent skill for creating stunning, animation-rich HTML presentations — from scratch or by converting PowerPoint files.

## What This Does

**Beautiful Slides** helps non-designers create beautiful web presentations without knowing CSS or JavaScript. It uses a "show, don't tell" approach: the Cursor agent generates visual style previews and lets you pick what you like, then produces a single HTML file that runs in any browser.

### Key Features

- **Zero Dependencies** — Single HTML files with inline CSS/JS. No npm, no build tools, no frameworks.
- **Visual Style Discovery** — Pick from generated style previews instead of describing preferences in words.
- **PPT Conversion** — Convert existing PowerPoint files (.ppt, .pptx) to web, preserving text, images, and notes.
- **Anti-AI-Slop** — Curated distinctive styles that avoid generic AI aesthetics.
- **Production Quality** — Accessible, responsive, viewport-fitting slides with keyboard/touch navigation and reduced-motion support.

## Installation

The skill lives in this repo under `.cursor/skills/beautiful-slides/`. Use it in either of these ways:

### Project scope (recommended)

If you clone or use this repo, the skill is already available to the Cursor agent when you work in this project. No extra steps.

### Personal scope (all projects)

Copy the skill to your user skills directory so it’s available in any project:

```bash
# Create the skill directory
mkdir -p ~/.cursor/skills/beautiful-slides

# Copy the skill files from this repo
cp -r .cursor/skills/beautiful-slides/* ~/.cursor/skills/beautiful-slides/
```

**Note:** Do not install into `~/.cursor/skills-cursor/` — that directory is reserved for Cursor’s built-in skills.

## Usage

There is no slash command. In Cursor, describe what you want in natural language. The agent discovers this skill when your request matches creating or converting presentations.

### Create a new presentation

Examples:

- "I want to create a pitch deck for my AI startup"
- "Make slides for my conference talk on TypeScript"
- "Build a short presentation about our Q4 results"

The agent will:

1. Ask about purpose, length, content readiness, and whether you have images
2. If you have images, evaluate them and propose a slide outline for you to confirm
3. Ask how you want to choose style (previews vs picking a preset)
4. Generate 3 style previews (if guided) or use your chosen preset
5. Create the full HTML presentation and open it in your browser

### Convert a PowerPoint

Example:

- "Convert my presentation.pptx to a web slideshow"

The agent will extract text and images from the PPT, show you the result for confirmation, run style selection, then generate an HTML presentation with your content.

### Enhance an existing presentation

If you already have an HTML presentation from this skill, you can ask the agent to add slides, change content, or adjust the design. It will keep viewport fitting and content density rules.

## Included Styles

Presets are defined in `.cursor/skills/beautiful-slides/STYLE_PRESETS.md`. Summary:

| Preset | Vibe |
|--------|------|
| Bold Signal | Confident, high-impact |
| Electric Studio | Clean, professional |
| Creative Voltage | Energetic, retro-modern |
| Dark Botanical | Elegant, sophisticated |
| Notebook Tabs | Editorial, organized |
| Pastel Geometry | Friendly, approachable |
| Split Pastel | Playful, modern |
| Vintage Editorial | Witty, personality-driven |
| Neon Cyber | Futuristic, techy |
| Terminal Green | Developer-focused |
| Swiss Modern | Minimal, precise |
| Paper & Ink | Literary, thoughtful |

## Output

Each presentation is a single HTML file (plus an `assets/` folder if you use images). It includes:

- Keyboard navigation (arrows, space)
- Touch/swipe and mouse wheel
- Progress bar and navigation dots
- Scroll-triggered animations
- Responsive, viewport-fitting layout (no scrolling within a slide)
- Optional in-browser text editing (if you opted in when creating)

## Files

| File | Purpose |
|------|---------|
| `.cursor/skills/beautiful-slides/SKILL.md` | Main skill instructions for the Cursor agent |
| `.cursor/skills/beautiful-slides/STYLE_PRESETS.md` | Curated visual styles and font/color specs |
| `.cursor/skills/beautiful-slides/REFERENCE.md` | Viewport CSS, PPT extraction code, edit button, animations |
| `requirements.txt` | Python dependencies for PPT conversion and image processing |

## Requirements

- **Cursor** — The skill is used by the Cursor agent when you ask for presentations or PPT conversion.
- **PPT conversion** — Python with the `python-pptx` library.
- **Image processing** — If you include images and the agent processes them (e.g. crop, resize), Python with the `Pillow` library.

Install Python dependencies: `pip install -r requirements.txt`

## Philosophy

1. **You don’t need to be a designer.** You react to what you see; the agent handles the rest.
2. **Dependencies are debt.** A single HTML file will work for years without toolchains.
3. **Generic is forgettable.** Every deck should feel custom-crafted, not template-generated.
4. **Comments are kindness.** Generated code is structured and commented so you can tweak it later.

## License

MIT — Use it, modify it, share it.
