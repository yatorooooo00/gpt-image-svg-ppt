# GPT Image SVG PPT

A Codex skill for turning source documents into PowerPoint decks using a staged workflow:

1. Understand the user's document and requirements.
2. Propose slide count and outline for user approval.
3. Generate representative full-slide `gpt-image-2` / `image2` samples for feedback.
4. Convert approved samples into reference PPT pages for editability review.
5. Generate complete full-slide PNG canvases.
6. Semantically reconstruct those pages into SVG/PPT objects.
7. Assemble a PPTX, preserving template constraints when a user supplies a PPT template.

The skill is designed for decks where image-generated visual design is useful, but key informational elements must remain editable. Text, charts, tables, data labels, axes, legends, and factual labels should be rebuilt as editable PPT text boxes, PPT objects, or clean SVG/PPT shapes. Complex decorative backgrounds can remain grouped or raster-backed when that preserves clarity and does not trap required editable content.

## Install

Copy this folder into your Codex skills directory:

```bash
cp -R gpt-image-svg-ppt ~/.codex/skills/gpt-image-svg-ppt
```

Restart Codex, then invoke the skill explicitly:

```text
Use $gpt-image-svg-ppt to turn my document and requirements into a style-consistent SVG-based PPT deck.
```

## Files

- `SKILL.md`: Codex skill instructions and trigger metadata.
- `README.zh-CN.md`: Chinese explanation of the workflow.
- `agents/openai.yaml`: UI metadata for Codex.
- `references/artifact-contract.md`: Required working artifacts and QA records.
- `references/toolchain.md`: Image generation, semantic SVG/PPT conversion, and PPT assembly guidance.

## Workflow Notes

- The generated `image2` page is the visual source of truth for conversion; the conversion step should not redesign the page unless the user asks for a design change.
- Full-deck generation must wait until the user approves both image samples and reference PPT pages.
- SVG output must not be a fake SVG that only wraps a full-page PNG/JPEG when editable text, chart, table, or data content is required.
- The PowerPoint output should favor practical editability over exhaustive pixel tracing.

## License

MIT
