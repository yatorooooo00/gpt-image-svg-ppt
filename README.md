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
## 中文介绍

`gpt-image-svg-ppt` 是一个用于生成 PPT 的 Codex skill。它会根据用户提供的文档、要求和 PPT 模板，先规划内容，再用 `image2` / `gpt-image-2` 生成完整 PPT 页面效果，并进一步重建为 SVG/PPT 页面，最终汇总成 PPTX。

它的重点不是简单生成背景图，而是让 PPT 同时具备视觉表现力和后期可编辑性：复杂背景可以整体保留，文本、图表、表格、数据等关键信息应保持可编辑。

这个skill旨在在不可编辑的美观图片和可编辑的PPT格式中找平衡

欢迎各位使用

### 建议流程

1. **理解文档**  
   先阅读用户提供的文档、备注、模板和要求，提炼主题、证据、数据、受众和限制条件。

2. **确认页数和大纲**  
   在生成图片前，先给出建议页数和每页内容大纲，让用户确认或修改。

3. **规划每页内容**  
   明确每页讲什么、画什么、依据来自哪里、哪些内容不能出现。

4. **固定整体画风**  
   为整套 PPT 设定统一的视觉方向，但不要求每一页完全一样。

5. **生成 image2 例图**  
   先生成 2-3 张代表性完整 PPT 页面例图，让用户确认风格、构图和内容方向。

6. **制作参考 PPT 页**  
   用户确认例图后，把例图转换成参考 PPT 页，让用户检查文本、图表、表格和数据是否可编辑。

7. **批量生成完整页面**  
   用户确认参考 PPT 页后，再批量生成每一页的完整 PPT 页面 PNG。

8. **智能重建为 SVG/PPT 页面**  
   以 image2 生成图为视觉基础，智能重建关键内容：文本、图表、表格、数据、标签、箭头和关键图解元素应可编辑；复杂背景可以整体保留。

9. **汇总成 PPTX**  
   将所有页面汇总为最终 PPTX。如用户提供模板，优先使用用户模板。

10. **检查和修改**  
   交付前检查内容是否忠于文档、画风是否统一、关键元素是否可编辑。后续修改时优先按用户最新要求，只改指定页面。

### 特色

- **完整页面生成**：`image2` 生成的是完整 PPT 页面，不是单纯背景图。
- **双确认机制**：先确认 image2 例图，再确认参考 PPT 页，降低整套生成后返工的风险。
- **模板优先**：如果用户提供 PPT 模板，优先适配模板的尺寸、版式、字体和视觉风格。
- **关键内容可编辑**：文本、图表、表格、数据、坐标轴、图例和标签等信息性内容应保持可编辑。
- **复杂背景可保留**：不强行把每个色块都拆成矢量路径，避免 PPT 卡顿或难以编辑。
- **文档依据优先**：所有事实、数据和视觉表达应来自用户文档或明确要求，避免模型编造。
- **适合迭代修改**：保留大纲、提示词、PNG、SVG、QA 记录等中间产物，方便后续只修改指定页面。

### 推荐调用方式

```text
请用 $gpt-image-svg-ppt，根据我上传的文档做一套 PPT。
先给我页数建议和每页大纲；我确认后再生成 image2 例图。
文本、图表、表格和数据必须可编辑，复杂背景可以整体保留。
严格依据文档，不要编造内容。
