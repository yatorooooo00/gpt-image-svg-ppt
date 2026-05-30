# Toolchain Guide

This skill is tool-agnostic, but the workflow must preserve this order:

```text
source documents -> outline approval -> deck plan -> style lock -> sample image-2 pages -> sample reference PPT pages -> full-slide GPT image PNGs -> semantic SVG/PPT conversion -> PPTX
```

## Image Generation

Use the available GPT image generation capability. Prefer `gpt-image-2`/`image2` when available in the environment. If the runtime exposes a different OpenAI image model, use the configured model and record it in `prompts/slide-XX.md`.

Generation requirements:

- One complete full-slide PNG canvas per slide, not a background image.
- Full-deck generation must be preceded by representative sample images for user review.
- Sample and full-deck image2 PNGs should include visible slide text so the user can evaluate wording, hierarchy, text density, and placement in the complete design.
- Same aspect ratio across all slides.
- Same reusable style suffix across all slides, with enough variation to fit each slide's purpose.
- Full-slide PPT page composition, not isolated spot art, unless the user explicitly asks for spot illustrations.
- Follow the user's latest content and style instructions before older preferences or skill defaults.
- If the user provided a PPT template, match the generated page to its dimensions, margins, palette, typography mood, and reusable visual elements.
- No unsupported people, dates, locations, logos, maps, charts, or statistics.

## Sample Review Gates

Use two review gates before the full deck:

1. **Image samples**: generate 2-3 representative image-2 complete slide PNGs and ask the user to review style, composition, source fidelity, and template fit.
2. **Reference PPT pages**: after the text-bearing image samples are approved, convert those samples into SVG/PPT reference pages, replacing image-rendered text with transparent editable PPT text boxes, and ask the user to review editability, text treatment, chart/table handling, and layout.

Do not proceed to full-deck generation until the user confirms both gates or explicitly tells you to continue.

## PNG to SVG/PPT Semantic Conversion

Choose the vectorizer based on desired output:

- **VTracer**: preferred for colorful flat illustrations, icons, infographic pages, and clean edges.
- **potrace/autotrace**: useful for monochrome or limited-palette silhouettes and line art.
- **Quiver or another image-to-SVG service**: useful when semantic vector reconstruction is more important than literal tracing.
- **GPT/vision-assisted SVG reconstruction**: preferred for this skill when the slide needs clean editable structure. Use the generated PNG as a visual reference, then rebuild major objects, diagrams, layout regions, and text areas as semantic SVG/PPT elements using the source document, deck plan, and PPT template as constraints.
- **Manual SVG recreation**: use when the generated PNG contains charts, timelines, diagrams, or text that must remain editable and automated conversion is not clean enough.

Hard requirement:

- The SVG must contain real vector elements such as `<path>`, `<polygon>`, `<rect>`, `<circle>`, `<line>`, `<polyline>`, `<text>`, or grouped equivalents.
- Keep the image-2 PNG as the visual source of truth. The conversion should reconstruct that slide's design intelligently, not substitute a different design.
- Treat text rendered inside the image2 PNG as a visual draft for wording, placement, size, and hierarchy. Rebuild that text as transparent editable PPT text boxes in the PPTX/SVG workflow.
- Do not accept an SVG that is only a wrapper around a full-page raster image when text/data/chart/table content needs to be editable.
- The intended PowerPoint workflow is: insert SVG, use PowerPoint's "Convert to Shape" command, then edit meaningful slide objects. Required text, charts, tables, data labels, legends, axes, factual labels, and key diagram elements must be editable either through converted SVG shapes or native PPT objects/text boxes.
- Complex decorative backgrounds, textures, atmospheric imagery, non-critical illustration details, and photo-like regions may remain as a grouped/vectorized visual layer or locked raster backdrop when that preserves clarity and performance. Do not spend effort turning every background color patch into separate editable shapes.
- Do not require every tiny color transition, texture patch, shadow, or antialiasing artifact to become a separate vector path. That produces unusable PPTs. Preserve clarity while making required informational content editable.
- If a vectorizer cannot produce useful editable vector content for a page, simplify the image prompt, use GPT/vision-assisted semantic reconstruction, use a semantic image-to-SVG service, or manually rebuild the page in SVG/PPT shapes.
- Important text should normally be rebuilt as PPT text boxes, using the image2 layout as reference plus source text and template typography. Do not rely on traced text outlines when the user needs to edit wording.

Quality settings:

- Prefer fewer colors, larger flat regions, and clean visual hierarchy in the original full-slide image prompt when SVG editability matters.
- Use the PPT template as a reconstruction guide: match margins, text boxes, title zones, palette, and reusable layout elements.
- Simplify or posterize before tracing only when semantic reconstruction is not available or not enough.
- For historical or factual diagrams, rebuild text, arrows, timelines, maps, chart marks, and labels as editable SVG/PPT shapes instead of trusting OCR-like tracing.

Reject an SVG if:

- it traps required text, chart, table, data, legend, axis, or factual-label content inside an uneditable raster image
- it contains an embedded full-page raster `<image>` as the only meaningful page content
- PowerPoint cannot convert required informational elements into meaningful editable shapes and those elements were not rebuilt as native PPT objects
- the page is blank or mostly missing
- the aspect ratio changed
- key source-backed objects disappeared
- key text blocks or label areas became unreadable noise
- the file is too large or too path-fragmented for practical PPT editing
- it converts into thousands of tiny color fragments instead of usable grouped shapes

## PPTX Assembly

Assemble slides from SVG files in deck order.

Preferred behavior:

- Use the user's PPT template as the assembly base when provided; otherwise create a blank wide or standard deck matching `style-lock.md`.
- Add each full-slide SVG to the matching slide.
- Add crisp editable PPT overlays for title, labels, captions, source notes, and page numbers.
- Added text boxes should be transparent by default: no fill color, no opaque background, and no visible border unless needed for readability or required by the template/user.
- Match text boxes to the slide background and template: adapt font family, size, weight, line height, alignment, contrast, and position to the generated page.
- If text is too long, visually awkward, or conflicts with the image, rewrite it into concise source-faithful slide text before placing it. Preserve factual meaning, numbers, names, dates, and claims.
- Preserve speaker notes from `deck-plan.md` when requested.

Fallbacks:

- If the PPT library cannot embed SVG directly, do not silently fall back to raster-only output. Keep the true SVG files beside the deck, state the limitation, and preserve an alternate PPTX or workflow that lets the user insert/convert the SVG to shapes.
- If editable vector content is required, use a toolchain that imports SVG as PowerPoint vector content, or recreate critical shapes directly in PPT.

## Verification

Before delivery:

1. Open or render the PPTX.
2. Check all pages are visible and ordered.
3. Compare every slide against `deck-plan.md`.
4. Confirm the visual style is coherent across the deck, while allowing intentional slide-to-slide variation.
5. Confirm revised slides follow the user's latest instruction while remaining coherent with the deck.
6. Inspect SVG source or use a parser to confirm the main content is vector, not embedded raster images.
7. Open the PPTX in PowerPoint when possible and verify at least a representative SVG slide can be converted to shapes, with required text/data/chart/table content editable and complex backgrounds still visually clear.
8. Verify added text boxes are transparent, visually integrated with the background, readable, and source-faithful if rewritten.
