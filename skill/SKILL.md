---
name: storyboard-flow
version: 1.0.0
description: Genre-agnostic director storyboard skill. Convert scripts, story ideas, and visual assets into director-style storyboard workflows and direct 3x3 storyboard image generation for AI video production. Works for any genre — drama, sci-fi, thriller, action, romance, comedy, horror, advertisement, game cinematics, and more. The visual style, tone, and shot language are determined by the user's script and asset inputs. Use when the user sends /start, /help, /next, /grid, /asset, or asks for 分镜, 导演分镜, 分镜表, 镜头拆解, 剧本转分镜, AI 视频分镜, 每 15 秒一页, 短片镜头规划, 分镜宫格图, 九宫格分镜图, or turning a text storyboard page into generated storyboard images.
---

# Storyboard Flow

Use this skill to turn scripts and visual assets into a two-stage short-film storyboard workflow. This skill is genre-agnostic — it works for any film genre. The specific visual style, tone, color palette, and shot language are determined entirely by the user's script and asset inputs, not by the skill itself.

## /start Command

When the user sends `/start`, activate this skill immediately and reply with the following greeting:

```
🎬 导演分镜工作台已启动

我可以帮你完成：
1. 📝 剧本 / 故事想法 → 15 秒一页的导演分镜表
2. 🖼️ 分镜表 → 3×3 九宫格分镜图生成

标准流程：/start → /asset → 发送素材+剧本 → /storyboard → /grid → /next → /next...

/asset — 获取素材输入模板
/storyboard — 第一步：生成全部页面的文字分镜表
/grid — 第二步：生成第 1 页的九宫格分镜图
/next — 继续生成下一页的九宫格分镜图（跨页全方位连续）
/grid-all — 一键批量生成全部页的九宫格分镜图
/prompt — 输出全部页面的生图提示词
/help — 显示帮助

请先发送 /asset 获取素材模板，填写后连同剧本一起发给我。
适用于任何题材：剧情、科幻、悬疑、动作、爱情、喜剧、恐怖、广告、游戏 CG 等。
```

After the greeting, wait for the user to provide assets and script. Do NOT automatically start Stage 1 or Stage 2 when the user sends assets or script. Wait for the user to explicitly send a slash command.

## Commands

| Command | Action | Load |
|---|---|---|
| `/start` | Activate the skill and display the welcome menu | — |
| `/asset` | Output the asset input template | `templates/asset_input_template.md` |
| `/storyboard` | Execute Stage 1: generate the full text storyboard (all pages at once) | `workflows/01_script_to_text_storyboard.md`, `templates/text_storyboard_page_template.md` |
| `/grid` | Execute Stage 2: generate page 1’s 3×3 grid image | `workflows/02_text_storyboard_to_grid_prompt.md`, `rules/continuity_rules.md` |
| `/next` | Continue Stage 2: generate the next page’s 3×3 grid image with full cross-page visual continuity | `workflows/02_text_storyboard_to_grid_prompt.md`, `rules/continuity_rules.md` |
| `/grid-all` | Execute Stage 2: batch-generate all pages’ grid images in one response, sequentially | `workflows/02_text_storyboard_to_grid_prompt.md`, `rules/continuity_rules.md` |
| `/prompt` | Execute Stage 2: output the internal image generation prompts for all pages (text only) | `workflows/02_text_storyboard_to_grid_prompt.md`, `templates/grid_image_prompt_template.md` |
| `/help` | Display all available commands and a brief usage guide | — |

When the user sends `/help`, reply with the command table and a one-paragraph usage summary.

When the user sends `/storyboard`, execute Stage 1: generate the full text storyboard with all pages. This requires the user to have previously sent script/story and assets. If not, ask them to provide the material first.

When the user sends `/grid`, generate the first page’s (page 1) 3×3 grid image. This requires Stage 1 to have been completed. If Stage 1 has not been run, ask the user to run `/storyboard` first. After generating page 1, prompt the user to send `/next` for the next page.

When the user sends `/next`, generate the next page's 3×3 grid image. Before generating, locate the previous page's generated grid image in the conversation context and use it as a visual reference. Extract cell 9 (bottom-right) visual state for continuity. Enforce full cross-page visual continuity (character appearance, costume, hairstyle, body proportions, pose/action flow, scene environment, lighting direction, color palette, prop state, cinematic texture). Cell 9 of the previous page must visually connect to cell 1 of the current page. When generating, reference the previous image as style/character reference to ensure consistency. If the model supports attaching reference images, attach the previous page's grid image. If not, describe cell 9's visual details in the prompt as text-level anchoring. After generating, prompt `/next` or indicate completion.

When the user sends `/grid-all`, batch-generate all pages’ grid images in one response, sequentially from page 1 to the last page. Each page still follows the same cross-page visual continuity rules as `/next`. Do not stop after the first image or ask the user to continue mid-batch.

When the user sends `/prompt`, output the internal image generation prompts for all pages sequentially. Do not generate images. This requires Stage 1 to have been completed.

IMPORTANT: When the user sends assets or script text (without a slash command), acknowledge receipt and store the material, but do NOT automatically start generating. Wait for `/storyboard`.

## Execution Router

Route based on the slash command the user sends. Do not auto-route based on free-text input.

| User sends | Route | Load |
|---|---|---|
| `/asset` | Output the asset input template | `templates/asset_input_template.md` |
| `/storyboard` | Stage 1: generate full text storyboard from the previously provided script and assets | `workflows/01_script_to_text_storyboard.md`, then `templates/text_storyboard_page_template.md` |
| `/grid` | Stage 2: generate page 1’s grid image | `workflows/02_text_storyboard_to_grid_prompt.md`, `rules/continuity_rules.md` |
| `/next` | Stage 2: generate the next page’s grid image with cross-page continuity | `workflows/02_text_storyboard_to_grid_prompt.md`, `rules/continuity_rules.md` |
| `/grid-all` | Stage 2: batch-generate all pages in one response | `workflows/02_text_storyboard_to_grid_prompt.md`, `rules/continuity_rules.md` |
| `/prompt` | Stage 2: output internal prompts for all pages | `workflows/02_text_storyboard_to_grid_prompt.md`, `templates/grid_image_prompt_template.md` |
| Script, story idea, assets (no slash command) | Acknowledge receipt, store material, wait for `/storyboard` | — |

Load rule files only when their topic matters:

- Continuity, character drift, scene drift, prop state, page-to-page continuity: `rules/continuity_rules.md`
- Shot design, shot size, camera motion, pacing, time allocation: `rules/shot_language_rules.md`
- Grid image quality, no-text constraints, layout, realism, visual consistency: `rules/image_quality_rules.md`
- Example input/output behavior: `examples/example_input.md` and `examples/example_output.md`

## Defaults

Use these defaults when the user does not specify otherwise:

- Output language: Simplified Chinese
- Page duration: exactly 15 seconds per page
- Shots per page: 4 to 6 shots
- Total film length: usually 1 to 2 minutes
- Per-cell storyboard frame aspect ratio: user-specified film aspect ratio; default 9:16 vertical
- Storyboard image quality target: 4K
- Visual texture: live-action cinematic film stills
- Color style: low saturation, realistic lighting, mild film grain
- Tone: professional short-film camera language

## Hard Rules

- Keep Stage 1 text planning and Stage 2 image generation separate.
- Do not ask the image model to render Chinese tables, shot numbers, timecodes, captions, bottom parameter bars, or any descriptive text inside storyboard cells. The ONLY text allowed inside each cell is a small sequence number (1–9) in the top-left corner.
- In Stage 2, generate one image per 15-second page.
- In Stage 2, always use a 3x3 grid with exactly 9 cells.
- In Stage 2, apply the user film aspect ratio to each individual cell, not to the whole 3x3 canvas.
- In Stage 2, expand the original 4 to 6 shots into 9 continuous performance/action frames.
- Every Stage 1 page must total exactly 15 seconds with no gaps or overlaps.
- Every Stage 1 shot must include: shot number, time range, shot size/focus, static frame description, camera movement, visible action, sound/music, and narrative note.
- Every shot must be filmable, video-generatable, and visually concrete.
- Do not write novel-style inner thoughts as shot content. Translate psychology into visible action, framing, lighting, expression, body movement, and sound.
- Preserve user-provided character, costume, weapon, prop, scene, style, and color references. Do not invent important characters, core props, or key locations.
- If information is missing, produce a usable version from the available material and mark only the uncertain fields as `待补充`.
- In Stage 1, output all pages of the text storyboard at once. Do not stop after the first page or ask the user to continue.
- After Stage 1 completes, prompt the user with options: `/grid` to generate page 1, `/grid-all` to batch-generate all pages, `/prompt` to output prompts, or `/help` for guidance.
- `/grid` generates page 1 only. After page 1, prompt the user to send `/next`.
- `/next` generates the next page. Repeat `/next` until all pages are done.
- `/grid-all` batch-generates all pages in one response, sequentially from page 1 to the last. Same cross-page continuity rules apply.
- Cross-page visual continuity is mandatory for every `/next` and `/grid-all`: review the previous page’s grid image and enforce continuity of character appearance, costume, hairstyle, body proportions, facial features, pose/action flow, scene environment, lighting direction, color palette, prop state, and overall cinematic texture. The last cell (cell 9) of the previous page must visually connect to the first cell (cell 1) of the current page.
- When `/prompt` is used: output the internal image generation prompts for all pages sequentially. Do not generate images.

## Stage 1 Output Contract

When generating the text storyboard, use the following overall structure. The asset table and parameter bar each appear ONCE for the entire film, not per page. Each page contains ONLY the shot table.

The shot table includes a `台词 / 旁白` column. If a shot has no dialogue, write `—`.

```md
# 导演流程图式分镜表

## 使用资产

| 类型 | 名称 | 使用说明 |
|---|---|---|
| 角色 | xxx | 全片核心角色，状态描述 |
| 场景 | xxx | 主要空间描述 |
| 道具 | xxx | 关键叙事物件 |
| 调色参考 | xxx | 影片整体调色方向 |

---

## 第 1 页 / 0:00–0:15

| 镜头号 | 时间 | 景别 | 静态分镜画面 | 运镜方式 | 画面描述 / 动作 | 台词 / 旁白 | 音效 / 音乐 | 备注 |
|---|---|---|---|---|---|---|---|---|

## 第 2 页 / 0:15–0:30

| 镜头号 | 时间 | 景别 | 静态分镜画面 | 运镜方式 | 画面描述 / 动作 | 台词 / 旁白 | 音效 / 音乐 | 备注 |
|---|---|---|---|---|---|---|---|---|

(...all pages...)

---

## 底部参数栏

【整体风格 / 影像质感】
【摄影参数】
【动作设计】
【音效设计】
【总时长】
```

## Stage 2 Output Contract

When generating Stage 2 output, directly create the storyboard image if image generation is available. Do not default to showing the prompt.

Each Stage 2 image must:

- Correspond to one 15-second page.
- Use a fixed 3x3 grid.
- Contain exactly 9 storyboard cells.
- Arrange cells left to right, top to bottom in time order.
- Use the user film aspect ratio inside each cell. If unspecified, use 9:16 inside each cell.
- Expand the Stage 1 shots into 9 continuous performance/action frames when the page has fewer than 9 shots.
- Preserve unified cinematic texture, character consistency, scene consistency, prop consistency, and no-text/no-table/no-caption constraints.

Only output the internal image prompt when the user asks for it or direct image generation is not available.

## Quality Check Before Answering

Before final output, check:

- Stage 1 pages each total exactly 15 seconds.
- Shot time ranges have no overlap or empty gap.
- Every shot has a clear visible action and camera movement.
- Sound design exists for every shot.
- Notes explain the narrative or emotional purpose rather than repeating the picture.
- Stage 2 images use fixed 3x3 grids and exactly 9 cells.
- Stage 2 applies the film aspect ratio to each cell, not the whole canvas.
- Stage 2 images contain no text, labels, table fields, captions, or timecodes.
- Character, scene, prop, light direction, and emotional continuity are explicit enough for AI video production.
