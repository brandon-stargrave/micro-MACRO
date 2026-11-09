# Micrographics Generator: Design Requirements

---

## 1. Overview

A p5.js application for generating algorithmic vector micrographics in a sci-fi HUD / techno-industrial aesthetic. The application produces compositions ranging from dense, text-laden diagnostic panels to bold, minimal symbolic glyphs, controlled primarily by a single micro-to-macro slider.

**Primary output format is SVG** — all geometry is exported as `<path>` elements suitable for use with **pen plotters** (AxiDraw, iDraw, etc.) as well as laser cutters, vinyl cutters, and other CNC vector tools. Raster PNG export is also supported as a secondary output. The pen plotter use case is the driving reason for the vector-first architecture: all drawing must reduce to single-stroke paths, all text must be rendered as stroked paths (not filled outlines), and all visual effects must be representable as geometry rather than raster operations.

The visual language, element taxonomy, and generation rules are defined in the companion document: `micrographics-style-guide.md`.

---

## 2. Application Structure

### 2.1 Single-File Architecture
- Single `index.html` containing all HTML, CSS, and JavaScript
- p5.js loaded via CDN (latest stable, currently v1.9.0)
- No build process, no frameworks, vanilla JavaScript
- Organized with section comment headers:
  ```
  // ========================================================
  // SECTION NAME
  // ========================================================
  ```

### 2.2 Layout
- **Left sidebar**: 370px fixed width, scrollable, contains all controls
- **Right canvas area**: `flex: 1`, contains the p5.js canvas centered in the viewport
- Sidebar border on right edge with accent glow
- Canvas area uses a subtle background pattern (grid or dot matrix pattern, thematic to the app)

### 2.3 UI Design Language

While the application inherits its layout structure, QoL features, and control patterns from the existing repos (Dither Playground, Bitwise Explorer), the **visual design of the UI itself should be a direct expression of the micrographics style guide**. The interface is not merely a tool that produces these graphics — it looks, feels, and behaves like something that *belongs inside* them. The UI is a HUD. The sidebar is a diagnostic panel. The controls are instrument readouts. Every surface is an opportunity to echo the generated output.

#### Guiding Principle
The existing repos use a clean, dark, glassy UI. This application takes that foundation and pushes it further — adding animated texture, procedural decoration, and interactive visual feedback drawn from the same design vocabulary as the micrographics themselves. The UI should feel alive, monitored, and slightly unstable — like a system the user is operating from inside.

#### UI Visual Effects and Flares

**Animated Backgrounds and Textures:**
- **Sidebar background**: Subtle animated dot grid or cross-pattern grid that slowly drifts, pulses, or shimmers at very low opacity. Not static — the grid is alive. Implemented via a small canvas or CSS animation behind the sidebar content.
- **Canvas container background**: Animated scan line sweep or slow-moving concentric reticle pattern at very low opacity, visible when no generation fills the space. Suggests an active viewport waiting for data.
- **Section dividers**: Instead of plain horizontal rules, use dashed lines, tick-marked rules, or faint Morse-pattern lines between collapsible sections.

**Button and Control Interactions:**
- **Generate button hover**: Pixelated noise shimmer animation across the button surface — random small squares flicker at varying opacity, suggesting data being assembled. On click: a brief glitch-flash (horizontal slice displacement on the button text).
- **Randomize button hover**: The existing sparkle/pop animation is enhanced with a brief scan-line sweep across the button face.
- **Slider thumb**: Styled as a small crosshair, reticle, or targeting indicator rather than a plain circle or rectangle. On hover, a faint concentric ring pulses outward from the thumb.
- **Checkbox toggles**: When toggled on, a brief "power-up" flash — the glowing dot expands and contracts. When toggled off, a brief "shutdown" fade with a tiny downward flicker.
- **Number input spin buttons**: On click, a brief directional arrow flash (up or down) in the accent color.
- **Dropdown selects**: On open, a subtle vertical scan-line sweep across the dropdown options.
- **Collapsible section headers (`<summary>`)**: A small animated indicator — a rotating crosshair, a pulsing dot, or a cycling bracket pattern — beside the section title when the section is open, suggesting an active/monitored subsystem. Static/dimmed when closed.

**Micro-Macro Slider (Hero Control) Special Treatment:**
- The slider track renders a miniature visualization of the micro-to-macro concept: at the left end, a dense cluster of tiny shapes/dots; at the right end, a single bold shape. These can be static or subtly animated.
- As the slider moves, the zone labels (`DEEP MICRO`, `MICRO`, `MID`, `MACRO`, `DEEP MACRO`) illuminate when the thumb enters their zone — the active zone label glows in accent color, others dim.
- The thumb itself could be a small micrographic element that changes form as it moves: a detailed reticle at the left, simplifying to a bold dot at the right.

**Status and Feedback:**
- **Seed display**: Render the hex string in a monospace font with a faint blinking cursor at the end, as if it's a live terminal value.
- **Generation progress**: During multi-mode generation with many cells, a small progress indicator styled as a segmented bar or loading barcode animates in the Generate button.
- **Export confirmation**: On successful SVG/PNG export, a brief "TRANSMITTED" or "CAPTURED" flash in the accent color near the export buttons, styled as a HUD confirmation.
- **Error states in the UI** (not the canvas): If a control is in an invalid state, the label flickers or gains a subtle warning-stripe underline.

**Corner Bracket Framing:**
- The sidebar itself can use corner bracket accents at its four corners (or just the top-left and bottom-right) — the same containment language used in the generated graphics, applied to the tool's own interface.
- Collapsible sections, when open, gain faint corner brackets around their content area.

**Ambient Animation:**
- A very slow, subtle ambient animation runs at all times in the sidebar — a faint scan line moving top-to-bottom at very low opacity, or a barely-visible dot that orbits slowly in one corner. This gives the UI a sense of being "on" — a live system, not a static page. Performance must be negligible — use CSS animations, not JavaScript render loops.

#### Performance Constraint
All UI visual effects must be CSS-driven or use lightweight `requestAnimationFrame` loops independent of the main canvas. They must not interfere with generation performance. If a device cannot handle them, effects should degrade gracefully (e.g., `prefers-reduced-motion` media query disables animations).

---

**Color Palette:**
| Token | Value | Usage |
|---|---|---|
| `--bg-deep` | `#0a0612` | Page background |
| `--bg-sidebar` | `#110b1f` | Sidebar background |
| `--accent-primary` | `#00ff88` | Primary accent, active states, slider fills |
| `--accent-secondary` | `#00e5ff` | Labels, secondary highlights |
| `--text-primary` | `#d0d8e8` | Body text |
| `--text-muted` | `#5a6a80` | Hints, disabled states |
| `--border-subtle` | `#ffffff12` | Dividers, inactive borders |
| `--border-glow` | `#00ff8844` | Active borders, sidebar edge |

**Glass Morphism:** `backdrop-filter: blur(12px)` on buttons, inputs, select elements.

**Gradient Buttons:** `linear-gradient(135deg, #0d2a1a88, #0a1f2e88)` with hover brightness increase.

**Custom Form Controls:**
- Number inputs with custom spin buttons (18px width)
- Select dropdowns with spin buttons and dropdown arrow overlay
- Checkboxes as circles with glowing inner dot when checked
- Range sliders with styled track and thumb

**Typography (UI):**
- Font: `'Segoe UI', system-ui, sans-serif`
- Labels: `0.85em`, colored `--accent-secondary`
- Hints: `0.72em`, colored `--text-muted`
- Control group bottom margin: `14px`

**Typography (Generated Output):**

Fonts are organized into two directories, each serving a different role in the micro-to-macro scale:

#### Micro Fonts (`/fonts/micro/`)
Single-line (single-stroke) SVG fonts. Their glyphs are defined as open paths, not filled outlines. They render as stroked lines matching the foreground color and stroke weight. This makes them ideal for pen plotter output — each character is drawn as a continuous toolpath, not a filled region.

The set is **Plotterm**, our own monospace single-stroke family: `Plotterm Vector`, `Round`, `Thread`, `Typewriter` (the Auto pool), plus `Scan`, `Pin`, `Script` and `Blackletter` (chosen explicitly in the Type Engine).

- These fonts set **all small type** at every scale position: labels, readouts, treatment text (cipher, flood, hex dumps), glitch decomposition (Plotterm Vector's lattice strokes) and page furniture
- The old line-segment glyphs remain only as a fallback before fonts load, or for a character Plotterm lacks

#### Macro Fonts (`/fonts/macro/`)
The H74 outline faces (OTF). Display type only — hero words, index numerals, big headings — and glyph shapes. Above scale 0.3, type at least 6× min text size (falling to 3× by scale 0.75) may be set in them; smaller type is always Plotterm, and choosing a Macro Font in the Type Engine never changes that.

- Macro fonts are rendered by extracting glyph outlines via opentype.js and converting them to filled `<path>` elements in SVG
- For pen plotter compatibility, macro font glyphs are rendered as **outline strokes** (the path traces the glyph boundary, not a fill) — this produces a single toolpath following the letter's silhouette
- Macro font text elements are treated as graphic shapes — sized large, used sparingly (one word or short code, never paragraphs)
- Maximum macro font usage per composition: 1–2 instances. The generator hard-caps this regardless of settings.
- At mid scale (0.4–0.6): macro fonts may appear at very low probability (~10%), using only 1 instance
- At deep macro (0.8–1.0): macro fonts are the preferred option for any text that appears, though text itself is rare at this scale

The macro font directory is initially empty and populated by the user with their preferred display fonts. If the directory is empty or fonts fail to load, the generator falls back to micro fonts at all scale positions.

#### Font Directory Loading
- On startup, the app scans both font directories and loads all `.ttf`, `.otf`, and `.woff` files found via opentype.js
- Font metadata (name, character set) is cached for selection logic
- The directory-based approach allows users to add/remove fonts without modifying code — drop a font file in the appropriate directory and it becomes available
- A Font Bias control is exposed in Geometry Parameters (see 3.10) to let the user weight toward a preferred font from the loaded set

#### Pen Plotter Rendering Rules
All text, regardless of font source, must produce pen-plotter-compatible SVG output:
- **Micro fonts**: Rendered as stroked open paths (single-line). Each glyph is one continuous or near-continuous toolpath. This is native to single-line font design.
- **Macro fonts**: Rendered as stroked closed paths tracing the glyph outline. The fill is `none` — the pen traces the letter's boundary. For complex glyphs with counters (holes), inner and outer contours are both drawn as separate stroked paths.
- There is no `<text>` element strategy in SVG export — all text is always `<path>` elements. The paths ARE the text.

**Collapsible Sections:** `<details>` elements with styled `<summary>` for parameter grouping. Open/close with smooth transition.

---

## 3. Controls Specification

The sidebar is organized into collapsible `<details>` sections, ordered top-to-bottom as follows:

---

### 3.1 Output Dimensions

| Control | Type | Default | Range/Options | Notes |
|---|---|---|---|---|
| Width | number input | 800 | 64–4096 px | Output canvas width |
| Height | number input | 800 | 64–4096 px | Output canvas height |
| Margin | number input | 20 | 0–200 px | Uniform margin inside the output boundary |
| Margin Link toggle | checkbox | on | — | When on, all four margins are uniform. When off, expose Top/Right/Bottom/Left as four independent number inputs. |
| Background Color | color picker | `#0a0612` | any | Fill color for the output background |
| Foreground Color | color picker | `#d0d8e8` | any | Stroke/fill color for all generated elements |

**Hint text below color pickers:** "Single foreground color on solid background. Monochrome by design."

---

### 3.2 Micro–Macro Slider (Primary Control)

This is the hero control — visually prominent, spanning the full sidebar width.

| Control | Type | Default | Range | Notes |
|---|---|---|---|---|
| Scale | range slider | 0.0 | 0.00–1.00, step 0.01 | The primary micro-to-macro axis. Default 0.0 (full micro) for initial load generation. |

**Visual treatment:**
- Full sidebar width (minus padding)
- Taller track than standard sliders (6–8px)
- Labeled endpoints: `MICRO` on the left, `MACRO` on the right
- Current numeric value displayed above or beside the thumb
- Five zone tick marks at 0.0, 0.2, 0.4, 0.6, 0.8, 1.0 with subtle labels beneath: `DEEP MICRO`, `MICRO`, `MID`, `MACRO`, `DEEP MACRO`
- Track fill gradient from left to right: dense pattern → sparse (or accent color density fades)

**Behavioral effect:** This slider drives element filtering, detail shedding, rendering mode, element count, and all scale-dependent parameters as defined in the style guide Section 12.

---

### 3.3 Composition Mode

| Control | Type | Default | Options | Notes |
|---|---|---|---|---|
| Mode | toggle (two buttons) | Single | Single / Multi | Styled as a segmented control / tab pair |

#### Single Mode
Generates one composition filling the full output area (minus margins). The micro-macro slider determines the type and detail level of that single composition.

No additional controls exposed.

#### Multi Mode
Recursively subdivides the output area into a grid of rectangular cells, then generates an independent composition in each cell. Smaller cells bias toward micro; larger cells bias toward macro.

**Multi Mode Parameters (revealed when Multi is selected):**

| Control | Type | Default | Range | Notes |
|---|---|---|---|---|
| Subdivision Depth | range slider | 3 | 1–6 | Number of recursive subdivision passes. 1 = 2–4 cells. 6 = potentially hundreds of cells. |
| Subdivision Irregularity | range slider | 0.5 | 0.00–1.00 | 0 = perfectly even grid. 1 = highly irregular, Mondrian-like splits. |
| Min Cell Size | number input | 60 | 20–400 px | Minimum width or height of any cell. Prevents subdivision below this threshold. |
| Cell Padding | number input | 4 | 0–20 px | Internal padding within each cell (between cell border and generated content) |
| Cell Gap | number input | 2 | 0–20 px | Gutter space between adjacent cells. Applied as half-gap on each side of each cell. Distinct from Cell Padding (which is inside the cell) — this is the space between cells. |
| Scale Bias | range slider | 0.0 | -1.0–1.0 | Shifts the per-cell micro-macro mapping. Negative = all cells bias toward micro. Positive = all cells bias toward macro. 0 = cell size determines scale naturally. |

**Subdivision Algorithm:**
1. Start with the full output rectangle (minus margins).
2. For each subdivision pass: select existing cells to split. Each cell has a probability of being split based on its current size relative to Min Cell Size.
3. Each split is either horizontal or vertical (chosen randomly, or biased by the cell's aspect ratio to avoid extremely narrow cells).
4. The split position is determined by Irregularity: at 0.0, always split at the midpoint; at 1.0, the split point is uniformly random within the cell (clamped to avoid cells smaller than Min Cell Size).
5. Repeat for the specified Depth.

**Per-Cell Scale Mapping:**
Each cell's effective micro-macro scale is computed from its area, the global slider position, and the Scale Bias:

```
// Normalize cell area to 0–1 range across all cells in this generation
areaMin = min area of all cells
areaMax = max area of all cells
areaNorm = (cellArea - areaMin) / (areaMax - areaMin)  // 0 for smallest, 1 for largest
// If all cells are same size (irregularity=0), areaNorm = 0.5 for all

// Map through the global slider. The slider defines the center of the range.
// The range width is determined by the spread of cell sizes.
rangeWidth = 0.6  // cells span ±0.3 around the slider position
baseScale = globalSlider + (areaNorm - 0.5) * rangeWidth

// Apply user bias
cellScale = clamp(baseScale + scaleBias, 0.0, 1.0)
```

This means: the global slider sets the midpoint, large cells skew macro relative to it, small cells skew micro. Scale Bias shifts everything uniformly.

**Micro-Macro Slider Influence on Subdivision:**
The global micro-macro slider has a secondary, non-linear effect on the subdivision process itself. The subdivision parameters (Depth, Irregularity, Min Cell Size) remain the primary controls, but the slider applies a soft bias:

- **Depth modulation:** `effectiveDepth = round(depth - (globalSlider - 0.5) * 1.5)`, clamped to `[1, depth]`. At deep macro (slider=1.0), effective depth is reduced by ~1 level. At deep micro (slider=0.0), depth is increased by ~1 level (up to the user's setting). This means more cells at micro, fewer at macro.
- **Min Cell Size modulation:** `effectiveMinCell = minCellSize * (1.0 + globalSlider * 0.3)`. At macro, minimum cell size grows slightly, preventing tiny cells that would generate micro content in a macro context.
- The user's explicit parameters always cap these modulations — the slider can reduce subdivision below the user's settings but never exceed them.

---

### 3.4 Borders

| Control | Type | Default | Notes |
|---|---|---|---|
| Borders | toggle (on/off) | Off | Global toggle for border rendering |

**When borders are ON:**
- In **Single Mode**: one border is generated around the entire composition.
- In **Multi Mode**: each cell receives its own independently generated border.

**Border sub-parameters (revealed when Borders is ON):**

| Control | Type | Default | Options | Notes |
|---|---|---|---|---|
| Border Style | select dropdown | Auto | Auto, Corner Bracket, Broken Path, Manhattan, Technical/HUD, Ornamental | "Auto" selects style per cell based on its micro-macro position (see style guide Section 8.3) |
| Border Weight | range slider | 1.0 | 0.5–3.0 | Stroke weight multiplier for border lines, relative to content stroke weight |
| Border Padding | number input | 6 | 0–30 px | Space between the border and the content it encloses |

---

### 3.5 Generate Button

A large, prominent button spanning the full sidebar width.

**Label:** `GENERATE`

**Visual treatment:**
- Taller than standard buttons (44–50px)
- Gradient background with accent glow
- Active/click animation: brief brightness pulse
- While generating: label changes to `GENERATING...` with a subtle animation (pulse or dot-dot-dot)

**Behavior:**
- Clears the canvas
- Runs the full generation pipeline:
  1. Apply dimensions and margins
  2. If Multi mode: run subdivision algorithm to produce cell rectangles
  3. For each cell (or the single canvas in Single mode): select archetype, filter elements, generate composition at the cell's effective scale
  4. If borders enabled: generate and render borders
  5. Render all elements to the p5.js canvas
- All draw calls must record their geometry as SVG path data in parallel (see Section 4)

---

### 3.6 Randomize Button

Positioned directly below or beside Generate.

**Label:** `RANDOMIZE` (with sparkle icon)

**Visual treatment:** Same hero styling as existing repos — "pop" animation (label scale 1 -> 1.12 -> 0.96 -> 1 over 0.4s) and flash (brightness pulse) on click.

**Behavior:**
- Randomizes all non-dimensional parameters: micro-macro scale, mode, subdivision depth/irregularity, borders, all generator toggles, seed
- Preserves: output dimensions, margins, colors
- Then auto-triggers Generate

---

### 3.7 Export

| Control | Type | Notes |
|---|---|---|
| Export SVG | button | Downloads the current composition as an `.svg` file. All geometry exported as `<path>` elements. Pen plotter ready — all strokes, no fills. |
| Export PNG | button | Downloads a rasterized PNG at the current output dimensions. Secondary output for preview/sharing. |
| Copy SVG | button | Copies SVG source to clipboard. Brief "Copied!" confirmation. |
| Filename prefix | text input (default: `micrographic`) | Prepended to exported filenames with a timestamp. |

**Keyboard Shortcuts:**

| Key | Action |
|---|---|
| `Enter` | Generate (same as pressing the Generate button) |
| `R` | Randomize (same as pressing Randomize) |
| `S` | Export SVG |
| `P` | Export PNG |
| `Cmd/Ctrl+C` | Copy SVG to clipboard (when canvas is focused, not when a text input is focused) |

Keyboard shortcuts are suppressed when any text input or number input is focused, to avoid conflicts with typing.

---

### 3.8 Seed

| Control | Type | Default | Notes |
|---|---|---|---|
| Seed | text input | (random on load) | Deterministic seed string. Same seed + same parameters = identical output. |
| New Seed | small button | — | Generates a new random seed and auto-triggers Generate. |
| Lock Seed | checkbox | off | When locked, Generate reuses the current seed. When unlocked, each Generate picks a new seed. |

**Seed display format:** 8-character hex string (e.g., `7f2a9b03`). Displayed in monospace font for thematic consistency.

---

### 3.9 Generator Types (Element Toggles)

A collapsible section listing each element archetype and geometry family with individual enable/disable toggles. This allows the user to constrain the generator to specific element types.

**Archetype Controls:**

Each archetype has two controls on a single row: an enable/disable toggle (checkbox) and a probability weight slider.

| Toggle | Probability | Maps to |
|---|---|---|
| Status Panel | 0.0–1.0 (default 0.5) | Archetype 6.1 |
| Targeting Reticle | 0.0–1.0 (default 0.5) | Archetype 6.2 |
| Meter / Gauge | 0.0–1.0 (default 0.5) | Archetype 6.3 |
| Data Readout | 0.0–1.0 (default 0.5) | Archetype 6.4 |
| Node / Network | 0.0–1.0 (default 0.5) | Archetype 6.5 |
| Standalone Glyph | 0.0–1.0 (default 0.5) | Archetype 6.6 |
| Globe / Sphere | 0.0–1.0 (default 0.5) | Archetype 6.7 |
| Seal / Badge | 0.0–1.0 (default 0.5) | Archetype 6.8 |
| Error Message | 0.0–1.0 (default 0.5) | Archetype 6.9 |
| Terminal Block | 0.0–1.0 (default 0.5) | Archetype 6.10 |
| Encoded Data | 0.0–1.0 (default 0.5) | Archetype 6.11 |
| Dataviz Fragment | 0.0–1.0 (default 0.5) | Archetype 6.12 |

**Probability behavior:** When the generator selects an archetype for a cell, it draws from the pool of enabled archetypes weighted by their probability sliders. The weights are relative — if only two archetypes are enabled at 0.5 and 1.0, the second is twice as likely. A slider at 0.0 with the toggle ON means the archetype is eligible but extremely rare (not zero — treated as a floor of ~0.01). Disabling the toggle excludes the archetype entirely.

**Probability also factors in viable range proximity:** An archetype whose viable range is centered on the current scale gets a natural boost; one barely in range gets attenuated. The user's probability slider multiplies on top of this.

**Layout:** Each row is compact: `[checkbox] [label] [thin slider]`. The slider is smaller than standard sliders (shorter track, 60–80px) to fit alongside the label and checkbox on one line.

**Hint text:** "Disabled archetypes are excluded from generation. Probability sliders weight selection. At least one must be enabled."

**Quick actions row:** `All On` | `All Off` | `Micro Only` | `Macro Only` | `Reset Weights`
- `Micro Only`: enables only archetypes whose viable range includes values < 0.35
- `Macro Only`: enables only archetypes whose viable range includes values > 0.65
- `Reset Weights`: sets all probability sliders back to 0.5

---

### 3.10 Geometry Parameters

A collapsible section exposing fine-grained control over the generation process.

**Shape Controls:**

| Control | Type | Default | Range | Notes |
|---|---|---|---|---|
| Symmetry Bias | select | Any | Any, Radial, Bilateral, Rotational, Asymmetric | Constrains which symmetry classes the generator selects from |
| Max Radial Arms | range slider | 12 | 3–24 | Upper limit on arm/spoke/petal count for radial elements |
| Organic Character | range slider | 0.3 | 0.0–1.0 | How much biomorphic/curved forms are favored over pure geometry |
| Stroke Weight | readout | Pen ⌀ in px | — | Base stroke weight: follows Pen Diameter. Role weights scale it on screen. |
| Fill Probability | range slider | 0.0 | 0.0–1.0 | 0 = all line/outline. 1 = all solid fill. Intermediate = mixed. Overridden toward solid at macro end. |
| Font Bias | select | Auto | Auto, + all loaded font names | "Auto" selects font per text role and scale position. Selecting a specific font weights the generator toward it. Dropdown is dynamically populated from loaded fonts in both `/fonts/micro/` and `/fonts/macro/` directories. |
| Soft Edge Filtering | checkbox | on | — | When on, elements near the edge of their viable scale range appear at reduced probability (smooth transition). When off, hard cutoff at range boundaries. |

**Detail Controls:**

| Control | Type | Default | Range | Notes |
|---|---|---|---|---|
| Text Density | select | Auto | Auto, None, Sparse, Medium, Dense | "Auto" derives from micro-macro position. "None" disables all text at any scale. Non-auto values act as override — see interaction rules below. |
| Concentric Ring Limit | range slider | 4 | 0–8 | Max number of concentric rings/nested forms in any element |
| Tick Mark Density | range slider | 0.5 | 0.0–1.0 | Probability/density of tick marks on arcs and lines |
| Greeble Amount | range slider | 0.5 | 0.0–1.0 | Overall density of secondary/tertiary detail (annotation text, small accent elements, connector stubs). 0 = clean. 1 = maximum density. See interaction rules below. |

**Parameter vs. Scale Interaction Rules:**

The micro-macro scale provides a *baseline* for detail levels. The user's manual controls can push detail **beyond** what the scale would normally allow, but the effect is attenuated at the extremes to prevent total aesthetic collapse.

- **Greeble Amount:** The scale-derived greeble level sets a baseline. The Greeble Amount slider multiplies against it. At the default (0.5), it has no effect — the scale controls everything. Below 0.5, it suppresses greebles below the scale baseline. Above 0.5, it pushes greebles above the baseline, even at macro scale positions. At 1.0 with scale at deep macro (0.9+), a small amount of sub-detail still appears — perhaps one annotation string or a few tick marks — but it remains subtle and never fully overrides the macro simplification. The formula: `effectiveGreeble = scaleGreeble * (greebleSlider * 2.0)`, clamped to `[0, 1]`, with a floor of `greebleSlider * 0.15` ensuring that pushing the slider high always introduces *some* detail regardless of scale.

- **Text Density:** When set to "Auto," the scale fully controls text presence per the style guide's detail shedding order. When set to a specific level (Sparse/Medium/Dense), it acts as an override that fights against the scale:
  - At compatible scale positions (e.g., "Dense" at micro), the override has full effect.
  - At opposing scale positions (e.g., "Dense" at deep macro), the override is attenuated: Dense at scale 0.9 might produce Sparse-equivalent text rather than full dense text. The text that does appear uses fewer lines, larger font size, and bolder presentation — it adapts to macro aesthetics rather than forcing a micro layout into a macro context.
  - "None" is absolute — it disables all text at any scale regardless.

- **Fill Probability:** The scale-derived rendering mode (Line at micro, Solid at macro) sets a baseline. Fill Probability multiplies against it. At 0.0, force line-only even at macro. At 1.0, force solid-fill even at micro. At 0.5, the scale controls rendering mode normally. Intermediate values blend: Fill Probability 0.7 at scale 0.2 means some elements get fills that wouldn't normally have them at micro — but not all.

**General principle:** The scale is the autopilot. User controls are the manual override. They always have *some* effect when pushed, but the scale bends them toward aesthetic coherence rather than breaking.

**Feature Toggles (checkboxes):**

| Toggle | Default | Notes |
|---|---|---|
| Unicode Accents | on | Enable/disable cryptic Unicode characters as accent elements |
| Barcodes | on | Enable/disable barcode/QR code elements |
| Warning Icons | on | Enable/disable hazard/warning iconography |
| Wireframes | on | Enable/disable wireframe low-poly geometry |
| Dataviz Elements | on | Enable/disable chart/graph fragment elements |

---

### 3.11 Aesthetic Treatment

| Control | Type | Default | Options | Notes |
|---|---|---|---|---|
| Treatment | select dropdown | None | None, Glitch, Terminal, Corruption, Algorithm, Hacking | Applies the selected visual modifier (style guide Section 9) |
| Treatment Intensity | range slider | 0.5 | 0.0–1.0 | How strongly the treatment affects the output. 0 = barely visible. 1 = heavy application. Revealed only when Treatment is not "None". |

---

### 3.12 Presets

Follows the existing repos' pattern.

| Control | Type | Notes |
|---|---|---|
| Preset dropdown | select | Built-in presets + user-saved presets |
| Save Preset | button | Saves current parameter state under a user-provided name |
| Delete Preset | button (red) | Removes the selected user preset |
| Share | button | Encodes all parameters as a URL hash string or base64 string for sharing |

**Built-in Presets (examples):**
- `Cockpit Cluster` — Deep micro, multi mode, depth 4, borders on, all generators
- `Diagnostic Panel` — Micro, single mode, status panel + data readout only
- `Sigil` — Deep macro, single mode, standalone glyph only, no text
- `Specimen Grid` — Multi mode, full range, high irregularity, borders on
- `Corrupted Feed` — Mid scale, corruption treatment, intensity 0.8
- `Terminal Dump` — Deep micro, terminal treatment, terminal block + error message only
- `Warning Label` — Macro, warning icons only, borders on (chevron warning stripe)

---

## 4. SVG Export System

### 4.1 Rendering Architecture: Dual-Path Recording

All drawing operations must produce both a visual canvas render (via p5.js) AND a parallel SVG path representation. This is the primary technical constraint of the application, driven by the pen plotter output requirement: every visual element must reduce to toolpaths that a plotter can trace.

**Approach:** Implement a recording layer that intercepts or wraps p5.js draw calls and builds SVG `<path>` elements in parallel.

**Wrapper functions** for every drawing primitive used:

| p5.js Call | SVG Equivalent |
|---|---|
| `line(x1,y1,x2,y2)` | `<path d="M x1 y1 L x2 y2" />` |
| `rect(x,y,w,h)` | `<path d="M x y L x+w y L x+w y+h L x y+h Z" />` |
| `ellipse(x,y,w,h)` / `circle(x,y,d)` | `<path>` using cubic Bezier arc approximation (4 curves for full ellipse) |
| `arc(x,y,w,h,start,stop)` | `<path>` using `A` (arc) commands |
| `beginShape()`...`vertex()`...`endShape()` | `<path d="M ... L ... L ... Z" />` |
| `bezier()` / `bezierVertex()` | `<path d="M ... C ..." />` |
| `point(x,y)` | `<circle cx="x" cy="y" r="strokeWeight/2" />` (or tiny path) |
| `text(str,x,y)` | `<text>` element with font attributes, OR convert to `<path>` via font outline extraction |

**SVG Document Structure:**
```xml
<svg xmlns="http://www.w3.org/2000/svg"
     width="{outputWidth}" height="{outputHeight}"
     viewBox="0 0 {outputWidth} {outputHeight}">
  <rect width="100%" height="100%" fill="{backgroundColor}" />
  <g id="composition" stroke="{foregroundColor}" fill="none" stroke-width="{baseStrokeWeight}">
    <!-- In Multi mode, each cell is a <g> with transform -->
    <g id="cell-0" transform="translate(x,y)">
      <g id="border-0"><!-- border paths --></g>
      <g id="content-0"><!-- element paths --></g>
    </g>
    <!-- ... -->
  </g>
</svg>
```

### 4.2 Text Handling in SVG

All text is rendered as `<path>` elements — no `<text>` elements are ever used. This guarantees pen plotter compatibility and eliminates font dependency issues in exported SVGs.

**Micro font text** (single-line fonts):
- Glyph paths extracted via opentype.js are open paths (not closed contours)
- Rendered with `stroke="{fgColor}" fill="none"` and the current stroke weight
- Each character produces a single toolpath — minimal pen lifts, optimal for plotting

**Macro font text** (outline fonts):
- Glyph paths extracted via opentype.js are closed contours
- Rendered with `stroke="{fgColor}" fill="none"` — the pen traces the letter's outline boundary
- Inner contours (counters in letters like O, A, B) are separate stroked paths
- Stroke weight for macro font outlines matches the base stroke weight setting

**Both types:**
- Glyph path data is cached per character per font per size in a `Map` to avoid redundant extraction
- SVG output is fully self-contained — no external font references
- All text paths are grouped in `<g>` elements per text block for logical structure

### 4.3 SVG Optimization
- Combine consecutive paths with identical stroke/fill attributes into a single `<path>` with multiple subpaths where possible
- Use relative path commands (`l`, `c`, `a`) to reduce file size
- Round coordinate values to 2 decimal places
- Group elements hierarchically: `composition > cell > border | content > individual elements`

### 4.4 Pen Plotter Considerations
The SVG output is optimized for pen plotter consumption:
- **No fills.** All geometry uses `fill="none"` with `stroke` only. Pen plotters trace paths — they cannot fill regions. (The exception is the background `<rect>`, which plotters ignore.)
- **Stroke weight is informational.** The SVG includes `stroke-width` for on-screen display, but pen plotters use the physical pen width. The exported stroke weight serves as a visual preview of line density.
- **Path continuity.** Where possible, connected line segments are joined into single continuous `<path>` elements to minimize pen lifts. This improves plotting speed and reduces wear.
- **No overlapping duplicate strokes.** When geometry naturally overlaps (e.g., a crosshair line passing through a circle's center), the overlapping segments are not drawn twice. The plotter would trace them twice, wasting time and depositing excess ink.
- **Layer grouping.** The hierarchical `<g>` structure (cells > borders > content) allows plotter software to plot layers selectively — e.g., plot borders in one pass with one pen, content in another pass with a different pen.
- **Coordinate precision.** 2 decimal places (0.01px resolution) provides sufficient precision for plotters with typical step resolutions of 0.05–0.1mm.

---

## 5. Generation Pipeline

### 5.1 Pipeline Stages

```
[Parameters] → [Seed PRNG] → [Layout] → [Per-Cell Generation] → [Render + Record SVG]
```

**Stage 1: Parameter Resolution**
- Read all UI control values into a parameter state object
- Resolve "Auto" settings based on micro-macro position
- Validate constraints (at least one archetype enabled, etc.)

**Stage 2: Seed PRNG**
- Initialize a seeded PRNG (xorshift128 or similar fast deterministic RNG, matching existing repos)
- All subsequent random decisions use this PRNG, not `Math.random()`

**Stage 3: Layout**
- **Single mode**: one cell = full output area minus margins
- **Multi mode**: run recursive subdivision algorithm to produce an array of cell rectangles
  - Each cell stores: `{ x, y, w, h, scale }` where `scale` is the effective micro-macro value for that cell

**Stage 4: Per-Cell Generation**
For each cell:
1. **Filter archetypes**: exclude disabled archetypes and those outside the cell's scale range
2. **Select archetype**: weighted random selection from eligible archetypes
3. **Filter element families**: within the archetype, exclude families outside the cell's scale range
4. **Select elements**: choose specific primitives, respecting the archetype's compositional rules
5. **Apply detail shedding**: remove detail layers above the cell's scale threshold
6. **Apply rendering mode**: line, hybrid, or solid per the scale crossfade
7. **Apply aesthetic treatment**: if enabled, modify geometry per the treatment rules
8. **Generate border**: if borders enabled, select and generate a border style appropriate to the cell's scale

**Stage 5: Render + Record**
- Execute all p5.js draw calls through the wrapper layer
- Each wrapper call simultaneously appends to the SVG document model
- On completion, the SVG is ready for export

### 5.2 PRNG Requirements

- Deterministic: same seed produces identical output across runs
- Fast: xorshift128 or equivalent (matching existing repos' approach)
- All random decisions route through the PRNG: archetype selection, element placement, parameter variation, subdivision splits, border style selection
- The PRNG is the sole source of randomness in the pipeline

---

## 6. Canvas and Rendering

### 6.1 p5.js Configuration
- Renderer: `P2D` (default 2D canvas — no WebGL needed)
- Canvas size: set to the output dimensions specified in controls
- Pixel density: `pixelDensity(1)` for consistent sizing (export dimensions = pixel dimensions)
- Frame rate: not relevant (single-frame generation, not animation loop). Use `noLoop()` after generation.

### 6.1.1 Canvas Display Scaling
The canvas renders at the exact output dimensions (e.g., 4096x4096) but is displayed scaled to fit the viewport:
```css
#canvas-container canvas {
  display: block;
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
}
```
The canvas container uses `align-items: center; justify-content: center` to center the scaled canvas. This matches the existing repos.

### 6.1.2 Responsive Resize
Implement `windowResized()` to handle browser window resizing:
- The canvas container flexes with the viewport. The canvas element's CSS display size adapts via `object-fit: contain`.
- The canvas's internal resolution does NOT change on window resize — only its display size. The output dimensions are user-controlled, not viewport-controlled.
- The sidebar remains fixed at 370px regardless of window size.

### 6.2 Rendering Approach
- All drawing uses p5.js primitive calls (`line`, `arc`, `ellipse`, `rect`, `beginShape`/`vertex`/`endShape`)
- Text is rendered via extracted font glyph paths (see Section 4.2), not via p5.js `text()`
- **Pen plotter constraint on rendering modes:** The style guide defines three rendering modes (Line, Solid, Hybrid). For pen plotter compatibility, "Solid" and "Hybrid" fills are adapted:
  - **Line mode**: Stroke only, no fill. Direct pen plotter compatibility.
  - **Solid mode**: On the p5.js canvas, elements are drawn with fill for visual preview. In SVG export, fills are replaced with dense hatching or outline-only strokes. The "solid" appearance on screen is an approximation — the plotter output will show the outline or hatch pattern.
  - **Hybrid mode**: Canvas shows mixed fill+stroke. SVG exports stroke-only with selective hatching where fills would appear.
  - A future enhancement could offer user-selectable fill simulation strategies (parallel hatching, crosshatch, concentric) for solid regions in the plotter output.
- All elements use the single foreground color; the only color variation is between stroke and fill presence on the canvas preview
- `strokeCap(SQUARE)` or `strokeCap(PROJECT)` for the technical/precise aesthetic (not rounded caps)
- `strokeJoin(MITER)` for sharp corners on Manhattan paths and bracket elements

### 6.3 Coordinate System
- Origin (0,0) is top-left of the output
- All element generation functions receive a bounding rectangle `{ x, y, w, h }` defining their available space
- Elements are responsible for fitting themselves within their bounding rectangle, respecting internal padding
- In Multi mode, each cell's bounding rectangle is its subdivision rect minus cell padding

---

## 7. State Management

### 7.1 Global State Object
A single global state object holds all parameter values. Structure:

```javascript
const state = {
  // Output
  width: 800,
  height: 800,
  margin: { top: 20, right: 20, bottom: 20, left: 20, linked: true },
  bgColor: '#0a0612',
  fgColor: '#d0d8e8',

  // Primary control
  scale: 0.0,  // Default: full micro for initial load generation

  // Composition
  mode: 'single',  // 'single' | 'multi'
  multi: {
    depth: 3,
    irregularity: 0.5,
    minCellSize: 60,
    cellPadding: 4,
    cellGap: 2,
    scaleBias: 0.0
  },

  // Borders
  bordersEnabled: false,
  border: {
    style: 'auto',
    weight: 1.0,
    padding: 6
  },

  // Seed
  seed: '7f2a9b03',
  lockSeed: false,

  // Generator toggles + probability weights (archetypes)
  archetypes: {
    statusPanel:      { enabled: true, weight: 0.5 },
    targetingReticle: { enabled: true, weight: 0.5 },
    meterGauge:       { enabled: true, weight: 0.5 },
    dataReadout:      { enabled: true, weight: 0.5 },
    nodeNetwork:      { enabled: true, weight: 0.5 },
    standaloneGlyph:  { enabled: true, weight: 0.5 },
    globeSphere:      { enabled: true, weight: 0.5 },
    sealBadge:        { enabled: true, weight: 0.5 },
    errorMessage:     { enabled: true, weight: 0.5 },
    terminalBlock:    { enabled: true, weight: 0.5 },
    encodedData:      { enabled: true, weight: 0.5 },
    datavizFragment:  { enabled: true, weight: 0.5 }
  },

  // Geometry
  geometry: {
    symmetryBias: 'any',
    maxRadialArms: 12,
    organicCharacter: 0.3,
    strokeWeight: 1.5,
    fillProbability: 0.0,
    fontBias: 'auto',
    softEdgeFiltering: true
  },

  // Detail
  detail: {
    textDensity: 'auto',
    concentricRingLimit: 4,
    tickMarkDensity: 0.5,
    greebleAmount: 0.5
  },

  // Feature toggles
  features: {
    unicodeAccents: true,
    barcodes: true,
    warningIcons: true,
    wireframes: true,
    datavizElements: true
  },

  // Treatment
  treatment: 'none',
  treatmentIntensity: 0.5,

  // Export
  filenamePrefix: 'micrographic',

  // Fonts (populated at runtime from directory scan)
  fonts: {
    microFonts: [],   // loaded from /fonts/micro/ — single-line fonts
    macroFonts: [],   // loaded from /fonts/macro/ — outline display fonts
    fontBias: 'auto'  // 'auto' or name of a specific loaded font
  }
};
```

### 7.2 UI Synchronization
- Each UI control binds to its corresponding state property via `oninput` or `onchange` handlers
- Panel changes redraw the current composition in place (same seed, same cell edits, the animation left on its frame); slider drags coalesce
- Exceptions that wait for the next generation (Generate, Randomize, a new seed, a loaded preset): the Generator Types section, whose settings the current composition keeps a snapshot of, and the Seed field. Export and Presets controls never draw
- Treatments, stacking and borders draw from their own streams, so switching a treatment on and off returns the same page
- Controls that depend on other controls (e.g., Multi Mode params hidden when Single is selected, Treatment Intensity hidden when Treatment is "None") use conditional visibility toggled via CSS class or `style.display`

### 7.3 Preset Serialization
- State is serialized to/from a JSON string
- For URL sharing: JSON is compressed and base64-encoded into a URL hash fragment
- Built-in presets are defined as static JSON objects in the source
- User presets are stored in `localStorage` under a namespaced key (e.g., `micrographics_presets`)

---

## 8. File Organization (within index.html)

The single HTML file is organized into these major JavaScript sections, in order:

```
// ========================================================
// CONSTANTS & CONFIG
// ========================================================
// Style guide numeric constants, scale thresholds, viable ranges,
// archetype viable ranges, detail shedding thresholds

// ========================================================
// PRNG
// ========================================================
// Seeded xorshift128 implementation

// ========================================================
// FONT LOADER
// ========================================================
// Scan /fonts/micro/ and /fonts/macro/ directories
// Load all .ttf/.otf/.woff files via opentype.js
// Classify as micro (single-line) or macro (outline) based on directory
// Glyph path cache per character per font per size
// Populate font selection dropdown dynamically

// ========================================================
// SVG RECORDER
// ========================================================
// SVG document model, path builder, wrapper functions for all p5.js draw calls

// ========================================================
// TEXT GENERATION ENGINE
// ========================================================
// Word lists, format templates, number generators per text role
// textGen.statusLabel(), textGen.valueReadout(), etc.
// Accent character pool with weighted selection

// ========================================================
// GLOBAL STATE
// ========================================================
// State object, UI sync, preset management, keyboard shortcuts

// ========================================================
// SUBDIVISION
// ========================================================
// Recursive subdivision algorithm for Multi mode
// Cell scale mapping formula, slider-to-subdivision modulation

// ========================================================
// GEOMETRY PRIMITIVES
// ========================================================
// Circle, star, arc, line, dot, polygon drawing functions
// Each function draws to canvas AND records to SVG
// Symmetry enforcement: rotational (stamp-and-rotate), bilateral (mirror)

// ========================================================
// ELEMENT GENERATORS
// ========================================================
// One function per geometry family (4.1–4.12 in style guide)
// Each takes a bounding rect + scale + PRNG and returns drawn geometry

// ========================================================
// ARCHETYPE COMPOSERS
// ========================================================
// One function per archetype (6.1–6.12 in style guide)
// Each assembles element generators into a composition
// Layout algorithms per Section 12 of this document

// ========================================================
// BORDER GENERATOR
// ========================================================
// Border style selection and rendering per style guide Section 8

// ========================================================
// TREATMENTS (SVG-COMPATIBLE)
// ========================================================
// Glitch, terminal, corruption, algorithm, hacking
// All techniques produce SVG-compatible path output (see Section 14)

// ========================================================
// GENERATION PIPELINE
// ========================================================
// Main generate() function orchestrating the full pipeline
// Parameter resolution, scale interaction rules, error handling

// ========================================================
// ERROR DISPLAY
// ========================================================
// Terminal-aesthetic error state rendering (see Section 15)

// ========================================================
// EXPORT
// ========================================================
// SVG serialization, PNG export, clipboard copy

// ========================================================
// P5.JS SETUP & DRAW
// ========================================================
// setup(), draw(), windowResized()
// Initial load: auto-generate at scale=0.0, single mode, randomized params

// ========================================================
// UI EVENT HANDLERS
// ========================================================
// Window-exposed functions for HTML onclick/oninput handlers
```

---

## 9. Performance Considerations

- **No animation loop.** The app generates a single static frame per Generate press. Use `noLoop()` after rendering.
- **PRNG over Math.random().** All randomness is seeded and deterministic.
- **SVG recording is write-only during generation.** The SVG DOM is built as a flat array of path strings, not as a live DOM tree. Serialization to XML happens only on export.
- **Multi mode cell count.** At depth 6, cell counts can reach 100+. Each cell runs the full generation pipeline. If performance is a concern, generation can be batched with `requestAnimationFrame` to avoid blocking the UI, with a progress indicator on the Generate button.
- **Font loading.** All fonts from both `/fonts/micro/` and `/fonts/macro/` directories are loaded via opentype.js at startup. Glyph path data is cached per character per font per size in a `Map` to avoid redundant extraction. Font loading must complete before the initial auto-generation runs — use a loading state / promise gate. If macro font directory is empty, this is not an error — the generator falls back to micro fonts at all scales.
- **Treatment overhead.** SVG-compatible treatments (especially glitch slice displacement and scan line overlay) can increase SVG element count significantly. Cap scan lines at ~100 per composition. Cap glitch slices at ~15 bands.

---

## 10. Interaction Model

- **Controls redraw in place.** Changing a control redraws the current composition at once: same seed, same cell edits. Treatments switch on and off on the page in front of you.
- **Generator types wait for the next generation.** The Generator Types section (and the seed) take effect when Generate or Randomize makes the next composition, so exploring them never throws away the current one.
- **Randomize** updates parameters AND generates in one action.
- **Seed provides reproducibility.** Users can lock a seed, adjust one parameter, and regenerate to see the isolated effect of that change.
- **Export is always available.** After any generation, SVG and PNG export buttons immediately produce output from the current canvas state and recorded SVG data.
- **Canvas shows the latest generation**, redrawn as its controls change.
- **Initial load behavior.** On first load, the app auto-generates immediately with: scale set to 0.0 (full micro), single mode, and all other parameters randomized (seed, archetype weights, geometry params, treatment). This gives the user an immediate, visually dense result that showcases the aesthetic. The randomized parameter values are reflected in the UI controls so the user can see what was selected.

---

## 11. Text Content Generation Engine

The text generation engine produces the strings, numbers, codes, and labels that populate HUD elements. This system will be built incrementally during development. The requirements below define the contract the engine must satisfy.

### 11.1 Text Role Definitions and Templates

Each text role has a format specification. The engine exposes a function per role that returns a generated string using the seeded PRNG.

| Role | Format Template | Examples |
|---|---|---|
| Status label | Pick from word list, ALL CAPS | `ACTIVE`, `OFFLINE`, `CALIBRATED`, `STANDBY`, `OVERRIDE`, `SYNC`, `PROCESSING`, `IDLE`, `LOCKED`, `NOMINAL`, `WARNING`, `CRITICAL`, `DEGRADED`, `INITIALIZING`, `TERMINATED`, `SCANNING`, `VERIFIED`, `PENDING`, `REBOOTING`, `DORMANT` |
| Value readout | `{1-3 digit number}{unit}` | `47%`, `12V`, `38°C`, `1.8A`, `0.3%`, `99.7%`, `240V`, `-12°C` |
| Identifier / code | `{2-4 uppercase letters}{separator}{3-5 digits or hex}` | `EOH-9520`, `SYS.0044`, `NODE-7F2A`, `ARX:03B1`, `PROC_881`, `ID:292C` |
| Version string | `V{1-2}.{1-2}.{2}` | `V6.0.21`, `V1.3.08`, `V12.1.44`, `V.4.8.31` |
| Timestamp | `{HH}:{MM}:{SS}` or `{HH}:{MM}` | `12:04:15`, `08:33`, `23:59:01` |
| Coordinate | `{2-3 digits}.{2-4 digits}` separated by `/` or `,` | `82.2399`, `119.09 / 302.41`, `41.04, 28.97` |
| System path / filename | `{NAME}_{SUFFIX}.{EXT}` | `LOG_ARX.TXT`, `DATA.843.XML`, `SYS_CORE.DAT`, `ERR_DUMP.LOG`, `NET_07.CFG` |
| Hex dump fragment | `{8-16 hex chars in pairs}` | `4F 2A 7B 00`, `FF D1 3C 88 A0 12`, `0x7F2A9B03` |
| Year | 4-digit year from narrow range | `2026`, `2031`, `2084`, `2099` |
| Error code | `{ERR_TYPE}_{HEX}` or `{ERR_TYPE} AT 0x{HEX}` | `ERR_0x7F2A`, `SEGFAULT AT 0x0000`, `FATAL_9C03`, `OVERFLOW_1A2B` |
| Annotation text | Pick from micro-text phrase list | `CHECKSUM: OK`, `LATENCY: 12MS`, `PACKET LOSS: 0.3%`, `SIGNAL: NEAR`, `PORT: 07`, `SECTOR A`, `FREQUENCY REGISTERED`, `DECIPHERING...` |
| Terminal prompt | `{prompt char} {command}` | `> SCAN`, `$ ./diag`, `# STATUS`, `_ PING 192.168.0.1` |
| Override / hack label | Pick from word list, ALL CAPS | `ROOT`, `BYPASS`, `ACCESS GRANTED`, `SUDO`, `INJECT`, `ELEVATED`, `EXPLOIT`, `TUNNELED`, `SHADOW`, `BREACH` |

### 11.2 Unit and Range Specifications

| Unit Type | Symbol | Value Range | Decimal Places |
|---|---|---|---|
| Percentage | `%` | 0–100 (or 0.0–99.9) | 0–1 |
| Voltage | `V` | 1–480 | 0–1 |
| Current | `A` | 0.1–12.0 | 1 |
| Temperature | `°C` | -40–120 | 0 |
| Frequency | `MHz` or `GHz` | 1–9999 MHz or 0.1–12.0 GHz | 0–1 |
| Latency | `MS` | 1–9999 | 0 |
| Power | `W` | 0.1–500 | 0–1 |
| Data rate | `MB/S` or `GB/S` | 0.1–100 | 1 |

### 11.3 Accent Character Pool

The Unicode accent character pool with weights for selection. Higher weight = more likely to be chosen.

| Category | Characters | Weight | Notes |
|---|---|---|---|
| APL operators | `⌬ ⌖ ⌗ ⍉ ⍋ ⍒ ⍟ ⍰ ⎔ ⎕` | 1.0 | Strong geometric structure, primary pool |
| Geometric | `◈ ◉ ◎ ◬ ⬡ ⬢` | 0.8 | Bold, reads well at all sizes |
| Missing data | `⍰ ⊘ ∅ ▯ ▮` | 0.7 | Thematically powerful |
| Ballot/box | `☒ ☐ ⊠ ⊡ ⊞` | 0.5 | More delicate, micro-suited |
| Block/braille | `⣿ ░ ▒ ▓ ▤ ▦` | 0.4 | Texture-like, pair with corruption treatment |
| Box drawing | `┼ ├ ┤ ┬ ┴` | 0.3 | Subtle, use as broken-structure accent |

### 11.4 Engine API

```javascript
// All functions take a PRNG instance and return a string
textGen.statusLabel(prng)        // → "CALIBRATED"
textGen.valueReadout(prng)       // → "38°C"
textGen.identifier(prng)         // → "NODE-7F2A"
textGen.versionString(prng)      // → "V6.0.21"
textGen.timestamp(prng)          // → "12:04:15"
textGen.coordinate(prng)         // → "82.2399 / 119.09"
textGen.systemPath(prng)         // → "LOG_ARX.TXT"
textGen.hexDump(prng, length)    // → "4F 2A 7B 00 FF D1"
textGen.year(prng)               // → "2084"
textGen.errorCode(prng)          // → "ERR_0x7F2A"
textGen.annotation(prng)         // → "LATENCY: 12MS"
textGen.terminalPrompt(prng)     // → "> SCAN"
textGen.overrideLabel(prng)      // → "BYPASS"
textGen.accentChar(prng)         // → "⍟"

// Composite: returns a full text block for an archetype
textGen.statusPanelText(prng, density)  // → { label, value, status, annotations[] }
textGen.terminalBlock(prng, lines)      // → string[] of terminal output lines
textGen.errorMessageBlock(prng)         // → { header, code, stackLines[] }
```

### 11.5 Development Notes

- Word lists should be extensible — structured as arrays that can be added to over time
- Number generation should use the PRNG, not Math.random()
- The engine should be stateless beyond the PRNG — no internal counters or memory
- Duplicate detection within a single composition is desirable (don't generate the same status label twice in one panel) — pass a "used" set if needed
- The engine will be one of the first systems built, as most archetypes depend on it

---

## 12. Archetype Layout Specifications

Each archetype composer function receives a bounding rectangle `{ x, y, w, h }` and must position all sub-elements within it. These specifications define the layout algorithms. All padding and spacing values below are expressed as fractions of the bounding box dimensions, making them resolution-independent.

### 12.1 Layout Constants (User-Adjustable)

These are exposed indirectly through existing controls (Cell Padding, Border Padding, Margin) and scale-derived values. Internal layout constants use proportional spacing:

| Constant | Value | Notes |
|---|---|---|
| Inner padding (element to content) | 5–10% of min(w,h) | Space between an element's conceptual boundary and its internal content |
| Text line height | 1.4x font size | Spacing between text lines |
| Text margin | 3–5% of w | Left/right offset for text within a panel |
| Label-to-value gap | 2–4% of h | Vertical gap between a status label and its value readout |
| Bracket arm length | 8–15% of min(w,h) | Length of L-bracket arms on corner brackets |
| Bracket inset | 2–4% of min(w,h) | How far brackets are inset from the bounding edge |

### 12.2 Per-Archetype Layouts

#### Status Panel (6.1)
```
┌─────────────────────────────┐
│  [bracket]       [bracket]  │  ← brackets at corners of inner rect
│    STATUS_LABEL             │  ← top-left, large font
│    value_readout   ⍟        │  ← below label; accent glyph right-aligned
│    annotation_1             │  ← small text, left-aligned
│    annotation_2             │  ← small text, left-aligned
│  [bracket]       [bracket]  │
└─────────────────────────────┘
```
- Corner brackets define an inner rectangle inset from the bounding box
- Status label: top-left of inner rect, font size = 8–12% of bounding height
- Value readout: below label, smaller font
- Accent glyph: right-center of inner rect, sized 15–25% of inner height
- Annotations: bottom-left, tiny font, stacked

#### Targeting Reticle (6.2)
```
         ┌──┐
    ┌────┤  ├────┐
    │    └──┘    │
  ──┤     ·     ├──    ← crosshair lines extend to inner rect edges
    │    ┌──┐    │       center dot or gap
    └────┤  ├────┘
         └──┘
     CODE: 7F2A          ← annotation below, centered
```
- Primary circle: centered, diameter = 70–85% of min(w,h)
- Crosshair: perpendicular lines through center, with optional gap at center (gap = 10–15% of diameter)
- Tick marks: at 0°, 90°, 180°, 270° on the outer ring, protruding outward 5–8% of diameter
- Concentric rings (if any): equally spaced inside the primary circle
- Annotation text: centered below the circle, small font

#### Meter / Gauge (6.3)
```
        ╭────────╮
       ╱          ╲
      ╱    ╱│      ╲     ← pointer arm from center
     │    ╱ │       │
      ╲    │      ╱
       ╲          ╱
        ╰────────╯
          47%             ← value below arc
```
- Arc: centered, spanning 180–270° of a circle, diameter = 75–90% of min(w,h)
- Pointer: single line from center to arc edge at a random angle within the arc span
- Value readout: centered below the arc, medium font
- Tick marks: along the arc at regular angular intervals

#### Data Readout Block (6.4)
```
┌─────────────────────────────┐
│  ── DATA_BLOCK ──────────── │  ← header line with rule
│  KEY: VALUE                 │  ← key-value pairs, monospace
│  KEY: VALUE                 │
│  KEY: VALUE                 │
│  ░░░░░░░░░░░░░░░░░░░░░░░░░ │  ← optional: mini bar chart or waveform
│  V6.0.21          12:04:15  │  ← footer: version left, timestamp right
└─────────────────────────────┘
```
- Full border or corner brackets
- Header: centered or left-aligned with horizontal rule extending to right edge
- Body: left-aligned key-value pairs, monospace, one per line
- Footer: version string bottom-left, timestamp bottom-right
- All text left-padded from the inner rect edge

#### Standalone Glyph (6.6)
```
┌─────────────────────────────┐
│                             │
│                             │
│          ✱                  │  ← single shape, centered
│                             │
│                             │
└─────────────────────────────┘
```
- One primary shape centered in the bounding box
- Shape diameter/size = 50–80% of min(w,h), scaling with macro position (larger at deeper macro)
- No text, no framing, no annotation (unless greeble override pushes some in)
- The shape IS the entire composition

#### Other archetypes follow analogous patterns. Node/Network radiates from center. Globe/Sphere centers the sphere with optional bracket framing. Seal/Badge centers the circular form. Error Message and Terminal Block follow the Data Readout layout but with role-specific content.

#### The Facility: six panels of one site

Six archetypes describe one technomancer research facility: Containment Ward, Site Schematic, Event Chamber, Exo-Atmosphere, Core Lattice and Augment Diagnostic. They share a kit (the `fac*` helpers), and they quote the same place.

**One site per page.** `buildFacility(seed)` deals the facility from the page seed using hashes only, so it makes no PRNG draws and moves no other cell. It sets:
- the site name and sublevel;
- the vault and its anomaly;
- the incident;
- the alert level (green, amber, red or black);
- the shift and the clock.

The first panel of each kind on a page takes the facility's own vault and anomaly (`facUnit`); later panels of that kind are neighbouring units.

**Pens.** Alarm marks draw in the accent pen (`kitAccent`, pen 2). The ward draws in the detail pen (`facWard`, pen 3): inscriptions, lattices, conduits, seals and the thaumic marks. The ward script is an original single-stroke alphabet of 24 glyphs on a 3×3 node grid, dealt per facility, so every panel on a page writes the same script.

**Common layout.** Each panel has:
- a header: title, a tag (in the accent pen when alarmed) and the designation;
- the display;
- a panel beside it in wide cells, or under it in tall cells.

`facStack` fits the panel's modules into a column or a row. It drops modules that no longer fit, and a row shares out any width left over. Most panels also add a bottom strip of traces (`facTraces`).

Panel modules include:
- tables (`_radTable`, `facColumns`);
- bars (`facBars`);
- lines of text (`facLines`);
- `facTrend`: a reading, its recent trace with the ceiling ruled across it (the zone above hatched in the accent pen), and a linear scale with a pointer.

The wave deliberately uses no dial gauges.

**Plotter rules.** The displays are stroke-only, so:
- door gaps are split walls, not background cuts;
- hidden lines are clipped geometrically (`facClipConvex`, `facPolylineHidden`);
- contours come from `facContours`, which returns the lines it draws so they can be labelled.

All six pass the pen-clash sweep at 2–8% fused ink, except Augment's parallel-coordinates plot (about 9%), where crossing lines are inherent to the form.

| Archetype | Range | Variants | Notes |
|---|---|---|---|
| Containment Ward | 0.00–0.85 | lattice, breach, fieldMap, section | The cage around the anomaly: degree scale, sector integrity, glyph band, emitter rings, field lines. Panel: dossier, protocol, emitter loads, entropy trend. Strip: the harmonic beat. Strips are drawn as friezes. |
| Site Schematic | 0.00–0.70 | plan, wardMap, evacuation, exploded, compound | The facility drawn as its architects and wardens see it. A floor is one of four plans, picked by hash: **cross** (two corridors crossing), **spine** (one long corridor, the vault closing its far end), **ring** (a loop corridor round the vault's core) and **hub** (a round building: the vault's rotunda, a ring corridor, spokes; only where the floor is squarish). Walls are double, with real door gaps. **plan:** rooms fitted out, cameras, people, ducts, lockdown, breach front, blast doors. **wardMap:** fine walls under the great ward drawn over the whole floor, with ley lines, conduits, door seals and corridor script. **evacuation:** rooms hatched by the exit they empty to, routes, exit signs, you-are-here. **exploded:** three sublevels with hidden lines removed; it only falls back to the plan when the stack would be unreadable, and never when it was chosen. **compound:** the surface, with terrain contours, fence and watchtowers, road and gate, buildings, pad, mast, tanks, and the head house over the shaft with its ward and the alert cordon. Each look has its own legend, module and access log. |
| Event Chamber | 0.05–0.85 | transverse, longitudinal, lego, bubble | A detector event display. **transverse:** end-on, with helices, hits, calorimeter deposits, muons, jets and missing ET. **longitudinal:** r–z, with eta lines. **lego:** eta–phi towers with hidden lines. **bubble:** a chamber photograph with vertex star, pair spirals, V0 and delta rays. In some events one track is unphysical: a trefoil knot that writes the script (ward pen). Panel: event table, hardest tracks, mass spectrum, missing-ET trend. Strip: trigger rates. Wide cells take the side-on view. |
| Exo-Atmosphere | 0.00–0.80 | surface, polar, storm, bands | Alien weather. **surface:** synoptic map with ruled land, coast and water lines, isobars with labels, H/L, cold, warm and thaumic fronts (the last in the ward pen), geostrophic wind barbs, the facility with its disturbance. **polar:** stereographic with aurora (ward pen). **storm:** cyclone with eye seal, rain bands, lightning, track and cone. **bands:** the gas giant the world orbits, with zonal belts, vortices, rings and terminator. Panel: legend, station, skew-T sounding or zonal winds, 72 h outlook. Strip: meteogram. Strips become the surface chart. |
| Core Lattice | 0.05–0.85 | hexCore, cutaway, rodBank, torus | The power core. **hexCore:** hex channel map with rods by insertion, sigil cores, flux contours and the hot channel. **cutaway:** containment, vessel, core and its glyph lattice, rods and drives, steam generator and pump loop with flow. **rodBank:** rod position panel with ward rods and a stuck rod. **torus:** ring core with field coils, ward seals, ports and injectors. Panel: core table, reactivity trend (or poloidal section), coolant loops, trip status. Strip: flux, period, lattice harmonic. |
| Augment Diagnostic | 0.00–0.75 | coupling, parallel, phase, rings | Implant telemetry as abstract data, never anatomy. **coupling:** triangular channel-coupling matrix. **parallel:** parallel coordinates with non-crossing history echoes and a sync brush. **phase:** phase-portrait small multiples (limit cycle holding, Rössler attractor rejecting, still ring bound). **rings:** each implant's waveform on its own ring, out-of-tolerance stretches hatched. One implant may be rejecting (accent pen); the sigil port is in the ward pen. Panel: subject, implant sync, rejection trend. Strip: neural, cardiac and ward carrier. Tall cells take the phase grid. |

Text in these panels avoids glyphs the plot fonts lack. `·` was added to the fallback glyph set, so it draws in every face.

#### Site lore in the other archetypes

The other eighteen archetypes quote the same site in passing, so a page reads as one place even when no facility panel is on it.

**How a word is swapped.** About 30% of generated words (`LORE_RATE`) are swapped for the site's own. A swap is decided by hashing the draw the word already made (`lorePick`), or a seed the composer already holds (`loreChance`, `loreLine`); it never makes a new draw. So a page keeps its layout and values, and only some of its words change. Templates are filled from the page's facility (`loreFill`): site, sublevel, vault, anomaly, alert, incident, shift, clock, run, protocol name, giant and ward drift.

**The alert sets the mood.** Status words, notes and hero words come from pools chosen by the page's alert level:
- green: WARDED, SEALED, CONTAINED;
- amber adds DRIFTING and VENTING;
- red and black add BREACH, LOCKDOWN and UNBOUND.

**The giant.** The facility deals one giant per page (`f.giant`). The primary Exo-Atmosphere panel, the orrery and the status notes all name it.

| Archetype | What quotes the site |
|---|---|
| Shared text (every archetype) | Status words; identifier prefixes (WRD, VLT, SGL…); notes (SITE, VAULT, PRIMARY…); commands (SEAL, SCRY…); error types; paths; override words |
| Status Panel | The edge plate names the site |
| Targeting Reticle | Some reticles track the site's anomaly |
| Meter / Gauge | Ward, ley-flux, resonance, cryo and bleed scales; a ward chart's axes (LEY, WRD, SGL, THM, BLD) |
| Data Readout | Ward, ley, bleed, thaum, field, cryo and sigil channels; sections named for the site's systems |
| Error Message | Ward faults, containment faults and the page's incident as headlines; ward modules and functions in the backtrace |
| Terminal Block | The wardens' shell: ward, seal, bind and scry commands; ward hosts and users; lattice boot lines |
| Hero Text | WARD, SEAL, VAULT and the anomaly's designation (BREACH on alarmed pages); site notices as captions |
| Seal / Badge | The site's offices on the rim, and its vault, sublevel or anomaly underneath. Some seals carry the lower legend in the ward script (detail pen). |
| Standalone Glyph, Sigil | A ring of the ward script inside the bearing ring, where the gap is wide enough to read (detail pen). A sigil wears it in place of its bounding ring. |
| Node / Network | Some networks are the site's own mesh: a vault core, ley nodes, lattice switches, ward sensors and sigil ports |
| Globe | The site joins the ground-station network |
| Star Chart | The orrery's moon-bearing planet is the site's giant; rite windows; the finder marks the galaxy the anomaly came from |
| ASCII Art | Figures filed under the site's names (VAULT.CUBE, SUBLEVEL.MAZE, GIANT.RISE) |
| Dataviz, Encoded Data, Spectrogram | Lore titles and states. On a signal-bleed page the spectrogram usually names the bleed. |
| Mini-Game | The cabinet stands on the rec deck of the page's sublevel |

### 12.3 Aspect Ratio Handling

When a cell has a non-square aspect ratio, archetypes adapt:

**Radially symmetric elements** (Reticle, Seal, Globe, Standalone Glyph):
- The element is sized to fit the **short dimension** and centered along the long dimension
- Resulting negative space along the long axis is filled with: annotation text (at micro), or left empty (at macro)
- Maximum supported aspect ratio: 3:1 or 1:3. Beyond this, these archetypes are excluded from the eligible pool for that cell.

**Panel/text-heavy elements** (Status Panel, Data Readout, Error Message, Terminal Block):
- These naturally adapt to wide or tall rectangles
- In wide cells: text lines extend horizontally, layout stays left-aligned with more horizontal rule length
- In tall cells: more text lines can be stacked vertically
- Minimum supported aspect ratio: 1:4 or 4:1.

**Linear elements** (Encoded Data, Dataviz Fragment, Node/Network):
- These benefit from elongation — a barcode is naturally wide, a waveform is naturally wide
- In tall cells: orient vertically (e.g., vertical barcode, vertical waveform)
- These have the widest aspect ratio tolerance.

**Filtering rule:** When selecting an archetype for a cell, exclude archetypes whose aspect ratio tolerance is exceeded by the cell's actual ratio.

---

## 13. Symmetry Enforcement

### 13.1 Rotational Symmetry Generation

For elements requiring N-fold rotational symmetry (stars, seals, gears, pinwheels, etc.):

1. Define an invisible regular polygon with N vertices (N = random 3–9, or as constrained by Max Radial Arms), centered at the element's center point.
2. Generate the primitive shape/detail at the first vertex position and angular orientation.
3. Increment rotation by `360° / N` and stamp the same primitive at each subsequent vertex position.
4. The center of the polygon may optionally contain a separate element (a dot, a small circle, a crosshair, or nothing).

This "stamp and rotate" approach guarantees perfect rotational symmetry and simplifies SVG output — each arm is an identical `<path>` element within a `<g>` that applies a rotation transform.

**Polygon vertex count:**
- N is chosen per element from the range [3, min(9, maxRadialArms)]
- Certain archetypes have preferred N values: gears prefer even N (4, 6, 8), stars accept any, pinwheels prefer odd N (3, 5, 7) for visual interest

### 13.2 Bilateral Symmetry Generation

For elements requiring mirror symmetry (hourglass, bowtie, lens, panel layouts):

1. Generate all shapes on one side of the axis (left half for vertical axis, top half for horizontal axis).
2. Mirror all geometry across the axis by negating the appropriate coordinate.
3. The axis itself may contain shared elements (a line, a dot, a connector at the midpoint).

### 13.3 Linear Repetition

For repeated elements along a line (bead chains, barcode bars, dot rows, stacked bars):

1. Define a start point, end point, and count N.
2. Distribute N instances evenly along the line (or with parametric spacing variation).
3. Each instance may be identical or may vary one parameter (size, rotation) as a function of its position.

Linear symmetry can be applied as a post-process to any generated primitive — generate one element, then repeat it along a horizontal, vertical, or diagonal axis with defined spacing.

---

## 14. Aesthetic Treatments

Treatments work on the composition itself. Each one is an edit to a cell's own drawing, its SVG record, made after label knockout and before pens are handed out (`treatCell`). It cuts, moves and rewrites what the composer drew. What it moves or cuts keeps its own pens; only what it adds (copies, cipher, its own marks) goes in a `treatment-*` group, and so in the treatments' pen. On a two-pen plot a treatment reads as a second ink running through the drawing. Cell borders are never edited.

**Rules shared by all five:**
- They run in stack order, each on the drawing the one before left.
- They take every decision from hashes of the page seed and the cell (and the frame, when animating). They draw from no stream, so switching one off returns the page exactly as it was, and a cell's composition is the same on every frame.
- New text is set in the page's small face (`_drawBuiltinText`) at the floors that hold for all text. Decaying letters use the face the label was set in, at its true letter positions.
- Intensity (0–100%) sets how much of the drawing each one takes.
- Glitch and hacking leave some cells alone, more of them at low intensity. One cell of every page, also picked by hash, always takes each of them, so switching one on never leaves a page (a single-cell page included) as it was.

### 14.1 Glitch

Each cell draws two to five moves from a vocabulary of nine. On a moving page the moves are dealt again every few frames, and a cell sits some bursts out.

| Move | What it does to the drawing |
|---|---|
| Tear | A band slides sideways. |
| Tracking error | A stretch in thin slices, each nudged; the odd one far. |
| Stutter | A band repeated down the page, each repeat nudged. |
| Pixel sort | Where the drawing crosses one row, it drips down the band below, breaking into dashes and then dots. It acts only across the stretch the drawing crosses, and a sparse row drips only a short way. |
| Datamosh | Blocks lifted and dropped elsewhere; the hole keeps its corners. |
| Wrap | A band rolls off one edge and comes back on the other. |
| Comb | Alternate thin slices pulled apart. |
| Stretch | A band smeared wide from a point. |
| Misregistration | Outlines and type printed again a few pens off, diagonally, in the treatments' pen. Like a print off register, the copy shows only where it falls on bare paper. |

Small ticks at the cell's edges mark the tears.

### 14.2 Terminal

The drawing is interlaced: the scanlines are thin gaps cut through the graphics, so the treatment takes ink away rather than adding it. Type and borders stay whole. Above 40% a wider bar rolls through. On a moving page the scan rolls down two pitches a loop and the bar once.

### 14.3 Corruption

Letters decay where they stand, spreading from one point on the page (across the loop, when animating):
- The source glyph breaks: a strip of it is cut away.
- Its clones run up and down in the page's face, decomposing into slashes, ticks and dots, until a rule of the layout stops them; there they pool along the rule. A clone that would sit on its own letter is dropped, and a short run between rules gets fewer clones.
- A broken spine joins each run.
- Some words are redacted in place: a solid bar, fused on purpose.
- The layout's own rules lose segments, the gaps dashed.
- Missing-data icons and ghost columns keep to the empty space. Ghost columns go looking for it, so a cell with no type still shows the decay.

### 14.4 Decode

Ahead of the beam the drawing exists only as its encoding: one cipher glyph wherever a cell of a character grid held ink, so bars become columns of characters and rings become rings of glyphs. The cell's words are cipher there too, in the same face and size. The beam is a double rule with a tick ladder and a `DEC` readout. The letter at the beam locks in brackets, and words just decoded are underlined. A still page is frozen mid-decode; on a moving one the beam crosses each cell once a loop and the cipher cycles.

### 14.5 Hacking

The intrusion takes the cell's own elements:
- Labels are bracketed, addressed and wired to a bus down the cell's edge. Some are overwritten with the intruder's words (a cursor blinks after them), some X'd out, some get a hex dump beside them.
- The drawing's biggest clusters are selected like objects in an editor: dashed box, handles, crosshair, motion trail, predicted path, `PID`/`MAP` tags.
- A patch of the drawing is overwritten with a framed hex dump (`MEM`).
- The hijacked elements are linked edge to edge in computer-vision style: held links solid and ticked, probes dashed, both ringed at the ends and broken around type. Tags read as an intrusion (`SHELL :8080`, `PROBE :1337`), never as a tracker.
- A double-framed stamp (`ACCESS GRANTED`, `ROOT SHELL`) and a tally go in a free corner.
- Across the page (`treatPage`), a spanning tree links the nearest hijacked elements of neighbouring cells, plus a long probe or two (`PIVOT 10.0.8.216`, `EXFIL VAULT 3-C`), clear of type, tags, dumps and stamps.

On a moving page the intrusion spreads across the loop.

The style guide's algorithm treatment (visible iteration, parametric variation) was never built as a treatment.

---

## 15. Error States and Edge Cases

### 15.1 Error Display

When the generator cannot produce valid output, the canvas displays an error state in the terminal/hacker aesthetic:

```
┌──────────────────────────────────────┐
│                                      │
│         ╲                 ╱          │
│           ╲             ╱            │
│             ╲         ╱              │
│               ╲     ╱                │
│                 ╳                    │
│               ╱     ╲                │
│             ╱         ╲              │
│           ╱             ╲            │
│         ╱                 ╲          │
│                                      │
│  > ERR_GENERATION_FAILED             │
│  > {specific error message}          │
│  > SEED: {current seed}             │
│  > ████████████████░░░░░░░░ ABORTED  │
│                                      │
└──────────────────────────────────────┘
```

- Large X spanning the center of the canvas (60% of min dimension)
- Full rectangle border
- Error message text below the X in monospace, left-aligned with terminal prompt characters (`>`)
- Progress bar rendered as filled/empty block characters
- Styled in the foreground color on the background color, matching the current color settings
- The error state is rendered through the same SVG recording pipeline — even errors are exportable

### 15.2 Specific Error Conditions

| Condition | Behavior |
|---|---|
| All archetypes disabled | Display error: `NO GENERATORS ENABLED // ENABLE AT LEAST ONE ARCHETYPE` |
| All eligible archetypes filtered out by scale | Display error: `NO VIABLE ARCHETYPES AT SCALE {value} // ADJUST SCALE OR ENABLE MORE TYPES` |
| Output dimensions too small for any element | Display error: `CANVAS TOO SMALL // MINIMUM 64x64 REQUIRED` |
| Multi mode: Min Cell Size > output area | Display error: `MIN CELL SIZE EXCEEDS CANVAS // REDUCE MIN CELL SIZE OR INCREASE DIMENSIONS` |
| Font loading failure | Display error with fallback rendering using basic line segments for text approximation: `FONT LOAD FAILED // {font name} // USING FALLBACK RENDERER` |

### 15.3 Minimum Viable Output

At scale=1.0 (deep macro) with only Standalone Glyph enabled, the generator must still produce varied output. The minimum viable glyph set includes:

- A single solid circle (disc)
- A single 4-point star (sharp or equal)
- A single 8-point starburst
- A single radiation trefoil
- A single biohazard symbol
- A single petal star (4–6 petals)
- A single pinwheel (3–5 arms)
- A single organic ring (flame/leaf wreath)
- A single hourglass / bowtie
- A single serrated star / seal silhouette

These are the irreducible atomic outputs — single shapes with no internal detail, no text, no framing. Each is a valid "deep macro" output. The generator selects among them using the PRNG and the user's geometry parameter biases (symmetry, organic character, etc.).

---

## Section 17: User Text System

The Type Engine includes two user text input fields that inject custom strings into the generated composition, allowing personalization while maintaining the algorithmic micrographic aesthetic.

### 17.1 Macro Text Input

A text field for hero-scale display text. When populated:

- The **Hero Text** archetype is force-enabled regardless of the Generator Types panel state
- The user string is used verbatim as the hero text content, bypassing the random heroSources selection (status labels, codes, hex, numbers)
- The first Hero Text cell is guaranteed to spawn (8× weight boost on first use)
- **Hero Repeat Chance** (0.0–1.0, default 0.15) controls how many additional Hero Text cells appear in multi-mode compositions:
  - At 0.0: exactly one hero text cell per composition
  - At 0.15 (default): ~15% chance of each subsequent cell being hero text
  - At 1.0: hero text in every scale-eligible cell, overriding diversity enforcement
- When the macro text field is empty, hero text reverts to random source generation

### 17.2 Micro Text Input

A text field for micro-scale annotation text. When populated, the user string is distributed across multiple micro text elements throughout the composition:

- **User Text Amount** (0.0–1.0, default 0.5) controls injection frequency. At 0.0, user text never appears. At 1.0, user text appears at every opportunity.
- **Blend with Random** (default: on):
  - When enabled: each micro text site rolls against `userTextAmount` — on success, uses user text; on failure, uses a random `textGen.*` string. This creates a natural mix of user and procedural content.
  - When disabled: user text is the sole micro text content at sites where `userTextAmount` threshold is met. No random strings appear alongside it.

Micro text injection sites include:
- Banner stripe micro text lines
- Hero text sub-labels
- Archetype composer annotations, identifiers, and version strings
- Status panel labels and values
- Data readout key-value pairs
- Targeting reticle annotations
- Terminal block content lines
- Star chart labels
- Node network labels

### 17.3 Design Intent

User text should feel integrated into the composition, not pasted on top. At low `userTextAmount` values, user strings appear as rare embedded labels — as if the generated system references a specific entity. At high values, the user string dominates the text layer, creating a branded or personalized output. The blend mode ensures procedural texture is maintained even when user text is present.

---

## Section 18: Plotter Compatibility System

micro-MACRO is designed for pen plotter output. All geometry renders as single-stroke SVG paths with `fill="none"`. This section documents the plotter-specific features that bridge between the internal pixel coordinate system and physical plotter workflows.

### 18.1 Unit System

The application operates internally in pixels. A unit system provides bidirectional conversion for display and export:

| Unit | Display Format | SVG Export | Conversion |
|------|---------------|------------|------------|
| px   | Integer       | Unitless (`width="800"`) | Direct (no conversion) |
| mm   | 1 decimal     | `width="210.00mm"` | `px = mm × DPI / 25.4` |
| in   | 3 decimals    | `width="8.500in"` | `px = in × DPI` |

**DPI** (dots per inch) maps between pixels and physical dimensions. Default: 96 (screen). Common plotter values: 300, 600, 1016.

**Paper presets** set width and height to standard paper dimensions at the current DPI:
- A5: 148 × 210 mm
- A4: 210 × 297 mm
- A3: 297 × 420 mm
- Letter: 215.9 × 279.4 mm

The SVG `viewBox` always uses pixel coordinates (the internal coordinate space). The `width` and `height` attributes carry physical units, telling plotter software the intended output size. This is the correct SVG approach — the viewBox defines the coordinate system, width/height define the physical mapping.

### 18.2 Minimum Text Size

A global floor for all text rendering, settable in mm, inches, or pixels. Default: 2mm.

**Rationale:** Pen plotters have a minimum feature size determined by pen tip width and mechanical resolution. Text below approximately 2mm in height produces illegible or damaged output on most plotters. The minimum text size prevents the generator from creating sub-resolution text.

**Enforcement:** Centralized clamping in all 4 text drawing functions and 2 text measurement functions. The clamp is applied before any rendering or layout calculation, ensuring that compositions never allocate space for text that would be too small to plot. Existing proportional `Math.max()` calls in individual composers remain as secondary design-intent limits.

**Interaction with DPI:** The minimum converts to pixels via the current DPI setting. At 96 DPI, 2mm ≈ 7.6px. At 300 DPI, 2mm ≈ 23.6px. This means higher-DPI compositions automatically produce larger text in pixel terms, which is correct — the same 2mm physical text requires more pixels at higher resolution.

**Pen tip floor:** The pen tip (Pen Diameter, under Hatch Fill) raises the floor: text is never smaller than `pen_diameter × 1.5 / 0.18` of em (8.3× the pen, 2.5mm for a 0.3mm pen). Plotterm's tightest counters (Typewriter's A, m, 4) are 0.18 em between stroke centres, and at 1.5× the pen's width they keep half a nib of paper between the inks. The larger of the two floors wins; the panel says when the pen is the one setting it. Whole glyphs in treatment clones keep the same floor; single-stroke marks (slashes, ticks) may go smaller. Clip-sheet text (frame labels, footer, title) is set in Plotterm Round by cap height and keeps the floor too; the label band grows with it. The Stvrpype hub's marks are lettered by Stvrpype at its own fixed sizes.

**Line clearance:** Lines meant to read apart keep their centres at least 1.5 pen widths apart (`PEN_CLEAR`): half a nib of bare paper between the inks, which survives a fibre or gel pen's bleed. Hatch tone keeps the hatch rule's 1.2 pens, and a solid bar is drawn as rows under one pen apart (`penSolidRows`) so they fuse. Spacing between 1 and 1.2 pens is never right: too close to read as two lines, too open to read as one. Generators derive their gaps from the pen: graticules stop where they converge, tiles and meter segments give way by half the clearance, and dense marks thin out (fewer segments, bars or cells) rather than crowd.

**Stroke Weight follows the pen:** Stroke Weight is the Pen Diameter in px; the panel shows it rather than offering a slider. Role weights (heavy, primary, secondary, fine) still scale it on screen and pick pens in a multi-pen plot, but on paper every line is one nib wide. The pen-width reference in the bottom margin prints the Pen Diameter.

### 18.3 Detail Scale Multiplier

A size multiplier (0.5–2.0, default 1.0) applied to small detail elements throughout the composition. Unlike the greeble amount slider (which controls density/probability), detail scale controls the physical size of:

- Dash lengths and gaps in dashed lines
- Tick mark lengths on radial and ruler elements
- Barcode bar widths and gaps
- Texture stamp sizes
- Dither cell sizes
- Styled polygon parameters (dot sizes, hash lengths)

**At 0.5:** Details are half their default size — finer, more intricate output suitable for high-resolution plotters with fine pens.
**At 1.0:** Default proportions.
**At 2.0:** Details are double size — bolder, coarser output suitable for thick pens or lower-resolution plotters.

This parameter is NOT randomized — it is a physical-world setting tied to the plotter hardware.

### 18.4 Registration / Crop Marks

Optional L-shaped crop marks at the four corners of the content area, drawn within the margin zone. Used for:
- Aligning multi-pass plots (different pens/colors on separate passes)
- Trimming printed output to exact dimensions
- Registering the plot to a pre-cut paper edge

Marks are drawn at 0.25px stroke weight (hairline). They require margins of at least 3px to be visible. When margins are smaller, marks are suppressed.

### 18.5 Path Statistics

After each generation, the total SVG path count is displayed in the Export section. This provides:
- Complexity estimation for plotter time
- Feedback on how parameter changes affect output density
- A rough guide for when compositions may exceed plotter memory or patience limits

### 18.6 Pen Width Reference

When physical units are active (mm or inches), a small reference line is drawn in the margin area showing the actual pen stroke width with a numeric label (e.g., "0.30mm"). This helps the user verify that the stroke weight parameter produces an appropriate physical pen width for their plotter pen before committing to a full plot.

### 18.7 Layer System for Multi-Pen Plotting

When enabled, SVG content is organized into named layers using Inkscape-compatible group attributes (`inkscape:label`, `inkscape:groupmode="layer"`). Layer names are prefixed with numbers for consistent ordering:

| Layer | Content | Typical Pen |
|-------|---------|-------------|
| 1 - Main | Primary archetype geometry (shapes, glyphs, chart elements) | Medium pen |
| 2 - Borders | Cell borders, corner brackets, edge frames | Thin pen |
| 3 - Macro Text | Hero text, banner titles, large labels | Thick pen |
| 4 - Micro Text | Small annotations, identifiers, version strings, hex dumps | Fine pen |
| 5 - Details | Greebles, texture stamps, stacking fragments, dither fills, barcode accents | Extra-fine pen |
| 6 - Registration | Crop marks (only when registration marks enabled) | Hairline pen |

**Workflow:** Users enable layers, generate a composition, export SVG, then open in Inkscape or vpype. Each layer can be sent to a different pen, plotted in a separate pass, or assigned a different color for multi-color output. Layer names are editable in the UI for custom workflows.

**SVG structure when layers enabled:**
```xml
<svg xmlns="http://www.w3.org/2000/svg"
     xmlns:inkscape="http://www.inkscape.org/namespaces/inkscape"
     width="210.00mm" height="297.01mm"
     viewBox="0 0 2480 3508">
  <rect width="100%" height="100%" fill="#0a0612"/>
  <g inkscape:label="1 - Main" inkscape:groupmode="layer">
    <!-- primary geometry -->
  </g>
  <g inkscape:label="2 - Borders" inkscape:groupmode="layer">
    <!-- border elements -->
  </g>
  <!-- ... additional layers ... -->
</svg>
```

### 18.8 SVG Export Specifications

The exported SVG adheres to these plotter-compatibility requirements:

- **Stroke-only paths:** All geometry uses `fill="none"` with stroked outlines. No filled regions (pen plotters cannot fill areas natively).
- **Stroke attributes:** `stroke-linecap="square"`, `stroke-linejoin="miter"` set on the root `<svg>` element for consistent pen behavior.
- **Coordinate precision:** All path coordinates rounded to 2 decimal places (0.01px resolution).
- **Physical dimensions:** When mm or inch units are selected, `width` and `height` attributes carry unit suffixes. The `viewBox` remains in pixels.
- **Single-line fonts:** the Plotterm SVG micro fonts produce single-stroke open paths — the pen draws each character in one continuous motion without retracing.
- **OTF outline fonts:** Macro fonts (H74 series) produce closed-path outlines rendered as strokes — the pen traces the letter contour.

### 18.9 Plotter Workflow Recommendations

- **Travel optimization:** Process exported SVG through vpype (`vpype read input.svg linesort write output.svg`) to minimize pen-up travel distance between paths.
- **Gap enforcement:** Stroke Weight follows the pen, and generators space their lines from it (section 18.2, Line clearance); with thick pens, raising Detail Scale still gives the marks more room.
- **Test plots:** Use the pen width reference indicator to verify stroke width before full plots. Generate a simple single-cell composition at the target paper size to test pen/paper compatibility.
- **Multi-pen workflow:** Enable layers, export SVG, open in Inkscape, select each layer and send to plotter with the appropriate pen loaded.
- **Paper size verification:** After selecting a paper preset, check the displayed dimensions match your physical paper. Adjust DPI if your plotter uses a non-standard resolution.

---

## Section 19: Animation System

micro-MACRO supports frame-based animation for pen plotter stop-motion output. Each frame is a complete vector composition — there is no inter-frame interpolation, motion blur, or alpha blending. Animation is discrete: the plotter draws one complete frame, the camera captures it, then the next frame is drawn. This produces a mechanical, stepped motion aesthetic that reinforces the operational/diagnostic fiction.

### 19.1 Animation Parameters

#### Core Parameters
- **Animation enabled** (boolean, default false): Master toggle. When off, the application operates in static single-frame mode (current behavior).
- **Frame count** (integer, 4–120, default 24): Total frames in the animation loop. The loop is always seamless — frame N transitions back to frame 1 without visible discontinuity.
- **FPS** (number, 1–30, default 12): Frames per second for playback and export. The UI displays the resulting **loop duration in seconds** (frame count ÷ FPS). Common plotter animation rates: 6 FPS (deliberate, mechanical), 12 FPS (standard stop-motion), 24 FPS (smooth cinematic).
- **Current frame** (integer, 1–frame count): Scrubber/slider to preview individual frames. The canvas displays the selected frame.

#### Timing Parameters
- **Global cycle length** (integer, equals frame count): The master animation cycle. All per-element animation rates are expressed as fractions or multiples of this cycle.
- **Animation seed offset** (integer, 0–99): Added to the composition seed to vary animation phasing without changing the base composition. Seed `ABC` at offset 0 and offset 1 produce the same shapes but with different animation timing.

#### User Animation Controls
- **Animation density** (slider, 0.0–1.0, default 0.5): Controls what fraction of eligible elements receive animation. At 0.0, only the single most prominent element animates. At 1.0, every eligible element animates. At the default 0.5, roughly half of eligible elements animate, selected by visual priority (largest/most central elements first). This interacts with the scale-driven density from Section 19.3 — the user slider acts as a multiplier on the scale-derived density.
- **Global speed multiplier** (slider, 0.25–4.0, default 1.0, step 0.25): Scales all animation rates uniformly. At 0.25, everything moves at quarter speed (4× slower). At 4.0, everything moves 4× faster. Useful for tuning the overall energy of the animation without adjusting individual element rates. Does not change frame count — it changes how much each element moves per frame.
- **Motion amount** (slider, 0.0–1.0, default 0.5): Controls the magnitude/amplitude of all motion. At 0.0, animated elements barely move (1° rotation per frame, 0.5% scale oscillation). At 1.0, full-range motion (large rotation steps, bold scale swings, fast scrolling). This prevents over-busy or over-subtle results without the user needing to tune individual element parameters. Applied as a multiplier to rotation increments, scale amplitudes, translation distances, scroll speeds, and jitter ranges.
- **Stillness ratio** (slider, 0.0–1.0, default 0.3): The minimum fraction of elements that remain completely static across all frames. At 0.0, no elements are forced static (though some may naturally be non-animated). At 1.0, all elements are static (no animation despite animation mode being enabled). The default 0.3 enforces compositional rule 14 (at least 30% of elements remain still). Elements selected for stillness are chosen from the least visually prominent elements first (greebles before primary shapes).

### 19.2 Per-Element Animation Behaviors

Each element in the composition receives animation parameters during generation. These are stored alongside the element's geometry and applied per-frame during rendering.

#### Rotation Animations
- **Ring rotation**: Concentric rings rotate at independent speeds. Speed expressed as revolutions per cycle (e.g., 0.5 = half turn per full loop, 2.0 = two full turns). Adjacent rings default to counter-rotation.
- **Radial sweep**: A single arc or spoke advances its angle by `360° / frameCount` per frame, completing one full sweep per cycle. Multiple sweeps can run at different rates.
- **Tick orbit**: Radial tick marks rotate around their parent ring at a different rate than the ring itself.
- **Sigil spin**: Entire sigil or individual arm rings rotate. N-fold symmetric elements snap to symmetric positions (e.g., a 6-arm sigil advances by 60° per step, completing one full rotation over 6 frames or multiples thereof).
- **Gear mesh**: Interlocking gears rotate at mechanically correct ratios (gear A with 8 teeth meshes with gear B with 12 teeth → A rotates 1.5x faster than B, opposite direction).
- **Bezel rotation**: Outer ring/edge rotates while interior remains static.

#### Scale Animations
- **Breathing**: Element scale oscillates sinusoidally. Amplitude: 1–5% of base size. Period: typically 8–16 frames. Applied to standalone glyphs, seal badges, organic forms.
- **Ring pulse**: Concentric rings expand outward from center in sequence. Each ring appears at minimum radius, expands to maximum, then is replaced by the next ring. Creates a sonar-ping effect.
- **Crosshair lock**: Crosshair lines converge from wide to tight over N frames, hold for M frames, then snap back. Convergence is linear; the snap-back is instant (1 frame).
- **Corner bracket snap**: Brackets animate between expanded (wide padding) and contracted (tight padding) positions.

#### Translation Animations
- **Edge crawl**: A marker (bold dot, short dash) travels along a border or ring perimeter. Position advances by `perimeter / frameCount` per frame.
- **Scanline sweep**: A horizontal or vertical line moves across the composition. Position advances by `height / frameCount` per frame (top-to-bottom or left-to-right).
- **Assembly/disassembly**: Elements translate from off-canvas positions to their final positions over N frames, hold, then translate back out. Used for borders, brackets, and cell divisions.
- **Jitter**: Random positional offset per frame (±1–2 pen-widths). Used for asymmetric elements and glitch effects. Offset values are seeded for deterministic reproduction.

#### Data Animations
- **Counter/ticker**: Numeric text values increment, decrement, or random-walk per frame. Step size and direction are parameters. Values wrap at min/max bounds.
- **Text scramble**: Characters start as random glyphs and progressively resolve to the target string. Resolution order: left-to-right, random, or center-outward. Typically resolves over 6–12 frames, holds for remaining frames, then re-scrambles.
- **Terminal scroll**: New text lines appear at the bottom of a text block, previous lines shift upward. Oldest lines are removed. Scroll rate: 1 line per N frames.
- **Typewriter reveal**: Characters of a string appear left-to-right, one per frame or one per N frames. After the full string is revealed, it holds, then clears and restarts.
- **Status cycle**: Status labels cycle through a predefined state sequence (ACTIVE → STANDBY → SCANNING → LOCKED → ACTIVE). One state per N frames.
- **Waveform scroll**: Sparkline/waveform data shifts left by one sample per frame. A new random sample enters at the right edge. Creates continuous scrolling oscilloscope effect.
- **Bar graph cycle**: Bar heights interpolate between two or more random value sets over the cycle.
- **Polar chart morph**: Radar/polar chart vertices interpolate between two value sets, creating a morphing data polygon.
- **Progress arc advance**: Arc fill angle grows from 0° to a target over N frames, then resets.

#### Fill Animations
- **Hatch angle rotation**: The hatch fill angle increments per frame while the shape outline remains static. Creates a shimmering/rotating fill effect.
- **Hatch density breathing**: Hatch spacing oscillates (dense → sparse → dense). Simulates a pulse/glow through line density.
- **Progressive fill**: Shapes start as outlines, accumulate hatch density over several frames until fully filled, then return to outline.
- **Dither crawl**: Checkerboard/dither pattern shifts by one cell per frame.

#### Presence Animations
- **Blink**: Element alternates between drawn and not-drawn on alternating frames (or every N frames). Used for warning icons, cursor blocks, status indicators.
- **Stagger cascade**: Elements in a multi-element composition appear one-per-frame in sequence. After all elements are present, they hold, then disappear in reverse order (or simultaneously).
- **Dropout flicker**: Random rectangular regions are omitted from rendering on random frames. Creates signal-loss/glitch effect.
- **Draw-on reveal**: Path elements are drawn progressively — each frame adds more of the total path length. Over N frames, the entire composition "draws itself." The path ordering determines the visual sequence.

### 19.3 Animation and Scale Interaction

The micro-macro scale position determines animation density, speed, and character:

| Scale Zone | Frame Count Bias | Simultaneous Animations | Animation Types |
|---|---|---|---|
| Deep Micro (0.00–0.20) | 4–12 frames | 4–8 elements animate | Counter tick, terminal scroll, ring rotation, scan sweep, data update, waveform scroll |
| Micro (0.20–0.40) | 8–16 frames | 2–4 elements animate | Primary rotation + 1–2 data animations |
| Mid (0.40–0.60) | 12–24 frames | 1–2 elements animate | Single rotation/sweep with minimal secondary |
| Macro (0.60–0.80) | 16–32 frames | 1 element animates | Slow rotation, breathing, or hatch shift |
| Deep Macro (0.80–1.00) | 24–48 frames | 0–1 element animates | Near-stillness: barely perceptible rotation or scale pulse |

The animation speed hierarchy rule applies: primary/largest elements animate slowest, secondary elements at medium speed, tertiary elements (greebles, ticks) fastest.

### 19.4 Animation and Treatments

Aesthetic treatments have specific animation behaviors:

- **Glitch**: Per-frame randomized slice displacements, dropout regions, and fragment positions. No two consecutive frames share the same disruption pattern. Glitch intensity can itself oscillate over the cycle.
- **Terminal**: Scanline position sweeps downward. Cursor block blinks (present on even frames, absent on odd). Stroke weight oscillates ±10% to simulate phosphor pulse.
- **Corruption**: Decay spreads directionally across the composition over the cycle. Missing segments grow per frame. At loop reset, corruption snaps back to initial state.
- **Algorithm**: Each frame shows the next iteration step of a parametric process. A spiral adds one turn. A recursive subdivision adds one level. Parametric variation sweeps one parameter linearly.
- **Hacking**: Override labels flash (appear/disappear). Bracket intrusion extends further per frame. Memory dump values scroll upward. Breach intensity crescendos over the loop, then resets.

### 19.5 Frame Generation Pipeline

When animation is enabled, the `generate()` function accepts a `frameIndex` parameter (0 to frameCount-1):

1. **Base composition**: Generated once from the seed (same geometry, same layout, same archetype selection for all frames).
2. **Per-frame modifiers**: Each animated element applies its animation function using `frameIndex` to compute its current state (rotation angle, position offset, text content, hatch angle, visibility, etc.).
3. **Deterministic**: Frame N always produces identical output for a given seed + animation parameters. No `Math.random()` — all randomness comes from the seeded PRNG with frame-specific offsets.
4. **Frame-independent**: Each frame can be generated independently (no dependency on previous frame's state). This enables parallel generation and random-access frame preview.

### 19.6 Export Formats

#### GIF Export
- Renders each frame to the p5.js canvas, captures as image data, encodes to animated GIF.
- Uses a client-side GIF encoder (e.g., gif.js or similar library).
- Settings: loop count (infinite or N), frame delay (derived from FPS), canvas resolution.
- Color: mapped to the composition's foreground/background two-color palette.

#### MP4/WebM Video Export
- Renders frames to canvas, captures via `MediaRecorder` API or `canvas.captureStream()`.
- Settings: resolution, FPS, codec (H.264 for MP4, VP9 for WebM).
- Higher quality than GIF; supports full resolution output.

#### SVG Export — Single File, Frames as Layers
- All frames exported into a single SVG file, each frame as a separate Inkscape-compatible layer.
- Layer naming: `"F01 - Frame 1"`, `"F02 - Frame 2"`, etc. (numbered prefix for ordering).
- When combined with the existing content layer system (Main, Borders, Text, Details), each frame layer contains sub-layers for content types.
- The plotter operator shows one frame layer at a time, plots it, then switches to the next.

#### SVG Export — Individual Files Per Frame
- Each frame exported as a separate SVG file.
- File naming: `{prefix}_{seed}_F{frameNumber}.svg` (e.g., `micrographic_a1b2c3d4_F01.svg`).
- Each file is a complete standalone SVG with all content layers, physical units, and registration marks.
- Suitable for batch plotting workflows where the plotter software processes a queue of files.

#### SVG Export — Frames to Grid (Contact Sheet)
- All frames arranged in a grid on one or more sheets of paper (using the current paper size preset).
- Grid layout: frames arranged left-to-right, top-to-bottom, with configurable padding between frames.
- Frame scale: computed to fit the maximum number of frames per sheet. If frames don't fit on one sheet, multiple sheets are generated.
- Output options:
  - **Layered SVG**: Each sheet is one SVG file, frames as separate layers within that sheet.
  - **Individual SVGs**: One SVG file per sheet.
- Each frame in the grid includes its frame number label and optional registration marks.
- Purpose: allows cutting individual frames from a single large sheet for stop-motion assembly, or contact-sheet review of the full animation at a glance.

### 19.7 Per-Frame Registration Marks

When animation export is active and registration marks are enabled:

- **Standard registration marks**: L-shaped crop marks at the four corners of each frame's content area (same as static registration marks from Section 18.4).
- **Frame number**: A small text label in the margin displaying the frame number (e.g., `F01`, `F12`). Position: bottom-left margin. Font: builtin single-line at minimum text size. This label changes per frame.
- **Sequence indicator**: Optional thin progress bar or tick mark along one margin edge showing the frame's position within the full animation cycle. Frame 1 shows a tick at 0%, frame 12 of 24 shows a tick at 50%, etc.
- **Alignment crosses**: Small crosshair marks at the center of each edge (top, bottom, left, right) for precise multi-frame alignment. These are identical across all frames — they serve as registration anchors.
- **Purpose**: When plotting frames individually, registration marks ensure each frame can be precisely aligned for photography. The frame number prevents sequencing errors. The sequence indicator provides visual context for the animation position.

### 19.8 Preview and Playback

The application provides in-browser animation preview:

- **Play/Pause button**: Toggles continuous frame advancement at the set FPS.
- **Frame scrubber**: A slider to manually step through frames. Displays current frame number and loop time position.
- **Loop indicator**: Shows `Frame N / Total | X.Xs` (e.g., `Frame 12 / 24 | 1.0s`).
- **Preview quality**: Canvas renders at screen resolution (not print DPI) for responsive playback. Export renders at full DPI.
- **Onion skinning** (optional): Previous frame rendered at reduced stroke weight behind the current frame, helping visualize motion between frames. Toggled via checkbox.

### 19.9 Animation and Multi-Cell Compositions

In multi-cell (subdivided) compositions, animation parameters are assigned per-cell:

- Each cell can have independent animation types and rates.
- The cell layout (subdivision grid) remains static across all frames — cells don't move or resize.
- Different cells can be at different points in their animation cycle if their rates differ.
- Stagger offset: cells can have a frame offset applied to their animation, so cell 1 starts its cycle at frame 0, cell 2 at frame 3, cell 3 at frame 6, etc. This creates a cascading ripple of activity across the composition.

### 19.10 Animation Seed Behavior

- The **composition seed** determines the base geometry (shapes, layout, archetypes, text content). This is identical across all frames.
- The **animation seed offset** shifts the PRNG state for animation-specific randomness (jitter offsets, glitch patterns, text scramble sequences). Changing the offset produces different animation timing without changing the underlying composition.
- **Frame-specific randomness**: When an animation needs per-frame random values (e.g., jitter, glitch dropout positions), the PRNG is seeded with `compositionSeed + animationOffset + frameIndex`. This ensures deterministic reproduction of every frame.

### 19.11 Performance Considerations

- **Frame generation time**: Each frame runs the full generation pipeline. For complex multi-cell compositions at high DPI, this can take several seconds per frame. The UI should display a progress indicator during batch export.
- **SVG file size**: Animated exports can be large. A 24-frame animation with 500 paths per frame produces ~12,000 paths in a layered SVG. The path statistics display should show per-frame and total path counts.
- **Canvas preview optimization**: During playback, render at reduced resolution (e.g., 50% canvas size) for smooth FPS. Full resolution is used only for export.
- **Memory**: GIF/video export must process all frames sequentially, releasing each frame's canvas data after encoding. Do not store all frames simultaneously in memory.

### 19.12 Plotter Stop-Motion Workflow

The intended physical workflow for pen plotter animation:

1. **Design**: Set composition parameters, enable animation, set frame count and FPS. Preview in-browser to approve the motion.
2. **Export**: Choose export format:
   - Individual SVGs for one-frame-at-a-time plotting
   - Contact sheet grid for batch plotting multiple frames per sheet
   - GIF/MP4 for digital-only distribution
3. **Plot**: Feed SVG files to plotter. Each frame is plotted on a fresh sheet of paper (or a fresh region of a large sheet for contact grid mode).
4. **Photograph**: Photograph each plotted frame under consistent lighting and camera position. Registration marks ensure alignment.
5. **Assemble**: Import photographs into video editor or stop-motion software. Set playback rate to match the exported FPS.
6. **Output**: Render final video from the photographed frames. The pen plotter line quality, paper texture, and physical ink presence create an aesthetic impossible to replicate digitally.

**Multi-pass animation**: An alternative workflow where multiple frames are plotted on the same sheet of paper, each pass adding content. The camera captures after each pass. This creates a build-up effect where the composition accumulates density across frames — an animation technique unique to physical plotter output.
