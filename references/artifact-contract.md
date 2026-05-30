# Artifact Contract

Use this file as the deck state contract. It keeps planning, generation, vectorization, assembly, and revisions traceable.

## `source-summary.md`

Required sections:

```text
# Source Summary

## User Requirements
- ...

## Core Thesis
...

## Key Evidence
- Evidence:
  Source location:

## Required Terms / Names / Numbers
- ...

## Constraints
- Audience:
- Language:
- Page count:
- Aspect ratio:
- Tone:
- User-provided template:

## Open Questions
- NEEDS_USER_APPROVAL:
```

## `outline-proposal.md`

Write this after `source-summary.md` and before generating any images.

Required sections:

```text
# Outline Proposal

## Recommended Page Count
<number and rationale>

## Proposed Deck Structure
- Slide 01:
- Slide 02:

## User Decisions Needed
- Confirm page count:
- Add/remove/reorder sections:
- Confirm style direction:
- Confirm template use:

## Status
Awaiting user approval before image generation.
```

Rules:

- Do not proceed to image generation until the user approves the outline or explicitly says to proceed.
- If the user changes the outline, update this file and then write `deck-plan.md`.

## `deck-plan.md`

Required slide schema:

```text
## Slide 01 - <title>

Message:

Content:
- ...

Complete Slide Canvas:

Source Basis:
- <document/page/section/quote summary>

Do Not Include:
- ...

Text Overlays:
- Title:
- Labels:
- Speaker notes:
- Text box style:
- Rewritten text if needed:
Template Use:
- Layout:
- Preserved template elements:
- Replaced template elements:
```

Rules:

- Every visual claim must map to `Source Basis`.
- Include `Do Not Include` when the source is narrow or historically sensitive.
- Plan the generated PNG as the full slide page, then use PPT overlays to keep important text crisp.
- Put long prose in speaker notes, not inside generated images.

## `style-lock.md`

Required sections:

```text
# Style Lock

Aspect ratio:
Deck dimensions:
Medium:
Visual style:
Color palette:
Lighting:
Composition:
Slide-to-slide variation rules:
People/objects treatment:
Typography treatment:
Texture/detail level:
Template constraints:
Negative style constraints:

## Reusable Style Suffix
<copy this exact block into every image prompt>
```

Rules:

- Do not change this file during revisions unless the user asks for a global style change.
- If the user asks for style changes, version it as `style-lock-v2.md` and regenerate all affected slides.

## `prompts/slide-XX.md`

Each prompt file must include the final prompt sent to the image model plus a short source trace:

```text
# Slide XX Prompt

Model:
Size/aspect ratio:
Prompt:
Image-rendered text draft:

## Source Trace
- ...

## Revision History
- v1:
```

## `samples/`

Required sample artifacts:

```text
samples/
  prompts/
    sample-01.md
  png/
    sample-01.png
  svg/
    sample-01.svg
  reference-pages.pptx
  feedback.md
```

Rules:

- Generate representative image-2 samples before full-deck generation.
- Include visible text in image-2 samples so users can review complete slide composition.
- Record user feedback on image samples in `samples/feedback.md`.
- After image sample approval, convert the approved samples into reference PPT pages.
- During reference PPT conversion, replace image-rendered sample text with transparent editable PPT text boxes.
- Record user feedback on reference PPT editability, template fit, text boxes, charts, tables, and data handling.
- Do not proceed to the full deck until these sample gates are approved or explicitly waived by the user.

## `qa/notes.md`

Track:

- source fidelity issues
- image generation issues
- semantic SVG conversion artifacts
- SVG true-vector checks, including whether embedded full-page raster `<image>` elements are absent from main page content
- PowerPoint "Convert to Shape" verification results, including whether required informational elements are editable without excessive tiny path fragmentation
- text, chart, table, and data editability checks, including whether important text was rebuilt with PPT text boxes or reliable SVG text and whether charts/tables/data labels are editable
- transparent text box checks, including whether added boxes have no fill/opaque background unless explicitly required
- text fit checks, including whether font, size, line spacing, and placement coordinate with the background
- rewritten text checks, including whether condensed or rewritten text remains faithful to source documents
- confirmation that complex backgrounds remain visually clear and are not over-fragmented into unusable tiny paths
- PPT assembly issues
- unresolved user review items
