# Beautiful Slides — Reference

Detailed viewport CSS, extraction code, edit button, and animation patterns. Use when generating or modifying presentations.

---

## 1. Viewport Fitting (Mandatory)

Every slide MUST fit exactly in the viewport. No scrolling within slides.

### Content density limits per slide

| Slide Type    | Maximum Content                                    |
|---------------|----------------------------------------------------|
| Title slide   | 1 heading + 1 subtitle + optional tagline         |
| Content slide | 1 heading + 4-6 bullets OR 1 heading + 2 paragraphs |
| Feature grid  | 1 heading + 6 cards (2x3 or 3x2)                   |
| Code slide    | 1 heading + 8-10 lines of code                      |
| Quote slide   | 1 quote (max 3 lines) + attribution                 |
| Image slide   | 1 heading + 1 image (max 60vh height)               |

**If content exceeds these limits → Split into multiple slides.**

### Required base CSS (include in every presentation)

```css
/* ===========================================
   VIEWPORT FITTING: MANDATORY BASE STYLES
   =========================================== */

html, body {
    height: 100%;
    overflow-x: hidden;
}

html {
    scroll-snap-type: y mandatory;
    scroll-behavior: smooth;
}

.slide {
    width: 100vw;
    height: 100vh;
    height: 100dvh;
    overflow: hidden;
    scroll-snap-align: start;
    display: flex;
    flex-direction: column;
    position: relative;
}

.slide-content {
    flex: 1;
    display: flex;
    flex-direction: column;
    justify-content: center;
    max-height: 100%;
    overflow: hidden;
    padding: var(--slide-padding);
}

:root {
    --title-size: clamp(1.5rem, 5vw, 4rem);
    --h2-size: clamp(1.25rem, 3.5vw, 2.5rem);
    --h3-size: clamp(1rem, 2.5vw, 1.75rem);
    --body-size: clamp(0.75rem, 1.5vw, 1.125rem);
    --small-size: clamp(0.65rem, 1vw, 0.875rem);
    --slide-padding: clamp(1rem, 4vw, 4rem);
    --content-gap: clamp(0.5rem, 2vw, 2rem);
    --element-gap: clamp(0.25rem, 1vw, 1rem);
}

.card, .container, .content-box {
    max-width: min(90vw, 1000px);
    max-height: min(80vh, 700px);
}

.feature-list, .bullet-list { gap: clamp(0.4rem, 1vh, 1rem); }
.feature-list li, .bullet-list li { font-size: var(--body-size); line-height: 1.4; }

.grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(min(100%, 250px), 1fr));
    gap: clamp(0.5rem, 1.5vw, 1rem);
}

img, .image-container {
    max-width: 100%;
    max-height: min(50vh, 400px);
    object-fit: contain;
}

/* Short viewports (< 700px height) */
@media (max-height: 700px) {
    :root {
        --slide-padding: clamp(0.75rem, 3vw, 2rem);
        --content-gap: clamp(0.4rem, 1.5vw, 1rem);
        --title-size: clamp(1.25rem, 4.5vw, 2.5rem);
        --h2-size: clamp(1rem, 3vw, 1.75rem);
    }
}

@media (max-height: 600px) {
    :root {
        --slide-padding: clamp(0.5rem, 2.5vw, 1.5rem);
        --content-gap: clamp(0.3rem, 1vw, 0.75rem);
        --title-size: clamp(1.1rem, 4vw, 2rem);
        --body-size: clamp(0.7rem, 1.2vw, 0.95rem);
    }
    .nav-dots, .keyboard-hint, .decorative { display: none; }
}

@media (max-height: 500px) {
    :root {
        --slide-padding: clamp(0.4rem, 2vw, 1rem);
        --title-size: clamp(1rem, 3.5vw, 1.5rem);
        --h2-size: clamp(0.9rem, 2.5vw, 1.25rem);
        --body-size: clamp(0.65rem, 1vw, 0.85rem);
    }
}

@media (max-width: 600px) {
    :root { --title-size: clamp(1.25rem, 7vw, 2.5rem); }
    .grid { grid-template-columns: 1fr; }
}

@media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
        animation-duration: 0.01ms !important;
        transition-duration: 0.2s !important;
    }
    html { scroll-behavior: auto; }
}
```

### Overflow prevention checklist

- [ ] Every `.slide` has `height: 100vh; height: 100dvh; overflow: hidden;`
- [ ] All font sizes use `clamp(min, preferred, max)`
- [ ] All spacing uses `clamp()` or viewport units
- [ ] Content containers have `max-height` constraints
- [ ] Images have `max-height: min(50vh, 400px)` or similar
- [ ] Grids use `auto-fit` with `minmax()` for responsive columns
- [ ] Breakpoints for heights: 700px, 600px, 500px
- [ ] No fixed pixel heights on content elements
- [ ] Content per slide respects density limits

### When content doesn't fit

**DO:** Split into multiple slides; reduce bullets (max 5-6); shorten text (1-2 lines per bullet); use smaller code snippets; create a "continued" slide; when adding images to a full slide, move image to a new slide or reduce other content first.

**DON'T:** Reduce font size below readable limits; remove padding entirely; allow scrolling; cram content; add images without checking if the slide is already at capacity.

### Testing viewports

Recommend testing at: Desktop 1920×1080, 1440×900, 1280×720; Tablet 1024×768, 768×1024; Mobile 375×667, 414×896; Landscape 667×375, 896×414.

---

## 2. Enhancing existing presentations (Mode C)

Before adding content: read slide structure, check density limits, ensure new content fits.

**Adding images:** Use `max-height: min(50vh, 400px)`. If the slide already has max content (e.g. heading + 4 bullets), split into two slides or reduce to 2-3 bullets then add image.

**Adding text:** Max 4-6 bullets or 2 paragraphs per slide; otherwise split or continue on next slide.

**After any change:** Verify `.slide` has `overflow: hidden`, new elements use `clamp()`, new images have viewport-relative max-height, and total content respects density. If modifications would cause overflow, automatically split content and tell the user.

---

## 3. Phase 4 — PPT extraction (python-pptx)

Install: `pip install python-pptx`

```python
from pptx import Presentation
import os

def extract_pptx(file_path, output_dir):
    """Extract slides, text, and images from a PowerPoint file."""
    prs = Presentation(file_path)
    slides_data = []
    assets_dir = os.path.join(output_dir, 'assets')
    os.makedirs(assets_dir, exist_ok=True)

    for slide_num, slide in enumerate(prs.slides):
        slide_data = {
            'number': slide_num + 1,
            'title': '',
            'content': [],
            'images': [],
            'notes': ''
        }

        for shape in slide.shapes:
            if shape.has_text_frame:
                if shape == slide.shapes.title:
                    slide_data['title'] = shape.text
                else:
                    slide_data['content'].append({'type': 'text', 'content': shape.text})

            if shape.shape_type == 13:  # Picture
                image = shape.image
                image_ext = image.ext
                image_name = f"slide{slide_num + 1}_img{len(slide_data['images']) + 1}.{image_ext}"
                image_path = os.path.join(assets_dir, image_name)
                with open(image_path, 'wb') as f:
                    f.write(image.blob)
                slide_data['images'].append({
                    'path': f"assets/{image_name}",
                    'width': shape.width,
                    'height': shape.height
                })

        if slide.has_notes_slide:
            slide_data['notes'] = slide.notes_slide.notes_text_frame.text

        slides_data.append(slide_data)

    return slides_data
```

Present extracted content to the user, then run Phase 2 (style) and generate HTML preserving text, images, order, and notes.

---

## 4. Image processing (Pillow)

Install: `pip install Pillow`

```python
from PIL import Image, ImageDraw

def crop_circle(input_path, output_path):
    """Crop to circle with transparent background."""
    img = Image.open(input_path).convert('RGBA')
    w, h = img.size
    size = min(w, h)
    left, top = (w - size) // 2, (h - size) // 2
    img = img.crop((left, top, left + size, top + size))
    mask = Image.new('L', (size, size), 0)
    ImageDraw.Draw(mask).ellipse([0, 0, size, size], fill=255)
    img.putalpha(mask)
    img.save(output_path, 'PNG')

def resize_max(input_path, output_path, max_dim=1200):
    """Resize so largest dimension <= max_dim."""
    img = Image.open(input_path)
    img.thumbnail((max_dim, max_dim), Image.LANCZOS)
    img.save(output_path, quality=85)

def add_padding(input_path, output_path, padding=40, bg_color=(0, 0, 0, 0)):
    """Add transparent padding."""
    img = Image.open(input_path).convert('RGBA')
    w, h = img.size
    new = Image.new('RGBA', (w + 2*padding, h + 2*padding), bg_color)
    new.paste(img, (padding, padding), img)
    new.save(output_path, 'PNG')
```

Use: circular crop for logos; resize for large files; add_padding for breathing room. Save processed files with a suffix (e.g. `_processed`); never overwrite originals.

---

## 5. Edit button (when user opts in)

Only include if the user chose "Yes" for inline editing in Phase 1. Do **not** use CSS `~` sibling selector for hover — it breaks when the toggle has `pointer-events: none`. Use JS with a delay timeout.

**HTML:**
```html
<div class="edit-hotzone"></div>
<button class="edit-toggle" id="editToggle" title="Edit mode (E)">✏️</button>
```

**CSS (visibility via JS classes only):**
```css
.edit-hotzone {
    position: fixed; top: 0; left: 0;
    width: 80px; height: 80px;
    z-index: 10000;
    cursor: pointer;
}
.edit-toggle {
    opacity: 0;
    pointer-events: none;
    transition: opacity 0.3s ease;
    z-index: 10001;
}
.edit-toggle.show,
.edit-toggle.active {
    opacity: 1;
    pointer-events: auto;
}
```

**JS (hotzone hover with 400ms grace, click, E key):**
```javascript
document.getElementById('editToggle').addEventListener('click', () => { editor.toggleEditMode(); });

const hotzone = document.querySelector('.edit-hotzone');
const editToggle = document.getElementById('editToggle');
let hideTimeout = null;

hotzone.addEventListener('mouseenter', () => { clearTimeout(hideTimeout); editToggle.classList.add('show'); });
hotzone.addEventListener('mouseleave', () => {
    hideTimeout = setTimeout(() => { if (!editor.isActive) editToggle.classList.remove('show'); }, 400);
});
editToggle.addEventListener('mouseenter', () => { clearTimeout(hideTimeout); });
editToggle.addEventListener('mouseleave', () => {
    hideTimeout = setTimeout(() => { if (!editor.isActive) editToggle.classList.remove('show'); }, 400);
});

hotzone.addEventListener('click', () => { editor.toggleEditMode(); });

document.addEventListener('keydown', (e) => {
    if ((e.key === 'e' || e.key === 'E') && !e.target.getAttribute('contenteditable')) {
        editor.toggleEditMode();
    }
});
```

---

## 6. Effect → feeling mapping

- **Dramatic / Cinematic:** Slow fade-ins (1–1.5s), scale 0.9→1, dark + spotlight, parallax, full-bleed images.
- **Techy / Futuristic:** Neon glow, particle canvas, grid patterns, monospace accents, cyan/magenta palette.
- **Playful / Friendly:** Bouncy easing, large radius, pastels, floating/bobbing.
- **Professional / Corporate:** Subtle 200–300ms animations, clean sans, navy/slate, minimal decoration.
- **Calm / Minimal:** Very slow motion, high whitespace, muted palette, serif, generous padding.
- **Editorial / Magazine:** Strong type hierarchy, pull quotes, serif headlines + sans body, one accent.

---

## 7. Animation patterns

**Entrance (fade + slide up):**
```css
.reveal {
    opacity: 0;
    transform: translateY(30px);
    transition: opacity 0.6s var(--ease-out-expo), transform 0.6s var(--ease-out-expo);
}
.visible .reveal { opacity: 1; transform: translateY(0); }
.reveal:nth-child(1) { transition-delay: 0.1s; }
.reveal:nth-child(2) { transition-delay: 0.2s; }
/* ... */
```

**Scale in:** `transform: scale(0.9)` → `scale(1)`. **Slide from left:** `translateX(-50px)` → `0`. **Blur in:** `filter: blur(10px)` → `blur(0)`.

**Backgrounds:** Gradient mesh (radial-gradients), grid (`linear-gradient` 1px lines), noise (inline SVG).

---

## 8. CSS gotchas

**Negating CSS functions:** Browsers ignore `-clamp()`, `-min()`, `-max()` with no error. Use `calc(-1 * clamp(...))` instead, e.g. `right: calc(-1 * clamp(28px, 3.5vw, 44px));`.

---

## 9. Image placement

Reference images with relative paths: `src="assets/logo_round.png"`. Adapt `.screenshot` border/shadow to the style accent (e.g. Dark Botanical gold: `rgba(197, 160, 89, 0.2)`). Base classes:

```css
.slide-image {
    max-width: 100%;
    max-height: min(50vh, 400px);
    object-fit: contain;
    border-radius: 8px;
}
.slide-image.screenshot {
    border: 1px solid rgba(255,255,255,0.1);
    border-radius: 12px;
    box-shadow: 0 8px 32px rgba(0,0,0,0.3);
}
.slide-image.logo { max-height: min(30vh, 200px); }
```
