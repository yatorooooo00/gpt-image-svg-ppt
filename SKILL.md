---
name: gpt-image-svg-ppt
description: Create or revise PowerPoint decks from user-provided documents by understanding the source, proposing slide count and outline for user approval, generating complete style-consistent GPT image model PNG slide canvases, vectorizing each full-slide PNG to SVG, and assembling all SVG slides into a PPTX. Use when the user asks for a document-driven PPT, deck, slide deck, image2/gpt-image-2 generated full-slide pages, PNG-to-SVG vector slides, editable/vector-style PowerPoint output, template-based PPT generation, or revisions that must follow the user's latest requirements while staying faithful to source files.
---

# GPT Image SVG PPT

## Operating Rule

Treat the user's document and explicit requirements as the authority. Do not invent facts, imagery, chronology, claims, labels, or numbers that are not supported by the provided material or approved by the user.

Apply requirements in this order: the user's latest message, previously stated user requirements that do not conflict, source documents, user-provided PPT templates, then this skill's defaults. If a newer user instruction changes page count, content, style, template use, or generation method, update the plan before creating or revising images.

Generate complete slide canvases with `gpt-image-2`/`image2`, not background art. Each generated PNG should represent the whole intended PPT page: composition, visual hierarchy, major content blocks, imagery, diagram structure, and approximate text/label placement. Use editable PPT overlays only to make text crisp, correct, or selectable; do not reduce the generated image to a decorative background unless the user asks for that.

When the user provides a PPT template, use it as the primary deck container and visual constraint. Extract its slide size, masters/layouts, palette, typography, spacing, and reusable elements before planning. Prefer assembling generated SVG pages into that template and preserving template elements unless the user asks to replace them.

For every deck, produce these artifacts in a dedicated working folder:

```text
source-summary.md
outline-proposal.md
deck-plan.md
style-lock.md
samples/
prompts/
png/
svg/
qa/
final.pptx
```

Read `references/artifact-contract.md` before starting a new deck or handling revisions. Read `references/toolchain.md` before generating images, vectorizing PNGs, or assembling PPTX.

## Workflow

1. **Ingest the source**
   - Read all user-provided documents, notes, and constraints.
   - Write `source-summary.md` with the main thesis, evidence, entities, timeline, required tone, and explicit user requirements.
   - If the user provided a PPT template, inspect it before planning and record reusable template constraints in `source-summary.md`.
   - Mark unsupported assumptions as `NEEDS_USER_APPROVAL`; do not put them into image prompts.

2. **Propose slide count and outline**
   - Write `outline-proposal.md` before `deck-plan.md`.
   - Recommend page count based on the document length, density, audience, user requirements, and template constraints.
   - Present the recommended slide count and a concise slide-by-slide outline to the user.
   - Ask the user to confirm, change page count, reorder sections, add/remove topics, or adjust style before any image generation.
   - Do not generate PNGs or SVGs until the user approves the outline or explicitly tells you to proceed.

3. **Plan the deck before making images**
   - Write `deck-plan.md` with one section per slide:
     - slide number and title
     - source-backed message
     - bullet/content payload
     - complete slide canvas direction
     - exact source references or document locations used
     - what must not appear
   - Match the approved outline, user's requested structure, audience, page count, language, aspect ratio, template, and emphasis.

4. **Create a style lock**
   - Write `style-lock.md` before the first image generation.
   - Include composition rules, color palette, typography mood, illustration/photo style, lighting, perspective, texture level, icon/diagram treatment, negative prompts, template constraints when present, and a reusable style suffix.
   - Use the same style suffix in every GPT image prompt and every revision prompt.
   - Keep the deck visually coherent, but do not force every slide to look identical. Allow variation in composition, emphasis, and scene design when the approved outline calls for it.

5. **Generate sample image-2 slide canvases for user review**
   - Before generating the full deck, use `gpt-image-2`/`image2` to create a small set of representative complete slide PNG samples, typically 2-3 pages covering different slide types such as cover/key message, content/diagram, and data/table when applicable.
   - Save them under `samples/png/` with matching prompt files under `samples/prompts/`.
   - Present the sample images to the user and ask for feedback on style, composition, document fidelity, template fit, and whether the generated page direction is acceptable.
   - Apply the user's sample feedback to `style-lock.md` and `deck-plan.md`.
   - Do not generate the full deck until the user confirms the sample image direction or explicitly tells you to proceed.

6. **Convert approved samples into reference PPT pages**
   - After the user confirms the sample images, semantically convert those sample PNGs into SVG/reference PPT pages using the same rules that will be used for the final deck.
   - Save the reference PPT as `samples/reference-pages.pptx`.
   - Show or summarize the reference PPT pages for user review, specifically calling out what is editable: text boxes, charts, tables, data labels, and major diagram elements.
   - Ask the user for another round of feedback before building the full deck.
   - Do not proceed to full-deck generation until the user confirms the reference PPT page behavior or explicitly tells you to continue.

7. **Generate one complete PNG slide canvas per slide**
   - Generate each slide image with the configured GPT image model, using `gpt-image-2`/`image2` when available in the environment.
   - Build each prompt from: the approved `deck-plan.md` slide content + `style-lock.md` + exact constraints from the source + the user's latest requirements.
   - Request a complete full-slide PPT page composition, including visual hierarchy, layout, imagery, diagram structures, and intended title/label areas.
   - Avoid asking the image model to render long readable paragraphs. For important text, include its intended placement in the complete slide image and then add/edit crisp PPT overlays during assembly.
   - Save prompts in `prompts/slide-XX.md` and outputs in `png/slide-XX.png`.

8. **Semantically convert the whole PNG slide to SVG/PPT objects**
   - Convert every `png/slide-XX.png` to `svg/slide-XX.svg`.
   - Treat the SVG as the editable full slide page, not a background layer.
   - Keep the image-2 PNG as the visual source of truth for the conversion; do not redesign the slide from scratch unless the user asks for a design change.
   - Prefer semantic SVG/PPT reconstruction over naive pixel tracing: use GPT/vision-assisted interpretation of the PNG, source document, approved deck plan, and PPT template to rebuild editable text, charts, tables, data labels, key diagram elements, major layout regions, icons, shapes, arrows, and text areas.
   - Do not force every visual element to become a native PowerPoint object. Complex decorative backgrounds, textures, atmosphere, and non-critical illustrative detail may remain as grouped/vectorized background artwork or, when unavoidable, a locked raster backdrop, as long as required editable content is not trapped inside it.
   - Text, charts, tables, numeric data, legends, axes, data labels, and user-facing factual labels must be editable as PPT text boxes, PPT chart/table objects, or clean SVG/PPT shapes suitable for PowerPoint editing.
   - Keep clarity high and editability practical. Do not split every tiny color patch into thousands of vector paths. Group shapes by semantic role and preserve only the detail needed for a sharp presentation page.
   - Ensure required editable elements can become editable PowerPoint shapes through PowerPoint's "Convert to Shape" workflow or are rebuilt as native PPT objects/text boxes. Do not accept SVG files that merely wrap or embed the original PNG/JPEG as a base64 `<image>` element for the main editable content.
   - Recreate important text as editable PPT text boxes, or as SVG `<text>` only when the PPT import workflow preserves it well. Use the PPT template's text box positions, typography, and margins when provided.
   - Inspect each SVG for blank output, missing major objects, broken aspect ratio, excessive file size, embedded full-page raster images, lost text blocks, unreadable text, and visible trace artifacts.
   - If required text/data/chart/table content is not editable, regenerate or reconstruct that content before assembly.

9. **Assemble the PPTX**
   - Use the user-provided PPT template when available; otherwise create a blank deck with the approved dimensions.
   - Create one PPT slide per SVG, using matching template layouts when available.
   - Place each `svg/slide-XX.svg` full-bleed on its matching slide.
   - Add editable PPT text boxes for titles, labels, captions, page numbers, speaker notes, and any text that must remain crisp or user-editable. Prefer template text boxes and styles when available.
   - Keep `final.pptx` plus the `svg/` and `png/` folders so later revisions can target exact pages.

10. **QA before delivery**
   - Render or preview the PPTX pages.
   - Check: approved outline fidelity, sample feedback applied, reference PPT feedback applied, document fidelity, slide order, template fit, coherent style, no off-topic imagery, no hallucinated text, no clipped content, SVGs visible in the deck, required text/data/chart/table content is editable, and complex backgrounds remain clear.
   - Record issues and fixes in `qa/notes.md`.

## Revision Rules

When the user requests changes:

- Re-read `deck-plan.md`, `style-lock.md`, and the relevant slide prompt before editing.
- Apply the user's newest revision instruction first, even if it changes a prior style or content preference.
- Change only the requested slides unless the user asks for a global update.
- Keep the deck visually coherent with the original style lock, but allow the revised slide to differ when the user's newest instruction requires it.
- If the change affects factual content, trace it back to the source document or ask for approval.
- Save new prompts and images with versioned names such as `slide-03-v2`.
- Rebuild the PPTX after replacing the affected SVG pages.

## Prompt Discipline

Each slide prompt must contain:

```text
Slide: <number and title>
Source-grounded content: <facts and constraints from the document>
Visual objective: <what the slide should communicate visually>
Composition: <complete full-slide PPT page layout, focal point, safe text areas>
Required elements: <objects, people, places, charts, diagrams>
Forbidden elements: <unsupported or unwanted content>
Style lock: <shared suffix copied from style-lock.md>
Template constraints: <template layout, palette, typography, spacing, reusable elements if supplied>
Output: complete full-slide PNG canvas, same aspect ratio as deck
```

Keep prompts strict. If the source says "cotton textile exports rose after 1850", do not prompt "industrial factory interior with steam engines" unless that is supported or approved.

## Output Expectations

Deliver:

- `final.pptx`
- the working folder containing `deck-plan.md`, `style-lock.md`, `prompts/`, `png/`, and `svg/`
- a concise note listing source assumptions, generated pages, and any pages that need user review

If a required tool or API key is unavailable, stop before generating assets and tell the user exactly which capability is missing.
