# Micrographics Style Guide
## Algorithmic Vector Generation Reference

---

## 1. Concept and Aesthetic Identity

This design language synthesizes two visual traditions into a single coherent system:

1. **HUD / Diagnostic Panel** — sci-fi heads-up display, military targeting systems, instrument readouts. Technical, text-laden, precise. Suggests a machine *reporting* on itself.
2. **Symbolic Glyph** — standalone icon-weight forms with strong silhouette, radial structure, and biomorphic character. Suggests a machine *language* of signs and signals.

Together they describe a world of **operational symbols** — artifacts produced by systems that are both technical and organic, monitored and alive. The viewer is positioned as an analyst reading outputs they can partially but not fully decode.

**Mood**: clinical, operational, synthetic, monitored, encrypted, quietly biological

**Medium**: All output is designed for **pen plotter** production as well as screen display. Every element must reduce to single-stroke vector paths. This constraint shapes the aesthetic — the line-art quality is not just a style choice but a physical requirement of the output medium. Filled regions, gradients, and raster effects are excluded. The visual language emerges from what a single pen nib can trace in one continuous motion.

**Animation Identity**: When animated, compositions become *operational recordings* — a machine's display captured across time. Animation reinforces the fiction that these are live systems: dials that sweep, data that scrolls, rings that rotate. The stop-motion pen plotter medium imposes a deliberate, mechanical rhythm. Each frame is a complete drawing — there is no motion blur, no easing, no interpolation. Movement is discrete, stepped, and precise. This constraint becomes the aesthetic: the animation feels like a machine stepping through states, not a smooth organic motion.

---

## 2. Rendering Modes

All elements exist in one of three rendering modes. A single generated composition should commit to one mode. Note: because all output must be pen-plotter-compatible, "solid" and "hybrid" modes are visual approximations — on-screen fills represent areas that the plotter will render as outline strokes or hatching patterns.

### Mode A: Line / Outline
- Hairline to medium stroke weight only
- No fills except dot matrices used as texture
- Dominant mode for HUD panels and reticle elements
- Suggests: technical diagram, blueprint, schematic
- **Plotter output**: Direct compatibility — what you see is what the pen draws

### Mode B: Solid / Silhouette
- Full filled forms with no internal linework
- High contrast, icon-weight
- Dominant mode for standalone glyphs
- Suggests: symbol, sigil, system icon, badge
- **Plotter output**: Filled regions are rendered as outlines only (the pen traces the shape boundary). Dense hatching fill may be offered as a future option.

### Mode C: Hybrid
- Outline container with solid accent elements inside
- Or: solid primary form with outline annotations around it
- Used for composite elements that need both density and clarity
- Example: a solid globe shape surrounded by orbit-line annotations
- **Plotter output**: Outline portions trace directly; fill portions render as outlines or hatching

**Animation and Rendering Modes**: In animated sequences, the rendering mode may shift across frames. A composition can begin in Line/Outline mode and progressively accumulate density toward Hybrid or Solid through hatch fill accumulation. This "inking" progression — outline → partial fill → dense fill — is itself an animation technique unique to the plotter medium. Each frame adds a new layer of ink to the same physical paper (multi-pass animation) or represents a new sheet in the stop-motion sequence.

---

## 3. Color System

The palette is intentionally minimal and monochromatic per composition.

### Dark Mode (HUD / Panel)
- **Background**: near-black (`#0d0d14` or similar deep navy-black)
- **Foreground**: off-white or pale lavender-gray (`#d8d8e8`)
- No gradients. No shadows. No glow effects.
- Single foreground color only.

### Light Mode (Glyph / Symbol)
- **Background**: pale warm gray or off-white (`#e8e6e8`)
- **Foreground**: single saturated color
  - Purple: `#7b4fd4` range
  - Magenta/pink: `#d44f8a` range
  - Green: `#2a9a4a` range
- The foreground color is categorical — it classifies the glyph's type or system origin, not its aesthetic mood.

### Rule
Never mix colors within a single element. One background, one foreground. The color *is* the identity.

---

## 4. Geometry Taxonomy

All forms derive from a defined set of geometric families. Understanding these as code-generatable primitives is the core of the system.

---

### 4.1 Circle Family

The most fundamental primitive. All circle variants share a center point and radius parameter.

| Variant | Description |
|---|---|
| Plain ring | Unadorned circle, used as container or orbit |
| Concentric rings | 2–5 nested circles at regular interval |
| Circle with crosshair | Quartered by perpendicular lines through center |
| Circle with gap crosshair | Lines stop short of center, leaving clear zone |
| Circle with radial ticks | Short perpendicular marks at regular angular intervals around perimeter |
| Circle with off-center indicator | Single radial tick at one clock position |
| Circle with diagonal slash | Prohibition / offline state |
| Circle with flat chord (D-shape) | One straight chord cuts across |
| Partial arc | 90°, 180°, or 270° segment; incompleteness = active state |
| Circle filled | Solid disc, used as node or terminus |
| Concentric perspective (tunnel) | Circles that decrease in radius AND shift position, implying recession |

**Circle Animation**: Concentric rings are natural candidates for **ring rotation** — each ring rotates at a different speed per frame, with adjacent rings counter-rotating. Partial arcs are ideal for **radial sweep** — the arc's start angle advances per frame like a radar sweep. Tick marks can **orbit** independently of their parent ring. The gap crosshair can **lock/converge** — crosshair lines move from wide spacing to tight over several frames, then snap back. Ring pulse animations draw concentric rings at progressively larger radii across frames, with earlier rings drawn as dashed lines to suggest fading.

---

### 4.2 Star and Radial Burst Family

All variants share rotational symmetry. The key parameters are: arm count, arm length, arm width, arm shape (sharp/rounded/tapered), and gap between arms.

| Variant | Description |
|---|---|
| 4-point sharp star | High-contrast, spike-like; elongated on one axis creates a lens-flare form |
| 4-point equal star | Equilateral, compass-like |
| 6-arm asterisk | Thin, equal arms at 60° intervals |
| 8-point starburst | Used for primary accent or "signal" indicator |
| Multi-point sunburst | 12–24 thin rays radiating from center |
| Snowflake | Asterisk with secondary branches on each arm |
| Compass rose | 8-directional with alternating long/short arms |
| Pinwheel / vortex star | Arms are curved or swept, implying rotation |
| Petal star | Arms are rounded/filled, flower-like (4, 5, or 6 petals) |
| Serrated star | Outer edge jagged; between star and gear |
| Flame ring | Arms shaped like flames or feathers, arranged in a ring |
| Thorn ring | Short outward spikes densely packed around a circle |

**Key distinction**: Stars with *straight arms* feel technical/geometric. Stars with *curved or swept arms* feel organic/kinetic.

**Star Animation**: Pinwheel and vortex stars are inherently rotational — they should **spin slowly**, with arm positions advancing by a small angular increment per frame. The multi-point sunburst can **strobe** — alternating subsets of rays drawn on odd vs. even frames. Stars with concentric clone layers can animate each layer at a different rotation rate, creating **mechanical counter-rotation**. At macro scale, a star's arms can **breathe** — scaling subtly between frames (1.0→1.03→1.0 over 8-12 frames). Serrated stars and thorn rings can rotate while their interior elements remain static, creating a **bezel rotation** effect.

---

### 4.3 Globe and Sphere Family

Suggest three-dimensional volume through two-dimensional line systems.

| Variant | Description |
|---|---|
| Wireframe sphere (front) | Circle with meridian and latitude lines |
| Sphere with horizontal stripes | Filled ellipses stacked to suggest latitude bands; Saturn-ring feel |
| Eye-globe | Elliptical (horizontal) outline with internal globe grid |
| Half-globe | Hemisphere, flat edge horizontal |
| Torus / ring with grid | Donut shape with surface grid lines |
| Concentric ellipse (snail) | Inward-spiraling ellipses, not circles |
| Perspective rectangle (tunnel) | Nested rectangles that shift position to imply depth |
| Stacked parallelogram layers | Offset horizontal planes suggesting an isometric stack |

**Globe Animation**: Wireframe spheres animate through **longitude rotation** — meridian lines shift position per frame as if the globe is turning on its axis. Stripe-count can change per frame to create a **scanning/reveal** effect. The eye-globe variant can animate a **pupil drift** — the internal iris circle shifts position subtly between frames. Half-globe stripes can **scroll** vertically. Concentric perspective tunnels animate beautifully with **depth pulse** — rings expanding outward from center in sequence, each ring drawn one frame before disappearing.

---

### 4.4 Hourglass / Bowtie / Figure-8 Family

A family of bilateral mirror forms defined by two lobes meeting at a pinch point. The most biomorphic of the geometric families.

| Variant | Description |
|---|---|
| Vertical hourglass | Classic — narrow waist, symmetric top/bottom lobes |
| Horizontal bowtie | Same form rotated 90°; wider than tall |
| Double hourglass | Two hourglasses sharing a center point (X-form) |
| Organic hourglass | Lobes are rounded blobs rather than geometric curves |
| Hourglass with inner detail | Internal line or circle at waist or in each lobe |
| Figure-8 chain | Multiple hourglass forms linked in sequence |
| Diamond pinch | Square rotated 45°, lobes are triangular rather than curved |

**Hourglass Animation**: The pinch point of an hourglass is a natural **breathing** animation point — lobes expand and contract subtly per frame. The figure-8 chain can animate as a **flow** — the linking points shift position to suggest material flowing between lobes. Diamond pinch variants can **oscillate** between orientations (45° rotation back and forth across frames).

---

### 4.5 Connector and Chain Family

Linear elements that imply connection, flow, or measurement. Always have defined endpoints.

| Variant | Description |
|---|---|
| Line with terminal dots | `•————•` |
| Dashed line | Regular or irregular rhythm |
| Line with tick marks | Perpendicular marks along length, suggests measurement |
| Bead chain | Sequence of circles on a line, uniformly spaced |
| Dumbbell / barbell | Two circles or filled endpoints with a bar between them |
| Segmented bar | Horizontal bar divided into discrete filled/empty blocks |
| Node chain | Alternating large and small circles on a line |
| Branching connector | Y or T junctions, suggesting data flow |
| Wave connector | Sinusoidal or square-wave line connecting two points |
| Stacked bars | Multiple parallel horizontal bars grouped together |
| Ladder | Two vertical rails with rungs between them |

**Connector Animation**: Wave connectors are ideal for **waveform scroll** — the sine wave shifts phase per frame, creating flowing motion. Dashed lines can animate their **dash offset** — the gap pattern slides along the line. Segmented bars animate naturally with **fill progression** — segments fill one-by-one across frames, then reset. Node chains can animate **signal propagation** — a highlighted node travels along the chain per frame.

---

### 4.6 Gear / Seal / Badge Family

Circular forms with a structured outer edge. The outer edge treatment defines the subtype.

| Variant | Description |
|---|---|
| Gear wheel | Evenly-spaced rectangular teeth protruding outward |
| Serrated badge | Small triangular teeth, denser than gear |
| Wavy edge seal | Scalloped or sine-wave perimeter |
| Dotted ring badge | Ring of dots forming the outer boundary |
| Ratchet / saw blade | Teeth are asymmetric (one face steep, one gradual) |

**Gear Animation**: Gears are mechanically defined by **mesh rotation** — adjacent gears rotate in opposite directions at rates determined by their tooth count ratio. Serrated badges can spin while their interior symbol remains fixed. Dotted ring badges can animate their dots as **orbital markers** — dots shift position around the ring per frame. Ratchet/saw blade variants should only rotate in one direction (the asymmetric tooth direction), reinforcing the mechanical metaphor.

---

### 4.7 Enclosure and Frame Family

Contain or define space. Prefer implication over completion.

| Variant | Description |
|---|---|
| 4-corner bracket | L-shapes at all four corners; strongest containment |
| 2-corner bracket | Diagonal pair only (top-left + bottom-right, or opposite) |
| Nested squares | Inner square, optionally offset or rotated |
| Rounded rectangle | Pill-like frame; softer than hard rectangle |
| Square with notch | One or more corners cut diagonally |
| Hexagonal frame | Six-sided container |
| Concentric nested frames | Shrinking frames suggest recession |

**Frame Animation**: Corner brackets can **snap** — animate between expanded (wide) and contracted (tight) positions over several frames. A single bracket arm can **crawl** — the bracket travels along the edge of the frame. Nested squares can rotate at different rates (**concentric spin**). Hexagonal frames can **breathe** — subtle scale oscillation. The most powerful frame animation is **assembly** — brackets or frame segments fly in from off-canvas and lock into position frame by frame.

---

### 4.8 Grid, Pattern, and Texture Family

A broad family covering all repeated-element fills, structural grids, and surface texture patterns. These serve as backgrounds, fill regions, standalone compositions, or internal detail within other elements.

**Dot Grids**

| Variant | Description |
|---|---|
| Regular dot grid | Uniform rows and columns |
| Dot arc / spiral | Dots arranged along a curve; suggests a scan or orbit |
| Dot ring | Circle of equally-spaced dots |
| Dot cluster | Irregular grouping suggesting a data set |
| Dot-size gradient | Dots decrease in size from center outward or along a path |

**Block and Pixel Grids**

| Variant | Description |
|---|---|
| Pixel block grid | Small filled squares in grid, some filled/some empty; binary state visualization |
| Checkerboard | Alternating filled/empty squares; can be regular or with missing cells |
| Dither pattern | Ordered dither (Bayer matrix), halftone dots, or Floyd-Steinberg-style noise — suggests image data degraded to 1-bit |
| ASCII / ANSI block texture | Characters from the CP437 set (░ ▒ ▓ █ ▄ ▀ ▌ ▐) or their visual equivalents used as fill density levels |
| Crosshatch / hash grid | Overlapping diagonal or perpendicular line sets creating a mesh fill |

**Line Grids**

| Variant | Description |
|---|---|
| Parallel horizontal lines | Uniform spacing; suggests scan lines, register display, or redacted text |
| Parallel vertical lines | Same, rotated; suggests bar data or frequency |
| Diagonal parallel lines | Hatching at 45° or other angle; industrial/hazard feel |
| Chevron field | Repeated V or arrow shapes in rows; directional, suggests motion or warning |
| Herringbone / zigzag | Interlocking chevrons; structural pattern |
| Concentric line grid | Lines radiating from center or forming concentric shapes (circles, squares) |

**X and Cross Grids**

| Variant | Description |
|---|---|
| X-pattern grid | Repeating X marks in regular array; suggests deletion, redaction, or "no data" |
| Plus-pattern grid | Repeating + marks; suggests crosshair field or calibration |
| Mixed symbol grid | Regular array of mixed small symbols (dots, crosses, dashes); suggests encoded data |

**Pattern Animation**: Dot grids can animate with **wave displacement** — dots shift position in a sine pattern that propagates across the grid per frame. Dither patterns animate with **crawl** — the checkerboard shifts by one cell per frame, creating a marching texture. Block shade levels can **pulse** — cycling through density levels (░ → ▒ → ▓ → ▒ → ░) over several frames. Crosshatch angles can **rotate** — the hatch direction changes incrementally per frame. ASCII block textures can cycle characters through a sequence per frame, creating a **character animation** effect where the texture appears to shimmer or process.

---

### 4.9 Organic / Biomorphic Family

Forms that are non-geometric in origin — curves, blobs, cellular shapes. Use sparingly within HUD mode; primary in glyph mode.

| Variant | Description |
|---|---|
| Blob pair | Two rounded lobes joined at a narrow connection |
| Cellular cluster | 4–7 circles packed tightly together like cells |
| Molecule chain | Small circles connected by short lines in a branching structure |
| Organic ring | Wreath of flame, leaf, or petal shapes arranged in a circle |
| Tendril / spider | Central point with irregular radiating curved arms |
| Rotating claw | Asymmetric organic arms arranged in rotational symmetry |
| Sketch circle | A circle drawn with slight irregularity, as if hand-traced |

**Organic Animation**: Organic forms are the most amenable to **breathing** animation — subtle scale pulses that feel biological. Tendril/spider arms can **sway** — each arm's endpoint shifts slightly per frame in a waving motion. Cellular clusters can animate **division** — cells split and merge over multiple frames. The rotating claw variant is named for its implied rotation — it should animate as a **slow vortex spin**. Sketch circles can **redraw** — each frame traces the circle with slightly different irregularity, creating a hand-drawn vibration effect.

---

### 4.10 Barcode and Machine-Readable Code Family

Patterns that reference machine-readable encoding systems. These are among the most "authentically functional-looking" elements in the vocabulary — they resemble real data artifacts, which strengthens the diagnostic/operational fiction.

| Variant | Description |
|---|---|
| 1D barcode (linear) | Vertical bars of varying width and spacing; classic UPC/Code128 feel. Can be truncated, partial, or full-width. |
| 1D barcode with text | Barcode with a numeric string printed below (OCR-B style); deepens the "real artifact" illusion |
| 2D barcode (data matrix) | Square grid of filled/empty cells; smaller and denser than QR. Feels like encrypted data. |
| QR code block | Square QR-style grid with position markers (the three corner squares). Can be partial, cropped, or complete. |
| Corrupted barcode | Any barcode variant with missing bars, shifted columns, noise pixels, or interrupted scan lines — suggests damaged or unreadable data |
| Corrupted QR | QR grid with missing cells, smeared regions, or overlapping fragments — data that *was* there but can no longer be parsed |
| Barcode fragment | A cropped slice of a barcode — only a few bars visible, as if clipped by a panel edge or data truncation |
| Stacked barcode | Multiple barcode rows stacked vertically (PDF417-style); suggests dense encoded payload |

**Usage notes:**
- Barcodes at the micro end are detailed and "plausibly scannable." At the macro end, they reduce to abstract rhythm — a few bold bars with no pretense of encoding.
- Corrupted variants are powerful at mid-scale: recognizable enough to read as "barcode" but clearly non-functional.
- QR position markers (the three corner squares) are iconic enough to use as standalone elements detached from a full grid.

**Barcode Animation**: 1D barcodes can animate with **scan sweep** — a bright indicator line (drawn as a slightly bolder horizontal stroke) sweeps vertically across the barcode per frame. Corrupted barcodes can animate their corruption — bars that are present in one frame are missing in the next (**dropout flicker**). QR codes can animate **progressive encoding** — cells fill in one-by-one across frames as if data is being written. Stacked barcodes can scroll vertically like a **data feed**.

---

### 4.11 Warning and Hazard Iconography Family

Symbols drawn from industrial safety, electrical hazard, biohazard, radiation, and system warning conventions. These are not literal safety labels — they are *aestheticized references* to the visual language of danger and caution.

| Variant | Description |
|---|---|
| Triangle with exclamation | The universal "caution" sign. Can be outlined or filled. |
| Triangle with inner symbol | Hazard triangle containing lightning bolt, skull, flame, or abstract glyph |
| Radiation trefoil | Three-bladed fan in a circle; the classic radiation symbol. Strong radial form. |
| Biohazard | The three-lobed biohazard symbol. Interlocking arcs. |
| High voltage lightning bolt | Zigzag bolt, typically within a triangle or circle |
| Skull and crossbones (simplified) | Geometric/minimal skull — circles for eyes, simple jaw shape. Not cartoonish. |
| Chevron warning stripe | Diagonal alternating stripes (typically at 45°); the "caution tape" pattern as a border or fill element |
| Circular prohibition | Circle with diagonal slash across an interior symbol |
| Diamond hazard frame | Rotated square (diamond orientation) containing an inner symbol; DOT/NFPA reference |
| Alert beacon | Concentric arcs radiating from a point; "signal broadcast" or "alarm active" indicator |
| Eye / surveillance icon | Simplified open eye shape; suggests monitoring, observation, awareness |
| Lock / unlock | Padlock in open or closed state; system access indicator |
| Broken link / chain break | A chain or link icon with a gap; suggests severed connection or broken dependency |

**Usage notes:**
- These elements carry strong pre-existing semantic associations. Use that: placing a radiation trefoil inside a status panel makes the panel feel dangerous without any text.
- At micro scale: small warning icons embedded in panels as status indicators.
- At macro scale: a single hazard symbol as the entire composition — bold, immediate, graphic.
- The chevron warning stripe is a versatile *pattern* element — can be used as border decoration on any panel or as fill within a region.

**Warning Animation**: Warning elements are defined by **blink** — the element alternates between present and absent (or full and minimal) on alternating frames. This is the most authentic animation for hazard symbols. The alert beacon animates its concentric arcs as **broadcast rings** — arcs expand outward from the point and disappear when they reach maximum radius, with new arcs spawning at center. Chevron warning stripes can **march** — the stripe pattern shifts by one unit per frame, creating directional flow. The radiation trefoil can rotate slowly (one-third turn per cycle, snapping between its three symmetric positions).

---

### 4.12 Wireframe and Low-Poly Geometry Family

Three-dimensional forms projected to 2D using wireframe or faceted rendering. These suggest computational geometry, 3D modeling, simulation, or spatial data.

| Variant | Description |
|---|---|
| Wireframe cube | The classic: a cube in isometric or perspective projection, all edges visible |
| Wireframe icosahedron / polyhedron | A more complex regular solid — 20 triangular faces, reads as "3D mesh" |
| Wireframe sphere (geodesic) | Sphere approximated by triangular facets rather than lat/long grid lines |
| Low-poly terrain fragment | A small patch of triangulated surface — suggests topographic or mesh data |
| Wireframe torus | Donut form with visible quad or triangle mesh |
| Platonic solid (any) | Tetrahedron, octahedron, dodecahedron — clean, recognizable, geometric |
| Exploded wireframe | A polyhedron with faces separated/offset from center — suggests disassembly or analysis mode |
| Wireframe with hidden lines dashed | Visible edges solid, occluded edges dashed — the classic engineering drawing convention |
| Vertex-and-edge graph | Points connected by lines, not necessarily a closed solid — suggests a node graph or abstract topology |

**Usage notes:**
- At micro scale: small wireframe polyhedra embedded in panels as "3D data preview" indicators.
- At macro scale: a single bold wireframe form filling the composition — the geometry is the statement.
- Hidden-line-dashed treatment adds information density; remove it as the slider moves macro.
- These pair naturally with grid/dot backgrounds for a "simulation viewport" feel.

**Wireframe Animation**: Wireframe objects animate with **tumble rotation** — the 3D projection rotates around one or more axes per frame, showing different faces. Hidden-line-dashed treatment adds to this: lines that were solid become dashed and vice versa as faces rotate into and out of view. Exploded wireframes can animate the **explosion** — faces separate from center over several frames, then reassemble. Vertex-and-edge graphs can animate **edge traversal** — a highlighted edge travels through the graph per frame.

---

## 5. Symmetry Types

Every element belongs to exactly one symmetry class. This is a primary generative parameter.

| Class | Description | Examples |
|---|---|---|
| **Radial N-fold** | Identical arms/sections at `360/N` degree intervals | Stars, gears, asterisks; N = 3, 4, 5, 6, 8 |
| **Bilateral** | Mirror symmetry across one axis (vertical or horizontal) | Hourglass, bowtie, lens |
| **Double bilateral** | Mirror symmetry across both axes | Most panel elements, crosshairs |
| **Rotational (no mirror)** | Rotation symmetry without reflection | Pinwheels, vortex forms, ratchets |
| **Asymmetric** | No symmetry; rare, used for deliberate tension | Branching connectors, single-arm elements |

**Symmetry and Animation**: Rotational N-fold elements animate most naturally with rotation — the element spins at a rate that lands on a symmetric position every N frames (e.g., a 6-fold star completes one arm-to-arm step every 6 frames in a full rotation cycle). Bilateral elements animate with **mirror breathing** — both halves scale simultaneously. Rotational-no-mirror elements (pinwheels) should animate with continuous smooth rotation — they have no preferred rest position. Asymmetric elements animate with **jitter** or **drift** — small random positional offsets per frame suggesting instability.

---

## 6. Composite Element Archetypes

Assembled from the primitives above. These are the *output types* the generator produces.

### 6.1 Status Panel
A HUD panel with text and geometric indicators.
- **Structure**: 4-corner bracket framing a text block
- **Contents**: status label (ALL CAPS) + numeric readout + status string + micro-annotation text
- **Accent element**: one small glyph from the symbol vocabulary inside the panel
- **Mode**: Line / Outline

### 6.2 Targeting Reticle
Centered circular element suggesting aiming or scanning.
- **Structure**: 1–3 concentric rings + crosshair lines (usually with gap at center)
- **Details**: tick marks at cardinal or 45° positions, partial arcs as active-state indicators
- **Annotation**: alphanumeric code nearby, version string at bottom
- **Mode**: Line / Outline

**Reticle Animation**: The targeting reticle is the most animation-rich archetype. Its natural animation combines: **ring rotation** (outer ring spins), **sweep scan** (an arc indicator sweeps around), **crosshair lock** (lines converge then snap wide), and **data update** (annotation text changes per frame). These can layer simultaneously — the outer ring rotates while the crosshair pulses and the annotation ticks through values. A reticle in "acquisition" mode animates convergence; in "tracking" mode it animates steady rotation; in "lost" mode it animates divergence and jitter.

### 6.3 Meter / Gauge
A circular or arc-based dial element.
- **Structure**: Partial arc as track + single pointer tick + numeric value label
- **Variants**: Circular (clock-face), linear (segmented bar), stacked bar
- **Mode**: Line / Outline

**Meter Animation**: Gauges animate through **value sweep** — the data polygon or arc fill changes per frame, showing different values as if monitoring a live signal. Radial bar charts can animate individual bars growing and shrinking. Concentric arc tracks can fill progressively. The radar chart variant animates with **data polygon morphing** — vertices interpolate between value sets across frames. Ring-of-blocks variants can animate their fill pattern — blocks light up in sequence around the ring.

### 6.4 Data Readout Block
Dense text-forward element.
- **Structure**: Rectangular zone (corner brackets or full border) with multiple lines of technical strings
- **Layout**: One dominant value (large) + supporting key-value pairs (small monospace) + optional waveform or bar
- **Mode**: Line / Outline

### 6.5 Node / Network Marker
Topology diagram element.
- **Structure**: Center dot or circle + radiating lines to satellite dots + labels on each satellite
- **Variants**: Star topology, chain topology, branching topology
- **Mode**: Line / Outline

### 6.6 Standalone Glyph
A single icon-weight symbol with no text.
- **Structure**: One primary form from any geometric family
- **Scale**: fills its bounding box
- **Symmetry**: any; radial and bilateral most common
- **Mode**: Solid / Silhouette or Hybrid
- **Accent option**: Cryptic unicode character at center (see Section 10)

**Glyph Animation**: Standalone glyphs at macro scale animate with restraint — **slow rotation**, **subtle breathing**, or **hatch angle shift** (the fill pattern rotates while the outline remains static). Glyph clone layers can animate at different rates. The most dramatic glyph animation is **draw-on** — the outline traces itself path-by-path across frames, appearing to construct the glyph in real time. At deep macro, animation should be minimal: a single slow rotation or scale pulse. The glyph's power is in its stillness.

### 6.7 Globe / Sphere Panel
A sphere representation embedded in a panel context.
- **Structure**: Globe variant (wireframe, striped, eye-globe) + corner brackets + status label
- **Variants**: Front-facing, elliptical projection, hemisphere
- **Mode**: Hybrid (globe in Solid or Line; surrounding panel in Line)

### 6.8 Seal / Badge
A self-contained circular emblem.
- **Structure**: Gear or serrated outer edge + interior symbol + optional text arc
- **Interior**: May contain a 4-point star, crosshair, dot, or small concentric form
- **Mode**: Solid or Hybrid

**Seal Animation**: The seal/badge outer ring rotates while the interior symbol stays fixed — **bezel rotation**. Interior symbols can animate independently (a crosshair that locks, a star that pulses). The double border ring can counter-rotate against the serrated edge. Banner text below can animate as a **text ticker** — characters shift or update per frame.

### 6.9 Error Message / System Alert
A text-forward composition that resembles a terminal error output, system crash dump, or failed process notification.
- **Structure**: Rectangular container (full border or bracket-framed) with monospace text block
- **Contents**: Error code header (e.g., `ERR_0x7F2A`, `SEGFAULT AT 0x00`) + stack-trace-like lines + optional warning icon (triangle, exclamation)
- **Visual markers**: A corrupted barcode fragment along one edge, a blinking-cursor indicator (underscore or block), chevron warning stripe as border accent
- **Text style**: ALL CAPS or mixed-case monospace. Lines may include `>>>`, `//`, `::`, `--` prefixes. Some lines can be "redacted" (replaced with block characters ▓▓▓▓▓▓)
- **Mode**: Line / Outline
- **Scale range**: 0.00–0.35 (deeply text-dependent)

**Error Animation**: Error messages animate with **terminal urgency**: text lines appear one-per-frame (typewriter reveal), progress bars advance, the warning X blinks on alternating frames, and the cursor block blinks. Corrupted text can animate character substitution — random characters replace themselves per frame before settling. Hex dump values can increment. The most effective error animation is the **cascade** — each element appears in sequence with staggered timing, building the error report line by line.

### 6.10 Terminal / Console Block
A composition that resembles a command-line interface fragment or cyberdeck readout.
- **Structure**: Full rectangular border (unlike most elements, terminals have complete borders — the enclosure IS the terminal window)
- **Contents**: Monospace text lines with a mix of commands, output, and ASCII/ANSI decorative elements. May include a prompt character (`>`, `$`, `#`, `_`)
- **Visual markers**: Cursor block, scan lines (faint horizontal line pattern as overlay texture), optional title bar with dot/circle window controls
- **ASCII art region**: A small area within the terminal that contains an ASCII-rendered shape — a waveform, a histogram, a box-drawing diagram, or a miniature glyph composed of text characters
- **Mode**: Line / Outline
- **Scale range**: 0.00–0.30 (the most micro-locked archetype alongside Data Readout)

**Terminal Animation**: The terminal is the most text-animation-dense archetype. It combines **terminal scroll** (new lines appear at bottom, old lines shift up), **typewriter reveal** (characters appear left-to-right), **cursor blink** (cursor block alternates present/absent), and **command execution** (a prompt appears, a command types out, output scrolls in). The scroll rate, type speed, and blink rate should all be independent parameters. Line numbers increment. The highlighted line can shift position per frame.

### 6.11 Encoded Data Block
A composition built around barcode, QR code, or data-matrix elements.
- **Structure**: One or more machine-readable code elements (1D barcode, 2D grid, QR fragment) arranged within a panel frame
- **Contents**: Barcode + numeric label string beneath + optional status text (`SCAN`, `VERIFIED`, `UNREADABLE`)
- **Corruption option**: Barcode may be partially damaged, with missing bars or noisy cells — suggesting data that existed but is no longer intact
- **Mode**: Line or Hybrid
- **Scale range**: 0.00–0.60 (a detailed barcode is micro; a few bold abstract bars can push toward mid-macro)

### 6.12 Dataviz Fragment
A composition that resembles a clipped excerpt from a data visualization — a chart, graph, or plot that has been cropped, decontextualized, and rendered as a standalone graphic element.
- **Structure**: One primary data representation + optional axis lines + optional sparse labels
- **Variants**: Radial/polar chart segment, scatter plot fragment, histogram bars, waveform/oscilloscope trace, network graph excerpt, heat map tile, candlestick bars
- **Key principle**: The visualization is *incomplete* — cropped edges, missing labels, truncated axes. It is a fragment of a larger dashboard, not a self-contained chart. This incompleteness is what makes it a micrographic rather than an infographic.
- **Mode**: Line or Hybrid
- **Scale range**: 0.05–0.55 (data density decreases; at mid: a single bold waveform trace or 3 histogram bars)

**Dataviz Animation**: Data visualizations are inherently temporal — they animate by **updating their data**. Waveforms scroll (new samples enter right, old samples exit left). Bar charts cycle through datasets. Scatter plots can animate point positions drifting. Polar charts morph between value sets. The most natural dataviz animation is the **live feed** — data appears to stream in real time, with the visualization responding to changing inputs. Axis lines and grid backgrounds remain static while data elements move.

---

## 7. Compositional Rules

These apply regardless of element type.

1. **Corner brackets over full borders.** Containment is implied, not solid. Full rectangles feel heavy; corners feel precise and provisional.

2. **Asymmetric within symmetric.** Radially symmetric shells contain asymmetrically placed internal details — an off-center pointer, a label in one quadrant only.

3. **Information at multiple scales.** Every element has a dominant readable feature and a secondary layer of fine detail. This creates depth without literal 3D.

4. **Nothing unlabeled (in HUD mode).** Every shape has an associated text string. Labels can be cryptic but they exist. Glyph mode has no labels — the shape is the entire message.

5. **Incompleteness signals activity.** Full closed forms = static/resolved. Arcs, gaps, partial fills, single open corners = live/dynamic/processing.

6. **Dot patterns instead of fills (in Line mode).** Where a solid fill might appear, use a dot matrix instead. Preserves the line-art character.

7. **Codes as decoration.** Hex strings, ID numbers, file paths, and coordinates are valid design elements regardless of literal meaning.

8. **Radial and rectilinear in tension.** Each composition mixes at least one circular/radial element with at least one rectangular/gridded element. Pure circles or pure rectangles alone feel unresolved.

9. **Version numbers anchor elements.** A version string (V.X.X.XX) grounds a HUD panel and adds verisimilitude.

10. **Status states are discrete, never continuous.** Elements communicate one of: ACTIVE / OFFLINE / STANDBY / ERROR / CALIBRATING. No analog states.

11. **Scale contrast within a single element.** Pair a large primary form with very small secondary forms or annotation text. Never two elements of equal visual weight competing.

12. **Rotation implies life; translation implies data.** A spinning/rotational form is an organism or active process. A horizontally arranged sequence is a data structure or measurement.

13. **Animation speed hierarchy.** In animated compositions, larger/primary elements animate slowly, secondary elements animate at medium speed, and tertiary elements (greebles, ticks, annotations) animate fastest. This creates a layered temporal rhythm: the sigil turns once per cycle, the concentric rings turn twice, and the tick marks orbit four times. The eye is drawn to the slowest element — which should be the most important.

14. **Stillness is powerful.** Not everything should move. In any animated composition, at least 30% of elements should remain completely static. Static elements anchor the composition and provide reference points for the moving elements. A ring that doesn't rotate makes the rotating ring next to it more dramatic.

15. **Seamless loops over transitions.** All animation should be designed as perfect loops. The last frame transitions seamlessly back to the first frame. Avoid linear progressions that must "reset" — prefer oscillations, rotations, and cycles that have no visible start or end point.

---

## 8. Border System

Borders are **optional containers** placed around discrete blocks of generated graphics. They are toggled on/off globally. When enabled, every generated composition receives a border; the border style is selected per-composition from the vocabulary below.

Borders are not simple rectangles. They are stylized structural frames drawn from the same design language as the interior elements. A border should feel like part of the system's interface chrome — a UI bounding box, a data region delimiter, or a viewport edge — not a decorative picture frame.

---

### 8.1 Border Style Vocabulary

#### Corner-Only Borders
The interior is implied, not enclosed. These are the most characteristic border type in the system.

| Style | Description |
|---|---|
| **4-corner bracket** | L-shapes at all four corners. The signature containment form. Arm length is variable — short stubs feel tighter, long arms feel more structural. |
| **2-corner bracket (diagonal)** | Only top-left + bottom-right, or top-right + bottom-left. Suggests a region that is partially defined or in the process of being selected. |
| **Corner bracket with ticks** | L-shaped corners with perpendicular tick marks along the arms. Calibration/measurement feel. |
| **Corner bracket with dots** | Small filled circles at each corner vertex instead of or in addition to the L-shape. Node/waypoint feel. |
| **Corner notch** | Instead of extending inward, the corner is a small diagonal cut (chamfer) on an otherwise absent border — only the notch exists. Minimal. |

#### Broken Path Borders
The border path exists but is interrupted. Gaps, breaks, and discontinuities suggest data transmission, incomplete rendering, or a system still assembling its interface.

| Style | Description |
|---|---|
| **Dashed rectangle** | Full rectangular path drawn as dashes with regular gaps. Dash length and gap ratio are variable. |
| **Gapped sides** | Each side is a single line segment that stops short of the corners — four floating lines with open corners. The inverse of corner-only. |
| **Interrupted path** | A continuous rectangular path with 1–3 random-length gaps on different sides, as if signal was lost mid-draw. |
| **Morse border** | Dashes and dots of varying length along the path — suggests encoded data in the border itself. |
| **Decay border** | The border starts solid on one side and progressively breaks apart toward the opposite side — segments shrink, gaps widen. Entropy in the frame. |

#### Manhattan Path Borders
Borders that follow only horizontal and vertical segments (no diagonals) but deviate from a simple rectangle. The path routes around, steps in, or detours — suggesting a circuit trace, PCB routing, or a UI region with non-rectangular territory.

| Style | Description |
|---|---|
| **Stepped border** | One or more sides have a rectangular step/notch inward or outward, as if the border routes around an obstacle or accommodates a sub-panel. |
| **Tabbed border** | One side has a protruding rectangular tab (like a file folder tab or a UI panel title bar zone). The tab may contain a label. |
| **Inset corner** | One or more corners step inward to create an L-shaped indentation rather than a sharp right angle — suggests the frame is avoiding a region. |
| **Asymmetric Manhattan** | Each side is at a different inset depth — the border is rectangular but not centered on the content. Off-center framing. |
| **Circuit trace border** | The path has perpendicular stubs, T-junctions, or small dead-end branches protruding outward — suggests PCB traces or data bus lines extending from the frame. |

#### Technical / HUD Borders
Borders that carry information or suggest interface function.

| Style | Description |
|---|---|
| **Ruled border** | A continuous line with tick marks at regular intervals along one or more sides — a ruler/scale built into the frame. |
| **Indexed border** | Tick marks with tiny numeric labels at intervals (like axis markings on a chart). |
| **Crosshair border** | The midpoint of each side has a small crosshair or tick protruding inward — suggests alignment guides or centering marks. |
| **Bracket-and-line** | Corners are L-brackets, but the sides also have a thin connecting line inset from the bracket arms — a double-layer frame. |
| **Header border** | Top side is a full line (or double line); remaining three sides are corner brackets only. Suggests a panel with a title bar. |
| **Status border** | One side (typically bottom) includes an inline text label rendered as part of the border path — e.g., the bottom line has a gap where a small status string sits: `── ACTIVE ──`. |

#### Ornamental / Signal Borders
Borders that reference the pattern and glyph vocabulary.

| Style | Description |
|---|---|
| **Chevron border** | One or more sides rendered as a row of small chevrons (< < < < or > > > >) instead of a line — directional, suggests data flow direction. |
| **Dot border** | The frame is drawn as a series of evenly-spaced dots rather than a line. |
| **Barcode border** | One side is rendered as a short 1D barcode strip — the frame itself encodes (or pretends to encode) data. |
| **Warning stripe border** | One or more sides carry the diagonal chevron/hazard stripe pattern. |
| **Glyph-terminated border** | The line segments of the border terminate in small glyphs (asterisks, diamonds, circles, arrows) rather than simply ending. |

---

### 8.2 Border Behavior Rules

1. **Toggle, not per-element.** Borders are on or off globally. When on, every discrete composition block gets one. The *style* varies per block; the *presence* is uniform.

2. **The border is not the element.** The border wraps the generated content with consistent padding/margin. It does not overlap, intersect, or compete with interior geometry. Clear spatial separation between border and content.

3. **Stylistic coherence.** The border style should be compatible with the interior's micro-macro position and rendering mode:
   - Micro compositions (dense, Line mode): corner brackets, ruled borders, status borders, circuit traces
   - Mid compositions: broken paths, Manhattan paths, header borders
   - Macro compositions (bold, Solid mode): minimal corner dots, gapped sides, single-line with glyph terminals

4. **Never a plain rectangle.** A simple closed rectangle or rounded rectangle is explicitly excluded from the border vocabulary. Every border must have at least one point of structural interest — a gap, a step, a tick, a deviation.

5. **Consistent stroke weight.** The border stroke weight matches or is slightly lighter than the interior element's primary stroke weight. The border is structural chrome, not a competing visual element.

6. **Borders can carry information.** Status borders, indexed borders, and barcode borders blur the line between frame and content — the border becomes a secondary data layer. This is encouraged at the micro end of the scale.

7. **Padding scales with micro-macro.** At the micro end, padding between border and content is tight — the border hugs the content like a UI panel edge. At the macro end, padding increases — the border is a distant perimeter around a centered glyph, creating negative space.

---

### 8.3 Border Micro-Macro Grading

| Style Category | Viable Range | Notes |
|---|---|---|
| Corner bracket variants | 0.00–0.75 | The most versatile. At micro: tight, with ticks. At macro: wide-set, minimal stubs. |
| Broken path variants | 0.10–0.70 | Gaps add visual interest at mid-range. At deep micro, too much border noise competes with content. |
| Manhattan path variants | 0.00–0.50 | Steps and deviations are detail. At macro, the border should be simpler than the content. |
| Technical / HUD variants | 0.00–0.45 | Ruled borders, indexed borders carry information density. Micro territory. |
| Ornamental / signal variants | 0.05–0.65 | Chevron and dot borders are simple enough for mid-range. Barcode border is micro-only (0.00–0.35). |
| Gapped sides | 0.30–0.85 | The inverse of corner-only — works well at macro because it's minimal. |
| Corner dots only | 0.50–0.90 | Four dots marking the extent. Maximum restraint. Macro native. |
| Glyph-terminated lines | 0.20–0.70 | Small terminal glyphs add character without density. Mid-range sweet spot. |

At **deep macro (0.85–1.00)**, borders should be the absolute minimum if enabled at all: four corner dots, or two short corner stubs. The content is the entire composition; the border is barely a whisper.

**Border Animation**: Borders can animate with **edge crawl** — a small bright marker (a bold dot or short dash) travels along the border perimeter, completing one circuit per animation cycle. Corner brackets can **breathe** — subtly expanding and contracting. Dashed borders can animate their **dash offset** — the dash pattern slides along the border path per frame. The most dramatic border animation is **assembly/disassembly** — border segments slide in from off-canvas and lock into position sequentially, then slide back out before the loop repeats. Status borders can animate their inline text (cycling between states). Morse borders can animate their encoding — dots and dashes shift pattern per frame.

---

## 9. Aesthetic Treatments

Treatments are **visual modifiers** applied on top of any element or composition. They are not element types — they are lenses through which elements are rendered. A treatment affects how geometry is drawn, distorted, or contextualized. Multiple treatments can be combined, but restraint is critical — one primary treatment per composition.

---

### 8.1 Glitch Treatment

Simulates data corruption, signal interference, or rendering failure. The geometry *was* clean but something disrupted it.

| Technique | Description |
|---|---|
| Horizontal slice displacement | Horizontal bands of the element are shifted left or right by a few pixels, as if a scan line desynchronized |
| Duplicate offset | The entire element is drawn 2–3 times with slight offset and reduced opacity, as if a frame buffer failed to clear |
| Missing scanlines | Horizontal slices are simply absent — gaps where data should be |
| Noise pixel scatter | Random small squares or dots clustered near edges or within fills, as if encoding noise leaked in |
| Truncation | Part of the element is cleanly cut off at an arbitrary boundary — data that stopped transmitting mid-stream |
| Fragmentation | The element is broken into rectangular shards that are slightly displaced from their correct positions |

**Scale interaction**: Glitch treatment is most effective in the 0.15–0.65 range. At deep micro, the detail is already so dense that glitch reads as noise. At deep macro, the bold silhouette is too simple to glitch convincingly — there's nothing to disrupt.

---

### 8.2 Terminal / Cyberdeck Treatment

Makes any element look as though it is being rendered on a CRT monitor, a terminal emulator, or a hacked-together cyberdeck display.

| Technique | Description |
|---|---|
| Scan line overlay | Faint horizontal lines across the entire composition at regular intervals |
| Phosphor glow | Slight thickening or doubling of bright lines, as if the phosphor bleeds |
| Monospace text rendering | All text uses strict monospace grid alignment; characters snap to a character-cell grid |
| Cursor element | A blinking block cursor (█) or underscore appears somewhere in the composition, implying live input |
| Screen border / bezel | A subtle outer frame suggesting the edge of a physical display |
| Green/amber monochrome | If color is applied, limit to a single green or amber tone — the CRT palette |
| ASCII rendering of shapes | Geometric elements can be approximated using box-drawing characters (┌ ─ ┐ │ └ ┘) or block elements (█ ▄ ▀), as if the display can only render text characters |

**Scale interaction**: Terminal treatment is micro-biased (0.00–0.45). The character-grid constraint adds density. At macro scale, a single bold shape has no reason to look like a terminal.

---

### 8.3 Corruption / Decay Treatment

Suggests a system or data structure that is degrading over time. Where glitch is sudden disruption, corruption is slow entropy.

| Technique | Description |
|---|---|
| Missing segments | Parts of lines or arcs are simply absent, as if data was lost — not displaced, just gone |
| Character substitution | Labels or text strings contain replacement characters (▯ ⍰ ░) where letters should be |
| Progressive degradation | One region of the element is clean; another region of the same element is increasingly broken — a gradient of decay across space |
| Noise border | The edges of filled shapes are rough, eroded, or pixelated rather than clean |
| Redaction blocks | Sections of text replaced with solid block fills (▓▓▓▓▓) — data that has been deliberately or accidentally obscured |
| Partial barcode | A barcode element that is half-present, half-dissolved |

**Scale interaction**: Corruption works across the full range. At micro: corrupted text strings, degraded annotation layers. At macro: a bold glyph with an eroded edge or a missing quadrant.

---

### 8.4 Algorithm / Computational Treatment

Makes the element look as though it is the *output of a process* — not a designed graphic but something a system generated. Suggests procedural generation, mathematical functions, or iterative computation.

| Technique | Description |
|---|---|
| Visible iteration | An element drawn at multiple stages of its construction — e.g., a spiral shown at 3 different growth steps overlaid |
| Parametric variation | The same base form repeated with one parameter varied linearly (size, rotation, spacing) — suggests a parameter sweep |
| Recursive subdivision | A shape subdivided into smaller versions of itself (fractal-like), carried to 2–3 levels |
| Function plot | A visible sine wave, Lissajous curve, Perlin noise trace, or other mathematical curve used as a structural element |
| Grid sampling | Elements placed at grid intersections but with some positions empty or modified — suggests a function sampled at discrete points |
| Seed / hash display | A numeric string presented as a "seed" value (e.g., `SEED: 7f2a9b03`) — implies the composition is one of many possible outputs |

**Scale interaction**: Algorithm treatment spans 0.10–0.80. At micro: parametric variation tables, iteration traces, seed labels. At macro: a single bold form that is clearly one output of a parametric series.

---

### 8.5 Hacking / Intrusion Treatment

Suggests unauthorized access, system exploitation, or security breach. The composition looks like something that shouldn't be visible — a layer that was meant to be hidden.

| Technique | Description |
|---|---|
| Exposed structure | Elements that look like they belong to a layer beneath the UI — raw memory addresses, hex dumps, register contents |
| Override indicators | Text strings like `ROOT`, `BYPASS`, `ACCESS GRANTED`, `SUDO`, `INJECT` used as status labels |
| Layered transparency | Two compositions overlaid as if one is bleeding through from behind the other — a hidden layer made visible |
| Bracket intrusion | Corner brackets or frame elements that extend beyond their expected boundaries, as if a contained element is breaking out |
| Blinking / alert state | Elements marked with double-border or exclamation markers suggesting an active alarm or breach notification |
| Memory dump fragment | A short block of hexadecimal values arranged in a grid (e.g., `4F 2A 7B 00 FF D1 3C 88`) — suggests raw data exposure |

**Scale interaction**: Hacking treatment is micro-biased (0.00–0.50). The effect depends on text, layering, and information density. At macro, it reduces to a single override glyph or breach symbol.

**Treatment Animation**: Aesthetic treatments become especially powerful when animated:

- **Glitch**: Slice displacements change position and offset per frame — horizontal bands jitter erratically. Dropout regions appear and disappear randomly. The glitch should feel **unstable** — no two consecutive frames have the same disruption pattern.
- **Terminal**: Scan lines sweep downward. The cursor blinks. Phosphor glow can pulse (thicker/thinner strokes on alternating frames).
- **Corruption**: Decay progresses across frames — clean regions degrade over time, missing segments grow. The corruption **spreads** directionally across the composition over the course of the loop, then resets.
- **Algorithm**: Iteration becomes literal — each frame shows the next step of a parametric process. A spiral grows by one turn per frame. A recursive subdivision adds one level per frame.
- **Hacking**: Override labels flash between frames. Bracket intrusion elements extend further with each frame. Memory dump values scroll. The "breach" intensifies over the loop, then snaps back to a clean state.

---

## 10. Cryptic Unicode Accent Characters (and Missing Data Glyphs)

These characters replace CJK characters as high-contrast anchor glyphs. They evoke system errors, encoding failure, and data that exists at a layer the interface wasn't designed to expose. The viewer reads them as a machine's internal language leaking through.

### Character Categories

**Ballot/Box symbols** — failed checkboxes, form field states
`☒ ☐ ☑ ⊠ ⊡ ⊟ ⊞`

**Technical / APL operators** — alien machine language
`⌬ ⌖ ⌗ ⍉ ⍋ ⍒ ⍔ ⍜ ⍞ ⍟ ⍰ ⍾ ⎔ ⎕`

**Geometric shapes used out of context**
`◈ ◉ ◎ ◬ ◭ ◮ ⬡ ⬢ ⬣ ⬟`

**Broken logic / directional anomalies**
`⇌ ⇎ ⇏ ⇔ ⇲ ⇱ ⇵ ⟁ ⟂ ⟃`

**Corrupted memory / block patterns**
`⣿ ⣾ ⣽ ⢿ ░ ▒ ▓ ▤ ▦ ▧ ▨`

**Literal missing-data surrogates**
`▯ ▮ ▰ ▱ ⍰ ␣ ␦`

**Missing file / character / data icons** — the visual language of "this should be here but isn't"
`⍰` (APL quad question), `�` (replacement character), `☒` (ballot X), `⊘` (circled slash / null), `∅` (empty set)
These are conceptually powerful: they are symbols whose meaning is *the absence of the thing they represent*. A missing-character glyph inside a panel says "this system tried to display something and failed."

**Box-drawing fragments used as broken structure**
`┌ ─ ┐ │ └ ┘ ├ ┤ ┬ ┴ ┼`
Not used to draw actual boxes (that's the terminal treatment) — used as *characters* that reference structure without forming it. A `┼` in the middle of a label reads as a broken intersection.

### Usage Rules
1. **One dominant glyph per element** — sized up (2–4x body text) to function as an icon
2. **Prefer characters with strong geometric structure** — must hold visual weight as a shape, not read as punctuation
3. **Pair with an oblique nearby label**: `[INDEX] DECIPHERING...`, `ERROR // ID: 292C`, `FILE NOT FOUND`, `DATA ∅` — the text implies the glyph has a referent
4. **Never cluster more than 2–3 together** — power is in isolation
5. **Can appear in "degraded" context** — near connector lines that trail off, or within panels marked OFFLINE / CORRUPTED
6. **Missing-data glyphs can replace expected content** — where a label would normally show a value, show `⍰` or `▯▯▯` instead — the system tried and failed to retrieve the data

---

## 11. Typography System (HUD Mode Only)

### Typefaces

Fonts are organized into two tiers loaded from separate directories:

#### Micro Fonts (`/fonts/micro/`)
Single-line (single-stroke) SVG fonts. Glyphs are open paths rendered as strokes, not filled outlines. This produces true single-toolpath lettering, ideal for pen plotter output — the pen draws each character in one continuous or near-continuous motion.

Initial set:
| Font | Character | Usage |
|---|---|---|
| **EMSOsmotron** | Geometric, constructed, futuristic | Primary HUD labels, status text, identifiers |
| **TwinSans** | Clean, neutral, technical | Secondary labels, value readouts, coordinates |
| **EMSReadability** | Clear, functional, higher legibility | Dense annotation text, data readouts, terminal output |
| **EMSSpaceRocks** | Angular, alien, decorative | Accent text, error codes, warning labels, stylized headers |

These fonts are used at all scale positions and constitute the primary typographic voice of the system.

#### Macro Fonts (`/fonts/macro/`)
Standard outline display fonts used only at macro scale positions (scale >= 0.6) and in limited quantities (1–2 instances per composition maximum). These provide bold, graphic typographic presence when text appears as a dominant visual element rather than annotation.

- Macro font glyphs are rendered as outline strokes (the path traces the letter boundary) for pen plotter compatibility
- User-populated directory — the user adds their own preferred display fonts
- If empty, the generator falls back to micro fonts at all scales
- At mid scale (0.4–0.6): may appear at ~10% probability, 1 instance only
- At deep macro (0.8–1.0): preferred option for any text that appears

The two tiers create a typographic hierarchy that mirrors the micro-to-macro visual shift: dense, functional, single-stroke lettering at micro; bold, graphic, outline lettering at macro.

### Text Roles
| Role | Format | Example |
|---|---|---|
| Status label | ALL CAPS, left-aligned | `ACTIVE`, `OFFLINE`, `CALIBRATED` |
| Value readout | Numeric with inline unit | `47%`, `12V`, `38°C`, `1.8A` |
| Identifier / code | Alphanumeric, hex-adjacent | `EOH-9520`, `ID: 292C` |
| Version string | `V` + dotted number | `V6.0.21`, `V.4.8.31` |
| Timestamp / coordinate | Colon- or dot-separated | `12:04:15`, `02.44`, `119.09` |
| System path / filename | Path-like string | `LOG_ARX.TXT`, `DATA.843.XML` |
| Year as element | 4-digit year, sized up | `2026` |
| Annotation texture | Tiny, near-illegible strings | suggests depth, not meant to be read |

### Typography Layout Rules
- Labels associate with geometric elements via proximity or a connector line
- Mixed scales within one element: one large display number + many small annotations
- Text rarely centered; favors left-align with hanging brackets or right-align flush to edge
- Bracket labels: `[DATACLUSTER]`, `(ANALOG)`, `//SYSTEM`, `<CORE_FUNCTION>`

---

## 12. The Micro–Macro Scale

The application exposes a single primary control: a continuous slider from **Micro** (0.0) to **Macro** (1.0). This axis governs which elements are eligible for generation, how they render, how many appear in a composition, and how much internal detail they carry.

This is not simply a "detail level" control. It represents a conceptual shift from **dense diagnostic readout** at the micro end to **bold symbolic mark** at the macro end. The metaphor: micro is the machine's full telemetry stream; macro is the single glyph stamped on its hull.

---

### 10.1 Scale Zones

The continuous 0.0–1.0 range divides into five named zones for discussion, though the slider interpolates smoothly.

| Zone | Range | Character |
|---|---|---|
| **Deep Micro** | 0.00–0.20 | Maximum density. Multi-panel compositions with subdivisions, full annotation layers, tick marks on everything. The "cockpit instrument cluster." |
| **Micro** | 0.20–0.40 | Single-panel or small-cluster compositions. Text present but not overwhelming. Greebles and secondary detail active. The "individual gauge." |
| **Mid** | 0.40–0.60 | Transitional zone. Elements simplify — text drops to labels only, detail reduces to structural features. The composition might be one element with modest annotation, or a clean glyph with a single label. The "icon with context." |
| **Macro** | 0.60–0.80 | Single element dominates. No text. Internal detail minimal — maybe one concentric ring or a few radial ticks, but no greebles. Form reads as silhouette. The "system icon." |
| **Deep Macro** | 0.80–1.00 | Purest form. One element, zero internal detail, monolithic shape. No tick marks, no subdivision, no annotation. The form *is* the entire statement. The "sigil." |

---

### 10.2 Global Behaviors Along the Scale

These properties change continuously as the slider moves from 0.0 to 1.0:

| Property | Micro End (0.0) | Macro End (1.0) |
|---|---|---|
| **Element count per composition** | 3–8 elements in subdivided grid | 1 element, centered |
| **Rendering mode** | Line / Outline | Solid / Silhouette |
| **Stroke weight** | Hairline (0.5–1px) | Heavy (3–6px) or filled |
| **Text presence** | Dense (6+ strings) | None |
| **Text scale** | Tiny annotation + medium labels | N/A |
| **Annotation texture** | Full (micro-text layer active) | Absent |
| **Corner bracket framing** | Present on most elements | Absent |
| **Internal subdivision** | Deep (nested shapes within shapes) | None (monolithic form) |
| **Tick marks / calibration marks** | Abundant | Absent |
| **Dot matrix fill** | Available as texture | Absent |
| **Concentric ring count** | 3–5 | 0–1 |
| **Crosshair / gap detail** | Full crosshairs with gaps and ticks | No crosshairs |
| **Connector lines** | Present with arrows and labels | Absent |
| **Unicode accent characters** | 1–2 per panel, at body text scale | Absent (or 1 character IS the element) |
| **Version strings** | Present | Absent |
| **Organic character** | Low (pure geometry) | Available (full range) |
| **Color mode** | Dark mode (HUD) preferred | Light mode (glyph) preferred |
| **Aesthetic treatments available** | All (glitch, terminal, corruption, algorithm, hacking) | Corruption only (others require density) |
| **Barcode / QR presence** | Detailed, plausibly real | Absent or abstracted to rhythm |
| **Warning icons** | Small, embedded in panels | Large, standalone, full silhouette |
| **Wireframe geometry** | Detailed mesh with hidden lines | Simple solid (tetrahedron, cube) or absent |
| **Grid / pattern fills** | Active (dither, dot grid, checkerboard, chevron) | Absent |

Note: "preferred" does not mean "required" — it means the mode is more natural at that end. The user may override.

**Scale and Animation**: The micro-macro position affects animation density and speed:

| Scale Zone | Animation Character |
|---|---|
| **Deep Micro** | Maximum animation density. Many elements animate simultaneously: counters tick, rings spin, text scrolls, data updates. Short cycle lengths (4-8 frames). Feels like a live operational display. |
| **Micro** | Moderate density. Primary element animates with 1-2 secondary animations. 8-16 frame cycles. Feels like a focused instrument reading. |
| **Mid** | Selective animation. One primary motion (rotation or sweep) with minimal secondary motion. 12-24 frame cycles. Feels like a standby indicator. |
| **Macro** | Minimal animation. Slow rotation, subtle breathing, or hatch angle shift only. 16-32 frame cycles. Feels like a monument slowly turning. |
| **Deep Macro** | Near-stillness. One very slow motion — a barely perceptible rotation or scale pulse. 24-48 frame cycles. The sigil is almost still; its motion is meditative, not operational. |

The total path count per frame should remain within plottable limits. Animation at deep micro may require simplifying the base composition to keep frame generation time practical.

---

### 10.3 Element Family Grading

Each geometric family has a **viable range** on the scale — the zone where it can be generated. Outside that range, the family is filtered out. Within the range, the element transforms as described.

#### Circle Family

| Variant | Viable Range | Notes |
|---|---|---|
| Plain ring | 0.00–0.70 | At micro: container for other elements. At macro: too simple alone, filtered out. |
| Concentric rings | 0.00–0.60 | Ring count decreases toward mid. Filtered at macro — too detailed for silhouette. |
| Circle with crosshair | 0.00–0.50 | Crosshair is inherently a detail/subdivision element. |
| Circle with gap crosshair | 0.00–0.45 | The gap is a refinement of a refinement — deep micro territory. |
| Circle with radial ticks | 0.00–0.50 | Ticks are greebles; they vanish by mid. |
| Circle with off-center indicator | 0.00–0.55 | Single tick as pointer survives slightly longer than tick clusters. |
| Circle with diagonal slash | 0.00–0.65 | Simple enough to approach macro as a status indicator. |
| Circle with flat chord (D-shape) | 0.20–0.85 | Strong silhouette — reads well at macro. Needs *some* context at micro to not feel empty. |
| Partial arc | 0.10–0.75 | At micro: dynamic element within a panel. At macro: bold gesture. |
| Circle filled (solid disc) | 0.40–1.00 | Too simple for micro (feels like a dot, not an element). Perfect macro primitive. |
| Concentric perspective (tunnel) | 0.00–0.45 | Inherently detailed; the depth illusion needs multiple rings. |

#### Star and Radial Burst Family

| Variant | Viable Range | Notes |
|---|---|---|
| 4-point sharp star | 0.30–1.00 | The quintessential macro glyph. At micro: accent within a panel. At deep macro: standalone sigil. |
| 4-point equal star | 0.30–1.00 | Same range as sharp; slightly more balanced feel. |
| 6-arm asterisk | 0.00–0.65 | At micro: indicator within a panel. Thin arms don't hold up as bold macro silhouette. |
| 8-point starburst | 0.20–0.90 | Wide range. Adapts well — detailed at micro, bold at macro. |
| Multi-point sunburst (12–24 rays) | 0.00–0.50 | Many thin lines = inherently micro. Collapses to a blob at macro scale. |
| Snowflake | 0.00–0.40 | Secondary branches are greebles. Deeply micro. |
| Compass rose | 0.10–0.60 | Alternating arm lengths = detail. Mid-range element. |
| Pinwheel / vortex star | 0.40–1.00 | Curved arms create strong silhouette. Born for macro. |
| Petal star | 0.40–1.00 | Filled rounded arms = bold silhouette. Strong macro element. |
| Serrated star | 0.30–0.90 | The jagged edge carries across scales. |
| Flame ring | 0.50–1.00 | Organic, bold, silhouette-weight. Macro native. |
| Thorn ring | 0.30–0.85 | Spikes add detail but the ring form is bold. Wide mid-range. |

#### Globe and Sphere Family

| Variant | Viable Range | Notes |
|---|---|---|
| Wireframe sphere (front) | 0.00–0.50 | Grid lines are inherent detail. Micro/mid element. |
| Sphere with horizontal stripes | 0.20–0.80 | Stripe count decreases toward macro. At macro: 2–3 bold bands. |
| Eye-globe | 0.15–0.70 | Distinctive shape survives into macro, but needs some internal structure. |
| Half-globe | 0.30–0.85 | Clean silhouette. Works well at macro with minimal stripes. |
| Torus / ring with grid | 0.00–0.45 | Surface detail is the point. Micro only. |
| Concentric ellipse (snail) | 0.10–0.55 | Multiple nested forms = detail. |
| Perspective rectangle (tunnel) | 0.00–0.40 | Needs many layers to read. Deep micro. |
| Stacked parallelogram layers | 0.10–0.55 | Layer count decreases toward mid. |

#### Hourglass / Bowtie / Figure-8 Family

| Variant | Viable Range | Notes |
|---|---|---|
| Vertical hourglass | 0.30–1.00 | Strong bilateral silhouette. Excellent macro element. |
| Horizontal bowtie | 0.30–1.00 | Same — bold form at any scale above micro. |
| Double hourglass | 0.20–0.70 | More complex; the X-form adds detail. |
| Organic hourglass | 0.50–1.00 | Soft curves demand bold scale. Born macro. |
| Hourglass with inner detail | 0.10–0.55 | The inner detail is the point — loses meaning at macro. |
| Figure-8 chain | 0.10–0.60 | Multiple linked forms = inherent complexity. |
| Diamond pinch | 0.25–0.90 | Geometric version — clean silhouette, wide range. |

#### Connector and Chain Family

| Variant | Viable Range | Notes |
|---|---|---|
| Line with terminal dots | 0.00–0.50 | Micro/mid element. Too minimal for macro as standalone. |
| Dashed line | 0.00–0.35 | Pure greeble / annotation. Deep micro. |
| Line with tick marks | 0.00–0.30 | Ticks are the definition of micro detail. |
| Bead chain | 0.20–0.75 | Simplifies well — fewer, larger beads toward macro. |
| Dumbbell / barbell | 0.30–0.85 | Clean bilateral form. Reads at macro scale. |
| Segmented bar | 0.00–0.50 | Segments are inherent detail. |
| Node chain | 0.10–0.65 | Node count decreases. At mid: just two nodes and a line. |
| Branching connector | 0.00–0.40 | Complexity is the point. Micro. |
| Wave connector | 0.10–0.55 | Simpler than branching but still a connector. |
| Stacked bars | 0.00–0.45 | Multiple parallel elements = density. |
| Ladder | 0.00–0.40 | Rungs are greebles. |

#### Gear / Seal / Badge Family

| Variant | Viable Range | Notes |
|---|---|---|
| Gear wheel | 0.20–0.85 | Tooth count decreases toward macro. At macro: bold cog silhouette. |
| Serrated badge | 0.30–0.90 | Dense teeth create strong edge at any scale. |
| Wavy edge seal | 0.35–0.95 | Smooth, bold, reads as silhouette. Good macro. |
| Dotted ring badge | 0.10–0.60 | Dots are small detail. Mid-range ceiling. |
| Ratchet / saw blade | 0.30–0.90 | Asymmetric teeth create dynamic silhouette. |

#### Enclosure and Frame Family

| Variant | Viable Range | Notes |
|---|---|---|
| 4-corner bracket | 0.00–0.45 | Framing device for micro compositions. Vanishes at macro — nothing to frame. |
| 2-corner bracket | 0.00–0.40 | Even more minimal framing; same ceiling. |
| Nested squares | 0.10–0.65 | Count decreases. At mid: just two. |
| Rounded rectangle | 0.15–0.75 | As standalone silhouette, works at macro. As frame, micro. |
| Square with notch | 0.15–0.70 | The notch adds character that persists across scales. |
| Hexagonal frame | 0.10–0.75 | Distinctive shape. At macro: single bold hex outline. |
| Concentric nested frames | 0.00–0.40 | Multiple layers = density. |

#### Dot and Grid Family

| Variant | Viable Range | Notes |
|---|---|---|
| Regular dot grid | 0.00–0.40 | Texture element. Does not exist at macro. |
| Dot arc / spiral | 0.05–0.55 | Dot count decreases. At mid: sparse arc. |
| Dot ring | 0.10–0.60 | At mid: ring of 6–8 dots. Beyond that, becomes a circle. |
| Dot cluster | 0.00–0.35 | Pure texture. Deep micro only. |
| Pixel block grid | 0.00–0.35 | Binary state visualization. Deep micro. |
| Dot-size gradient | 0.00–0.40 | Gradient requires enough dots to read the progression. |

#### Organic / Biomorphic Family

| Variant | Viable Range | Notes |
|---|---|---|
| Blob pair | 0.45–1.00 | Needs bold scale to read as intentional, not accidental. |
| Cellular cluster | 0.30–0.80 | Cell count decreases toward macro. At macro: 2–3 bold circles. |
| Molecule chain | 0.10–0.55 | Branching detail. Mid-range ceiling. |
| Organic ring | 0.45–1.00 | Bold wreath form. Macro native. |
| Tendril / spider | 0.40–0.90 | Arm count decreases. At macro: 3–4 bold curved arms. |
| Rotating claw | 0.50–1.00 | Defined by its bold, swept form. Deep macro. |
| Sketch circle | 0.50–0.95 | The irregularity is the statement. Needs macro scale for the gesture to read. |

#### Barcode and Machine-Readable Code Family

| Variant | Viable Range | Notes |
|---|---|---|
| 1D barcode (full) | 0.00–0.45 | Detailed, many bars. Plausibly scannable. Micro native. |
| 1D barcode with text | 0.00–0.35 | Text underneath adds density. Deep micro. |
| 2D barcode / data matrix | 0.00–0.40 | Dense grid of cells. Inherently micro. |
| QR code block | 0.00–0.45 | Position markers help it read at slightly larger scale. |
| Corrupted barcode | 0.10–0.60 | Recognizable but broken. The corruption adds visual interest at mid-scale. |
| Corrupted QR | 0.10–0.55 | Same — corruption extends range by adding graphic weight. |
| Barcode fragment | 0.15–0.70 | A few bold bars. At macro end: abstract rhythm, not "barcode." |
| Stacked barcode (PDF417) | 0.00–0.35 | Maximum density. Deep micro. |
| QR position marker (standalone) | 0.25–0.80 | The three-square corner element used as an isolated glyph. Strong silhouette. |

#### Warning and Hazard Iconography Family

| Variant | Viable Range | Notes |
|---|---|---|
| Triangle with exclamation | 0.15–0.95 | Universal, reads at any scale. One of the widest-range elements. |
| Triangle with inner symbol | 0.10–0.85 | Inner symbol sheds at macro end; triangle alone remains. |
| Radiation trefoil | 0.25–1.00 | Iconic radial form. Excellent macro silhouette. |
| Biohazard | 0.25–1.00 | Interlocking arcs — bold at any scale. |
| High voltage bolt | 0.20–0.95 | Simple, graphic, strong silhouette. Wide range. |
| Skull (simplified) | 0.30–0.90 | Needs to be geometric enough to not feel illustrative. |
| Chevron warning stripe | 0.00–0.60 | Repeated pattern = density. Used as border/fill, not standalone at macro. |
| Circular prohibition | 0.15–0.85 | The slash-through-circle is icon-weight at any scale. |
| Diamond hazard frame | 0.15–0.80 | Outer diamond + inner glyph. Inner sheds toward macro. |
| Alert beacon (broadcast arcs) | 0.10–0.70 | Arcs reduce in count toward macro. |
| Eye / surveillance | 0.25–0.90 | Bold bilateral form. Strong macro candidate. |
| Lock / unlock | 0.20–0.85 | Simple, iconic. |
| Broken link | 0.10–0.60 | Needs enough detail to read the "break." |

#### Wireframe and Low-Poly Geometry Family

| Variant | Viable Range | Notes |
|---|---|---|
| Wireframe cube | 0.10–0.75 | At micro: small indicator in panel. At macro: bold geometric statement. Edge count decreases (hidden lines removed). |
| Wireframe icosahedron | 0.00–0.50 | Many edges = density. Micro native. |
| Geodesic sphere | 0.05–0.55 | Triangular facets are inherent detail. |
| Low-poly terrain | 0.00–0.40 | Mesh density is the point. |
| Wireframe torus | 0.05–0.50 | Grid lines on curved surface. |
| Platonic solid (simple) | 0.15–0.85 | Tetrahedron, octahedron — few enough faces for clean macro. |
| Exploded wireframe | 0.05–0.45 | Multiple offset faces = complexity. |
| Hidden-line-dashed | 0.00–0.40 | The dashed treatment is a detail layer that sheds early. |
| Vertex-and-edge graph | 0.10–0.65 | Node count decreases. At mid: 3–4 vertices. |

#### Grid, Pattern, and Texture Family

| Variant | Viable Range | Notes |
|---|---|---|
| Regular dot grid | 0.00–0.40 | Pure texture. |
| Dot arc / spiral | 0.05–0.55 | Dot count decreases. |
| Dot ring | 0.10–0.60 | At mid: 6–8 dots in a ring. |
| Dot cluster | 0.00–0.35 | Noise-like. Deep micro. |
| Pixel block grid | 0.00–0.35 | Binary visualization. |
| Dot-size gradient | 0.00–0.40 | Needs enough dots for the gradient to read. |
| Checkerboard | 0.00–0.45 | At mid: very coarse (2x2 or 3x3). |
| Dither pattern | 0.00–0.35 | Inherently fine-grained. |
| ASCII block texture | 0.00–0.35 | Character-density dependent. |
| Crosshatch | 0.00–0.45 | Line density decreases. At mid: just two crossing line sets. |
| Parallel lines | 0.00–0.55 | Line count decreases. At mid: 3–4 bold lines. |
| Chevron field | 0.00–0.50 | Row count decreases. |
| X-pattern grid | 0.00–0.40 | Repeat count decreases. |
| Plus-pattern grid | 0.00–0.40 | Same. |

---

### 10.4 Archetype Grading

Each composite archetype also has a viable range and transformation behavior.

| Archetype | Viable Range | Transformation Toward Macro |
|---|---|---|
| **Status Panel** | 0.00–0.35 | Loses annotation layers first, then secondary text, then numeric values. By 0.35, only a label + one shape remains — at which point it becomes a different archetype. |
| **Targeting Reticle** | 0.00–0.55 | Loses tick marks first, then crosshair gaps simplify, then outer rings drop. At 0.55: just one ring + simple crosshair. |
| **Meter / Gauge** | 0.00–0.45 | Loses numeric readout, then the pointer simplifies, then the arc track alone remains. |
| **Data Readout Block** | 0.00–0.25 | The most micro-locked archetype. It IS text. Remove text and it ceases to exist. |
| **Node / Network Marker** | 0.05–0.50 | Satellite count decreases, labels drop, connector lines shorten. At 0.50: a central dot with 2–3 bare satellite dots. |
| **Standalone Glyph** | 0.35–1.00 | Gains simplicity. At 0.35: a glyph with a label and one accent. At 1.0: the glyph alone, no context. |
| **Globe / Sphere Panel** | 0.10–0.65 | Panel framing drops first, then grid detail on globe reduces, then stripe count drops. |
| **Seal / Badge** | 0.25–0.90 | Interior detail simplifies. At macro: bold outer silhouette + maybe one central dot. |
| **Error Message / System Alert** | 0.00–0.35 | Text-dependent like Data Readout. Loses stack trace lines first, then error detail, then warning icon stands alone (at which point it becomes a Standalone Glyph). |
| **Terminal / Console Block** | 0.00–0.30 | The most micro-locked alongside Data Readout. Defined by its text grid. Loses ASCII art first, then output lines, then prompt. Cannot exist without text. |
| **Encoded Data Block** | 0.00–0.60 | Barcode detail reduces: full scannable code → corrupted → fragment → a few bold abstract bars. Labels drop by 0.40. At 0.60: just a rhythmic bar pattern, no longer "barcode." |
| **Dataviz Fragment** | 0.05–0.55 | Axis lines and labels drop first, then data point count decreases. At 0.55: a single bold waveform trace or 3 histogram bars with no annotation. |

---

### 10.5 Detail Shedding Order

As the slider moves from micro to macro, detail layers shed in a defined order. This is the sequence — each layer vanishes entirely before the next begins to reduce:

1. **Annotation texture** (micro-text fills) — first to go (vanishes by ~0.25)
2. **Version strings** — gone by ~0.30
3. **Secondary text** (key-value pairs, coordinates, filenames) — gone by ~0.35
4. **Tick marks and calibration marks** — gone by ~0.40
5. **Connector lines and arrows** — gone by ~0.40
6. **Corner bracket framing** — gone by ~0.45
7. **Primary labels** (status words, identifiers) — gone by ~0.50
8. **Dot matrix textures** — gone by ~0.50
9. **Crosshairs and subdivision lines** — gone by ~0.55
10. **Secondary concentric rings / nested forms** — reduced through 0.40–0.65
11. **Internal structural detail** (inner circles, internal lines) — gone by ~0.70
12. **All remaining internal features** — gone by ~0.80; only the outer silhouette remains

---

### 10.6 Rendering Mode Crossfade

The rendering mode shifts as a function of scale position:

| Range | Dominant Mode | Behavior |
|---|---|---|
| 0.00–0.30 | **Line / Outline** | All elements rendered as outlines. Fills only via dot matrix texture. |
| 0.30–0.55 | **Line → Hybrid transition** | Primary forms begin to take on partial fills. Accent elements may be solid while containers remain outlined. |
| 0.55–0.75 | **Hybrid → Solid transition** | Most forms are filled. Outlines remain only on secondary/framing elements. |
| 0.75–1.00 | **Solid / Silhouette** | All forms are fully filled. No outlines remain. Pure shape. |

---

### 10.7 Filtering Rules for Implementation

When the slider value `S` is set, the generator should:

1. **Filter archetypes**: exclude any archetype whose viable range does not contain `S`.
2. **Filter element families**: within the selected archetype, exclude any family variant whose viable range does not contain `S`.
3. **Apply detail shedding**: remove all detail layers whose vanishing threshold is below `S`.
4. **Set rendering mode**: use the mode corresponding to `S` per the crossfade table.
5. **Set element count**: interpolate between the micro element count (3–8) and macro element count (1) based on `S`.
6. **Adjust internal parameters**: for any surviving element, interpolate continuous parameters (ring count, arm count, tick density, stroke weight) between their micro and macro values based on where `S` falls within that element's viable range.

The viable ranges have soft edges — at the boundaries, an element should appear at reduced probability (e.g., within 0.05 of the edge, 50% chance of inclusion) rather than hard on/off cutoffs. This prevents visible "popping" as the slider moves.

---

### 10.8 Micro–Macro Interaction with Other Parameters

Some generation parameters from Section 13 are constrained or biased by the scale position:

| Parameter | Micro Bias | Macro Bias |
|---|---|---|
| Symmetry class | Double bilateral, Asymmetric | Radial-N, Rotational |
| Organic character | Low | Full range available |
| Fill texture | Line-only, Dot matrix | Solid |
| Activity state | Active (gaps, partial arcs) | Either (static reads as monumental) |
| Containment style | Brackets, frames | None |
| Text density | Medium–Dense | None |
| Unicode accent | Integrated in panel | Absent, or IS the element |
| Color mode | Dark (HUD) preferred | Light (glyph) preferred |

---

## 13. Additional Generation Parameters

Primary axes for algorithmic variation. Each generated element is fully described by values on these axes.

| Parameter | Options |
|---|---|
| **Rendering mode** | Line, Solid, Hybrid |
| **Element archetype** | Status Panel, Reticle, Meter, Data Block, Node, Glyph, Globe Panel, Seal, Error Message, Terminal Block, Encoded Data, Dataviz Fragment |
| **Primary shape family** | Circle, Star/Radial, Globe, Hourglass, Connector, Gear/Seal, Enclosure, Grid/Pattern, Organic, Barcode, Warning/Hazard, Wireframe |
| **Border** (global toggle) | Off, On (style auto-selected per micro-macro position) |
| **Border style** (when On) | Corner bracket, Broken path, Manhattan path, Technical/HUD, Ornamental/Signal (see Section 8) |
| **Aesthetic treatment** | None, Glitch, Terminal, Corruption, Algorithm, Hacking (see Section 9) |
| **Symmetry class** | Radial-N (N=3/4/5/6/8), Bilateral, Double Bilateral, Rotational, Asymmetric |
| **Containment style** | None, 2-corner bracket, 4-corner bracket, Full rectangle, Hexagonal frame |
| **Radial subdivision** | None, Quartered, 8-way, 12-way, 24-way |
| **Text density** (HUD only) | Sparse (1–2 labels), Medium (3–5 strings), Dense (6+ strings) |
| **Unicode accent** | None, Single character, Two characters |
| **Fill texture** | Line-only, Dot matrix, Solid, Stripe/layer fill |
| **Activity state** | Static (closed forms), Active (partial arcs / gaps) |
| **Annotation layer** | None, Minimal, Full (micro-text texture) |
| **Connector presence** | None, Tick-line, Arrow, Branching, Wave |
| **Dominant numeric** | Percentage, Voltage, Temperature, Coordinate, Hex code |
| **Organic character** | Low (pure geometry), Medium (soft curves), High (biomorphic) |
| **Rotational motion feel** | Static, Directional (arrow-implied), Spinning (pinwheel arms) |
| **Font selection** | Auto, or any loaded font from `/fonts/micro/` and `/fonts/macro/` directories |
| **Font tier** | Micro fonts (single-line, all scales) or Macro fonts (outline, scale >= 0.6 only, 1–2 instances max) |
| **Archetype probability weights** | 0.0–1.0 per archetype (relative weighting) |
| **Soft edge filtering** | On (gradual probability falloff at range edges), Off (hard cutoff) |

---

## 14. The Unified Conceptual Frame

The HUD elements and the symbolic glyphs are not two separate vocabularies — they are two *scales* of the same system.

- **Glyphs** are the symbols a system uses to identify its own subsystems — icons for processes, states, and entities.
- **HUD panels** are the diagnostic outputs those systems generate — readouts, logs, status reports.

A generated micrographic is either a **symbol** (a thing's identity mark) or a **panel** (a thing's status report). Both are artifacts of the same unseen machine. Together they imply an entire operational world without depicting it directly.

The viewer's role is that of an analyst: able to recognize patterns, read partial information, and intuit function from form — but never fully decode the system. That productive incompleteness is the aesthetic's core tension.

There is also a material dimension: these graphics are designed for **pen plotter output** — physical ink on physical paper, traced by a mechanical arm following vector paths. The constraint of single-stroke rendering is not a limitation but a creative partner. It forces every design decision through the filter of "can a pen draw this in one motion?" — and the answer shapes the aesthetic as much as any visual reference does. The plotted output carries the subtle imperfections of physical machinery: slight ink variation, paper texture, the mechanical precision of stepper motors. These artifacts are part of the work.
