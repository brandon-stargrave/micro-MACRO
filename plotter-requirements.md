# Pen Plotter Compatibility Requirements

---

## 1. Design Philosophy

micro-MACRO's primary output format is SVG for pen plotters (AxiDraw, iDraw, etc.) as well as laser cutters, vinyl cutters, and other CNC vector tools. The pen plotter use case is the driving reason for the vector-first architecture:

- All drawing must reduce to single-stroke paths
- All text must be rendered as stroked paths (not filled outlines)
- All visual effects must be representable as geometry rather than raster operations
- No raster-only techniques (blur, glow, pixel noise, alpha blending)

Every visual element must reduce to toolpaths that a plotter can trace.

---

## 2. SVG Architecture

### 2.1 Dual-Path Recording

All drawing operations produce both a visual canvas render (via p5.js) AND a parallel SVG path representation. This is the primary technical constraint of the application.

Wrapper functions intercept every p5.js draw call and build SVG `<path>` elements in parallel:

| p5.js Call | SVG Equivalent |
|---|---|
| `line(x1,y1,x2,y2)` | `<path d="M x1 y1 L x2 y2" />` |
| `rect(x,y,w,h)` | `<path d="M x y L x+w y L x+w y+h L x y+h Z" />` |
| `ellipse(x,y,w,h)` / `circle(x,y,d)` | `<path>` using cubic Bezier arc approximation |
| `arc(x,y,w,h,start,stop)` | `<path>` using `A` (arc) commands |
| `beginShape()`...`vertex()`...`endShape()` | `<path d="M ... L ... L ... Z" />` |
| `bezier()` / `bezierVertex()` | `<path d="M ... C ..." />` |
| `point(x,y)` | `<circle cx="x" cy="y" r="strokeWeight/2" />` |

### 2.2 Stroke-Only Output

- **No fills.** All geometry uses `fill="none"` with `stroke` only. Pen plotters trace paths -- they cannot fill regions. The background `<rect>` is the sole exception (plotters ignore it).
- **Stroke weight is informational.** The SVG includes `stroke-width` for on-screen display, but pen plotters use the physical pen width. The exported stroke weight serves as a visual preview of line density.
- **Stroke caps and joins:** `strokeCap(SQUARE)` and `strokeJoin(MITER)` for the technical/precise aesthetic (not rounded caps).

### 2.3 Path Continuity

Where possible, connected line segments are joined into single continuous `<path>` elements to minimize pen lifts. This improves plotting speed and reduces wear.

### 2.4 No Overlapping Duplicate Strokes

When geometry naturally overlaps (e.g., a crosshair line passing through a circle's center), the overlapping segments are not drawn twice. The plotter would trace them twice, wasting time and depositing excess ink.

### 2.5 SVG Document Structure

```xml
<svg xmlns="http://www.w3.org/2000/svg"
     width="{outputWidth}" height="{outputHeight}"
     viewBox="0 0 {outputWidth} {outputHeight}">
  <rect width="100%" height="100%" fill="{backgroundColor}" />
  <g id="composition" stroke="{foregroundColor}" fill="none"
     stroke-width="{baseStrokeWeight}"
     stroke-linecap="square" stroke-linejoin="miter">
    <g id="cell-0">
      <g id="border-0"><!-- border paths --></g>
      <g id="content-0"><!-- element paths --></g>
    </g>
    <!-- ... -->
  </g>
</svg>
```

### 2.6 SVG Optimization

- Combine consecutive paths with identical stroke/fill attributes into a single `<path>` with multiple subpaths where possible
- Use relative path commands (`l`, `c`, `a`) to reduce file size
- Round coordinate values to 2 decimal places (0.01px resolution)
- Group elements hierarchically: `composition > cell > border | content > individual elements`

### 2.7 Coordinate Precision

2 decimal places (0.01px resolution) provides sufficient precision for plotters with typical step resolutions of 0.05-0.1mm.

---

## 3. Text Rendering

All text is rendered as `<path>` elements -- no `<text>` elements are ever used. This guarantees pen plotter compatibility and eliminates font dependency issues in exported SVGs.

### 3.1 Micro Fonts (Single-Line)

Single-line (single-stroke) SVG fonts from `/fonts/micro/`. Their glyphs are defined as open paths, not filled outlines. They render as stroked lines matching the foreground color and stroke weight.

- Each character is drawn as a continuous toolpath, not a filled region
- Minimal pen lifts, optimal for plotting
- Rendered with `stroke="{fgColor}" fill="none"` and the current stroke weight

### 3.2 Macro Fonts (Outline)

Standard outline fonts (TTF/OTF/WOFF) from `/fonts/macro/`. Glyph paths are extracted via opentype.js and converted to `<path>` elements.

- Rendered with `stroke="{fgColor}" fill="none"` -- the pen traces the letter's outline boundary
- Inner contours (counters in letters like O, A, B) are separate stroked paths
- Stroke weight for macro font outlines matches the base stroke weight setting

### 3.3 Path-Only Rule

- There is no `<text>` element strategy in SVG export -- all text is always `<path>` elements
- Glyph path data is cached per character per font per size in a `Map` to avoid redundant extraction
- SVG output is fully self-contained -- no external font references

---

## 4. No Opacity Variation

Pen plotters cannot vary opacity -- ink is either deposited or not. All SVG elements intended for plotter output must use full opacity.

- Visual weight and density must be achieved through geometric means: line density, stippling, hatching, and spacing
- Any use of reduced opacity in the SVG (e.g., for ghost layers, duplicate-offset glitch effects) is a screen preview convenience only
- When exporting for plotter, opacity-based effects should be translated to geometric equivalents or omitted
- Tonal gradients are achieved through variable hatch spacing, stipple density, or multi-stroke density -- never through transparency

---

## 5. Fill Simulation Techniques

Since pen plotters cannot fill regions, "solid" and "hybrid" rendering modes must be adapted to plotter-compatible geometry.

### 5.1 Hatch Fill System

Parallel line patterns simulate filled regions. Configurable parameters:

| Parameter | Range | Purpose |
|---|---|---|
| Enabled | boolean | Master toggle for hatch fills |
| Angle | 0-180 degrees | Direction of hatch lines |
| Spacing | 0-20 px | Distance between parallel lines |
| Pen Diameter | 0.05-5 mm | Physical pen tip width -- hatch spacing must exceed this to avoid overlap |
| Gradient | boolean | Enable variable spacing for tonal gradient effect |
| Gradient Range | 0-1 | How much spacing varies across the region (0 = uniform, 1 = full range) |

**Pen diameter awareness:** Hatch line spacing must account for the physical pen tip width. Lines spaced closer than the pen diameter will overlap, producing ink pooling and bleed. The minimum effective spacing is `pen_diameter * 1.2`.

**Crosshatching:** Two overlapping hatch passes at different angles produce denser fill. Available as a future enhancement.

### 5.2 Stippling

Dense dot patterns as an alternative to hatching for simulating filled or tonal regions.

- Each dot is a tiny circle or point path
- Dot density controls perceived darkness
- Placement options: random (organic feel) or grid-based (mechanical feel)
- Variable spacing for gradient effects -- denser dots in darker regions, sparser in lighter
- Dot size should match or slightly exceed pen tip width for consistent marks
- Stippling produces a different visual texture than hatching -- more granular, organic

### 5.3 Rendering Mode Adaptation

| Canvas Mode | Plotter Adaptation |
|---|---|
| Line mode | Stroke only, no fill. Direct plotter compatibility. |
| Solid mode | Outline stroke + hatch fill or stipple fill inside the region |
| Hybrid mode | Stroke-only with selective hatching/stippling where fills would appear |

---

## 6. Multi-Stroking for Line Width Variation

Pen plotters draw with a fixed-width pen. To achieve visual line weight variation within a single-pen composition:

- A "thick" line is rendered as 2-3 closely spaced parallel paths, each offset by the pen diameter
- This creates the appearance of a heavier stroke while using the same physical pen
- Offset distance should equal the pen diameter for seamless coverage without gaps
- Applicable to borders, primary structural lines, and hero text outlines where visual weight hierarchy is desired
- Multi-stroking multiplies path count -- use judiciously on key elements, not globally

---

## 7. Unit System & Physical Dimensions

The application operates internally in pixels. A unit system provides bidirectional conversion for display and export:

| Unit | Display Format | SVG Export | Conversion |
|---|---|---|---|
| px | Integer | Unitless (`width="800"`) | Direct (no conversion) |
| mm | 1 decimal | `width="210.00mm"` | `px = mm x DPI / 25.4` |
| in | 3 decimals | `width="8.500in"` | `px = in x DPI` |

**DPI** (dots per inch) maps between pixels and physical dimensions. Default: 96 (screen). Common plotter values: 300, 600, 1016.

**Paper presets** set width and height to standard paper dimensions at the current DPI:

| Preset | Dimensions |
|---|---|
| A5 | 148 x 210 mm |
| A4 | 210 x 297 mm |
| A3 | 297 x 420 mm |
| Letter | 215.9 x 279.4 mm |

**SVG viewBox / physical dimension split:** The SVG `viewBox` always uses pixel coordinates (the internal coordinate space). The `width` and `height` attributes carry physical units, telling plotter software the intended output size. This is the correct SVG approach -- the viewBox defines the coordinate system, width/height define the physical mapping.

---

## 8. Minimum Text Size

A global floor for all text rendering, settable in mm, inches, or pixels. Default: 2mm.

**Rationale:** Pen plotters have a minimum feature size determined by pen tip width and mechanical resolution. Text below approximately 2mm in height produces illegible or damaged output on most plotters.

**Enforcement:** Centralized clamping in all text drawing and measurement functions. The clamp is applied before any rendering or layout calculation, ensuring that compositions never allocate space for text that would be too small to plot.

**Interaction with DPI:** The minimum converts to pixels via the current DPI setting. At 96 DPI, 2mm = ~7.6px. At 300 DPI, 2mm = ~23.6px. Higher-DPI compositions automatically produce larger text in pixel terms, which is correct -- the same 2mm physical text requires more pixels at higher resolution.

---

## 9. Detail Scale Multiplier

A size multiplier (0.5-2.0, default 1.0) applied to small detail elements throughout the composition. Unlike the greeble amount slider (which controls density/probability), detail scale controls the physical size of:

- Dash lengths and gaps in dashed lines
- Tick mark lengths on radial and ruler elements
- Barcode bar widths and gaps
- Texture stamp sizes
- Dither cell sizes
- Styled polygon parameters (dot sizes, hash lengths)

| Value | Effect | Use Case |
|---|---|---|
| 0.5 | Half-size details -- finer, more intricate | High-resolution plotters with fine pens |
| 1.0 | Default proportions | Standard plotting |
| 2.0 | Double-size details -- bolder, coarser | Thick pens or lower-resolution plotters |

This parameter is NOT randomized -- it is a physical-world setting tied to plotter hardware.

---

## 10. Registration & Crop Marks

Optional L-shaped crop marks at the four corners of the content area, drawn within the margin zone.

**Purpose:**
- Aligning multi-pass plots (different pens/colors on separate passes)
- Trimming printed output to exact dimensions
- Registering the plot to a pre-cut paper edge

**Specifications:**
- Drawn at 0.25px stroke weight (hairline)
- Require margins of at least 3px to be visible; suppressed when margins are smaller

### 10.1 Animation Registration Marks

When animation export is active:

- **Standard crop marks**: L-shaped at four corners of each frame's content area
- **Frame number**: Small text label in bottom-left margin (`F01`, `F12`, etc.), changes per frame
- **Sequence indicator**: Thin progress bar along one margin edge showing frame position within the animation cycle
- **Alignment crosses**: Small crosshair marks at edge centers (top, bottom, left, right) -- identical across all frames for precise multi-frame alignment

---

## 11. Path Statistics

After each generation, the total SVG path count is displayed in the Export section. This provides:

- Complexity estimation for plotter time
- Feedback on how parameter changes affect output density
- A rough guide for when compositions may exceed plotter memory or patience limits

---

## 12. Pen Width Reference

When physical units are active (mm or inches), a small reference line is drawn in the margin area showing the actual pen stroke width with a numeric label (e.g., "0.30mm"). This helps the user verify that the stroke weight parameter produces an appropriate physical pen width for their plotter pen before committing to a full plot.

---

## 13. Layer System for Multi-Pen Plotting

When enabled, SVG content is organized into named layers using Inkscape-compatible group attributes (`inkscape:label`, `inkscape:groupmode="layer"`). Layer names are prefixed with numbers for consistent ordering:

| Layer | Content | Typical Pen |
|---|---|---|
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

---

## 14. SVG Export Specifications

The exported SVG adheres to these plotter-compatibility requirements:

- **Stroke-only paths:** All geometry uses `fill="none"` with stroked outlines
- **Stroke attributes:** `stroke-linecap="square"`, `stroke-linejoin="miter"` on the root `<svg>` element
- **Coordinate precision:** All path coordinates rounded to 2 decimal places
- **Physical dimensions:** When mm or inch units are selected, `width` and `height` attributes carry unit suffixes; `viewBox` remains in pixels
- **Single-line fonts:** Micro fonts produce single-stroke open paths -- the pen draws each character in one continuous motion
- **Outline fonts:** Macro fonts produce closed-path outlines rendered as strokes -- the pen traces the letter contour
- **No `<text>` elements:** All text is pre-rendered as `<path>` data

---

## 15. Aesthetic Treatment Constraints

All aesthetic treatments must produce output representable as SVG paths. No raster-only techniques.

### 15.1 Permitted Techniques

| Treatment | SVG-Compatible Approach |
|---|---|
| Glitch: horizontal displacement | `<clipPath>` rectangles with `translate` transforms on bands |
| Glitch: duplicate offset | Duplicate `<g>` with slight `translate` offsets |
| Glitch: missing scanlines | Omit geometry in excluded Y-ranges during generation |
| Terminal: scan line overlay | Horizontal `<line>` elements at regular intervals (max ~100 per composition) |
| Terminal: cursor | Small filled `<rect>` at end of text line |
| Corruption: missing segments | Gap insertion in multi-segment paths at path-building level |
| Corruption: character substitution | Replace characters with accent glyphs before rendering |
| Corruption: redaction blocks | Replace text paths with filled `<rect>` elements |
| Algorithm: visible iteration | Same element at multiple parametric stages, overlaid |
| Algorithm: parametric variation | Base form repeated with linear parameter changes |
| Hacking: exposed structure | Secondary hex dump text layer behind main content |
| Hacking: bracket intrusion | Corner bracket arms extended past cell boundaries |

### 15.2 Eliminated Techniques

These raster-dependent techniques are not compatible with plotter output:

- **Noise pixel scatter:** Would require hundreds of tiny `<rect>` elements, bloating SVG
- **Phosphor glow / blur:** Requires filter effects (raster). Approximated with duplicate lines at slight offset instead.
- **Blinking / animation:** Static SVG cannot blink. Adapted as double-border framing.
- **Edge roughness / noise border:** Expensive per-vertex perturbation. Deferred.
- **Layered transparency:** Doubles path count. Adapted as low-opacity `<g>` for screen preview only.

---

## 16. Animation / Stop-Motion Workflow

### 16.1 SVG Animation Export Formats

| Format | Description |
|---|---|
| Single SVG, frames as layers | Each frame is an Inkscape-compatible layer; operator shows one at a time |
| Individual SVG files | One file per frame: `{prefix}_{seed}_F{nn}.svg` |
| Contact sheet grid | All frames arranged on paper-sized sheets for batch plotting |

### 16.1.1 Raster Preview / Archival Formats

While SVG is the plotter target, raster exports serve as visual preview and archival records of the intended output:

| Format | Description |
|---|---|
| GIF | Client-side encoded animated GIF for sharing and preview. Supports 1-bit transparency via a chroma-key color (pixels are composited onto magenta `#FF00FF`, which gif.js emits as transparent). |
| MP4 | `MediaRecorder`-based H.264 video export. The codec level is chosen dynamically from canvas resolution (Level 3.1 for 720p, 4.0 for 1080p, 5.1 for 4K, 6.0 for 8K) — past projects hit encoder refusal at 1080x1080+ when the default browser codec was too low-level. The bitrate target is `width * height * fps * 0.15` bits/sec, high enough for sharp two-color line art. MP4 **does not support transparency** — selecting MP4 with "Transparent background" enabled shows a warning and falls back to opaque rendering. |
| MOV (lossless) | **Uncompressed lossless QuickTime MOV using the `png ` codec** — PNG sequence wrapped in a MOV container. This is the standard "lossless archival" format used by motion graphics tools (After Effects, Premiere, Resolve, FFmpeg). Each frame is a full-resolution PNG, concatenated into the container's `mdat` atom with offsets tracked in the `moov > trak > mdia > minf > stbl` sample tables. No transcoding, no quality loss. PNG frames carry alpha natively, so MOV fully supports transparent export. |

### 16.1.2 Transparent Background Export

A "Transparent background" checkbox near the export buttons switches all export formats to a background-free mode. The main canvas is rendered with `clear()` instead of `background()`; the SVG serializer omits the full-canvas background rect; the GIF encoder receives frames composited on a chroma-key magenta and marks that color as transparent. Supported formats:

| Format | Alpha support |
|---|---|
| PNG | Full 8-bit alpha (native) |
| SVG | Full alpha (background rect omitted) |
| GIF | 1-bit transparency (chroma-key compositing) |
| MOV | Full alpha via PNG codec |
| MP4 | Not supported — warning shown, opaque fallback used |

### 16.2 Physical Plotting Workflow

1. **Design**: Set parameters, enable animation, set frame count and FPS. Preview in-browser.
2. **Export**: Choose SVG format (individual files, layered, or contact sheet).
3. **Plot**: Feed SVG files to plotter. Each frame plotted on a fresh sheet (or fresh region for contact grid).
4. **Photograph**: Photograph each plotted frame under consistent lighting. Registration marks ensure alignment.
5. **Assemble**: Import photos into stop-motion software. Set playback rate to match exported FPS.
6. **Output**: Render final video. Physical pen line quality, paper texture, and ink presence create an aesthetic impossible to replicate digitally.

### 16.3 Multi-Pass Animation

Alternative workflow where multiple frames are plotted on the same sheet, each pass adding content. The camera captures after each pass. Creates a build-up effect where the composition accumulates density across frames -- a technique unique to physical plotter output.

### 16.4 Frame Independence

Each frame can be generated independently (no dependency on previous frame state). This enables parallel generation, random-access preview, and selective re-plotting of individual frames.

---

## 17. Workflow Recommendations

### 17.1 Travel Optimization

Process exported SVG through vpype to minimize pen-up travel distance:
```
vpype read input.svg linesort write output.svg
```

### 17.2 Gap Enforcement

When using thick pens, increase both Stroke Weight and Detail Scale together to ensure no two parallel paths are closer than the pen width.

### 17.3 Test Plots

Use the pen width reference indicator to verify stroke width before full plots. Generate a simple single-cell composition at the target paper size to test pen/paper compatibility.

### 17.4 Multi-Pen Workflow

Enable layers, export SVG, open in Inkscape, select each layer and send to plotter with the appropriate pen loaded.

### 17.5 Paper Size Verification

After selecting a paper preset, check the displayed dimensions match physical paper. Adjust DPI if the plotter uses a non-standard resolution.
