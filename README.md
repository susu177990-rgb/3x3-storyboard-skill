# 3×3 Storyboard Skill

A genre-agnostic director storyboard skill for turning scripts, story ideas, and visual asset descriptions into a two-stage AI video storyboard workflow:

1. **Script / asset intake → 15-second-per-page director storyboard table**
2. **Storyboard table → 3×3 nine-cell storyboard grid image prompt / image generation workflow**

The skill is designed for AI video production, short films, advertisements, game cinematics, music videos, and visual pre-production workflows. It does not lock the user into one visual style. Instead, the final tone, shot language, lighting, color palette, and cinematic texture are controlled by the user-provided script and visual assets.

## Core Capability

This skill helps transform rough creative material into production-ready visual planning:

- Convert scripts, story ideas, and scene descriptions into director-style storyboard pages.
- Split the project into **exactly 15-second pages**.
- Generate structured shot tables with time ranges, shot size, frame description, camera movement, visible action, dialogue, sound, and narrative notes.
- Convert each 15-second page into a **3×3 storyboard grid** with exactly 9 cells.
- Maintain continuity across pages through character, costume, scene, prop, lighting, color, action, and emotional state.
- Output internal image-generation prompts when direct image generation is not available.

## Command Workflow

| Command | Purpose |
|---|---|
| `/start` | Activate the storyboard workflow and show the command menu. |
| `/asset` | Output the asset intake template. |
| `/storyboard` | Generate the full text storyboard from the provided script and assets. |
| `/grid` | Generate the first 3×3 storyboard grid image from page 1. |
| `/next` | Generate the next page’s grid image with cross-page continuity. |
| `/grid-all` | Batch-generate all storyboard grid images sequentially. |
| `/prompt` | Output image-generation prompts for all pages without generating images. |
| `/help` | Show command help and usage guidance. |

## Standard Usage

```text
/start
/asset
[paste or upload project assets + script]
/storyboard
/grid
/next
/next
```

For prompt-only workflows:

```text
/start
/asset
[paste or upload project assets + script]
/storyboard
/prompt
```

## Output Structure

### Stage 1: Text Storyboard

Stage 1 produces a complete director storyboard table:

```md
# 导演流程图式分镜表

## 使用资产

| 类型 | 名称 | 使用说明 |
|---|---|---|

## 第 1 页 / 0:00–0:15

| 镜头号 | 时间 | 景别 | 静态分镜画面 | 运镜方式 | 画面描述 / 动作 | 台词 / 旁白 | 音效 / 音乐 | 备注 |
|---|---|---|---|---|---|---|---|---|

## 底部参数栏
```

### Stage 2: 3×3 Storyboard Grid

Stage 2 converts each 15-second page into one fixed 3×3 storyboard grid:

- exactly 9 cells
- left-to-right, top-to-bottom time order
- one generated image per 15-second page
- no text, captions, shot numbers, timecodes, tables, or parameter bars inside the cells
- only a small optional sequence number `1–9` in the top-left corner is allowed
- each cell uses the user’s film aspect ratio internally, rather than applying the ratio to the entire grid canvas

## Repository Structure

```text
.
├── README.md
├── .skillignore
├── interface/
│   ├── input.json
│   └── output.json
└── skill/
    ├── SKILL.md
    ├── manifest.txt
    ├── agents/
    │   └── openai.yaml
    ├── workflows/
    │   ├── 01_script_to_text_storyboard.md
    │   └── 02_text_storyboard_to_grid_prompt.md
    ├── templates/
    │   ├── asset_input_template.md
    │   ├── text_storyboard_page_template.md
    │   └── grid_image_prompt_template.md
    ├── rules/
    │   ├── continuity_rules.md
    │   ├── shot_language_rules.md
    │   └── image_quality_rules.md
    └── examples/
        ├── example_input.md
        └── example_output.md
```

## Key Design Rules

- Stage 1 text planning and Stage 2 image generation must remain separate.
- Each Stage 1 page totals exactly 15 seconds.
- Every shot must be filmable, video-generatable, and visually concrete.
- Psychological states must be translated into visible action, lighting, framing, expression, body movement, and sound.
- The user’s character, costume, prop, weapon, scene, style, and color references must be preserved.
- Stage 2 always uses a fixed 3×3 grid with exactly 9 cells.
- Cross-page visual continuity is mandatory for `/next` and `/grid-all`.

## Interface Schemas

The `interface/` folder contains JSON schemas for UI-driven use:

- `input.json` defines project metadata, script input, character assets, scene assets, props, style references, color grading, Stage 2 context, and advanced options.
- `output.json` defines text storyboard output, page planning, asset summaries, 3×3 grid results, and image prompt output.

## Intended Use Cases

- AI video pre-production
- short-film storyboard planning
- commercial / advertisement storyboard planning
- music video visual planning
- game cinematic planning
- social video storyboard workflows
- text-to-image / image-to-video shot breakdowns
- multi-page visual continuity control

## Notes

This repository contains the complete skill package, including the runtime entrypoint, workflow documents, templates, continuity rules, shot-language rules, image-quality rules, UI schemas, and examples.
