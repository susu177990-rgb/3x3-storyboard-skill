# 3x3 Storyboard Skill

## 中文

一个面向 AI 视频预制作的导演分镜 Skill。它把用户提供的剧本、故事想法、角色资产、场景资产、道具资产和风格参考，拆解成两段式工作流：

1. 阶段一：剧本 / 素材 -> 15 秒一页的导演流程图式文字分镜表
2. 阶段二：文字分镜表 -> 3x3 九宫格分镜图 / 生图提示词

这个 Skill 不限定题材。剧情、科幻、悬疑、动作、爱情、喜剧、恐怖、广告、游戏 CG、MV、品牌短片都可以直接使用。风格、镜头语言、光线、调色、质感由用户输入决定，不被 Skill 预设绑死。

### 核心能力

- 把剧本或故事想法拆成可拍摄、可生成视频的镜头设计
- 统一按每 15 秒一页生成完整文字分镜表
- 每页输出镜头号、时间、景别、静态分镜画面、运镜、动作、台词 / 旁白、音效 / 音乐、备注
- 把每页文字分镜扩展为固定 3x3、共 9 格的分镜宫格图
- 在 `/next` 与 `/grid-all` 中强制执行跨页视觉连续性
- 支持只输出内部生图提示词，不直接出图
- 支持表单式输入和命令式工作流两种使用方式

### 命令流程

| 命令 | 作用 |
|---|---|
| `/start` | 启动导演分镜工作台并显示菜单 |
| `/asset` | 输出素材填写模板 |
| `/storyboard` | 阶段一：生成完整文字分镜表 |
| `/grid` | 阶段二：生成第 1 页九宫格分镜图 |
| `/next` | 阶段二：生成下一页九宫格分镜图，并保持跨页连续 |
| `/grid-all` | 阶段二：一次性批量生成全部页面九宫格分镜图 |
| `/prompt` | 阶段二：只输出全部页面的内部生图提示词 |
| `/help` | 显示命令说明与使用指引 |

标准使用顺序：

```text
/start
/asset
[填写并发送素材 + 剧本]
/storyboard
/grid
/next
/next
```

如果只要提示词：

```text
/start
/asset
[填写并发送素材 + 剧本]
/storyboard
/prompt
```

### 工作流结构

#### 阶段一：文字分镜

阶段一一次性输出整部片子的文字版导演流程图式分镜表，而不是只输出第一页。默认规则：

- 每页严格 15 秒
- 每页通常 4 到 6 个镜头，允许按节奏变化浮动
- 输出语言默认简体中文
- 所有镜头必须可拍摄、可生成、动作清晰
- 资产不完整时先生成可执行版本，并把不确定项标为 `待补充`

输出结构核心如下：

```md
# 导演流程图式分镜表

## 使用资产

| 类型 | 名称 | 使用说明 |
|---|---|---|

## 第 1 页 / 0:00–0:15

| 镜头号 | 时间 | 景别 | 静态分镜画面 | 运镜方式 | 画面描述 / 动作 | 台词 / 旁白 | 音效 / 音乐 | 备注 |
|---|---|---|---|---|---|---|---|---|

---

## 底部参数栏
【整体风格 / 影像质感】
【摄影参数】
【动作设计】
【音效设计】
【总时长】
```

#### 阶段二：3x3 九宫格分镜图

阶段二把每一页 15 秒文字分镜转换成一张固定 3x3、共 9 格的分镜宫格图。

关键规则：

- 一页对应一张图
- 永远固定 3x3，共 9 格
- 时间顺序为从左到右、从上到下
- 如果原页只有 4 到 6 个镜头，必须补拆成 9 个连续表演瞬间
- 每个格子内部使用用户影片画幅，不把整张九宫格强行做成该画幅
- 图中除每格左上角可有小号 1-9 序号外，不能有其他说明文字

`/grid` 生成第 1 页，`/next` 逐页推进，`/grid-all` 一次性跑完整部片。

### 跨页连续性

从第 2 页开始，必须把上一页第 9 格与当前页第 1 格做成连续电影帧关系。以下要素必须连续：

- 角色脸型、发型、发色、肤色、体型
- 服装、装甲、配饰、机械结构
- 动作状态和身体朝向
- 场景空间、物件位置、光线方向
- 道具状态
- 色温、调色、影像质感、情绪氛围

如果模型支持参考图输入，应直接附带上一页宫格图；如果不支持，也必须把上一页第 9 格的视觉状态写进提示词。

### 表单接口

仓库内置了 UI schema，适用于表单驱动的 Skill 运行方式。

#### `interface/input.json`

定义输入表单，包括：

- 执行阶段选择：`storyboard` / `grid` / `next` / `grid-all` / `prompt`
- 项目信息：项目名称、类型、时长、画幅比例、影像质感、输出语言
- 剧本输入：上传文件或直接文本输入
- 角色资产、场景资产、道具资产、风格参考、调色参考
- 阶段二补充上下文与高级选项

#### `interface/output.json`

定义输出结构，包括：

- 当前阶段与执行状态
- 全片资产汇总
- 页面规划表
- 阶段一文字分镜表
- 阶段二九宫格结果
- 阶段二提示词结果

### 目录结构

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

### 规则设计

- 阶段一和阶段二必须分离，不能混在一次输出里
- 阶段一必须一次性产出全部页面，不半路停下追问
- 阶段二必须始终固定 3x3 九宫格，不能退回 2x2、2x3 或按镜头数浮动
- 所有镜头都必须把心理状态翻译成可见的动作、表情、灯光、构图与声音
- 必须优先服从用户素材中的角色、服装、道具、场景与风格锚点
- 对话场景必须遵守正反打、轴线、反应镜头等专业拆镜原则

### 适用场景

- AI 视频预制作
- 短片导演分镜
- 广告脚本可视化
- MV / 游戏 CG 预演
- 图生视频 / 文生视频 的镜头规划
- 多页连续镜头设计

### 仓库内容说明

这个仓库包含完整 Skill 包：

- 入口规则：`skill/SKILL.md`
- 两段式工作流：`skill/workflows/`
- 输入模板：`skill/templates/`
- 连续性 / 镜头语言 / 画质规则：`skill/rules/`
- UI 表单 schema：`interface/`
- 示例输入输出：`skill/examples/`

如果要继续扩展，优先保持这套两段式结构和固定 3x3 输出契约，不要把提示词堆回单文件大杂烩。

---

## English

This is a director-oriented storyboard skill for AI video pre-production. It turns the user's script, story idea, character assets, scene assets, props, and style references into a two-stage workflow:

1. Stage 1: script / assets -> 15-second-per-page director storyboard table
2. Stage 2: storyboard table -> 3x3 storyboard grid images / image prompts

The skill is genre-agnostic. It works for drama, sci-fi, thriller, action, romance, comedy, horror, advertising, game cinematics, music videos, and branded shorts. Visual style, shot language, lighting, grading, and texture come from user input rather than a fixed built-in look.

### Core Capabilities

- Break a script or story idea into filmable, video-generatable shots
- Produce a complete text storyboard in 15-second pages
- Output shot number, time range, shot size, static frame, camera movement, action, dialogue / narration, sound / music, and narrative notes for every shot
- Expand each storyboard page into a fixed 3x3 grid with exactly 9 cells
- Enforce cross-page visual continuity in `/next` and `/grid-all`
- Support prompt-only output when direct image generation is not desired
- Support both form-based and command-based execution

### Command Flow

| Command | Purpose |
|---|---|
| `/start` | Start the storyboard workspace and show the command menu |
| `/asset` | Output the asset intake template |
| `/storyboard` | Stage 1: generate the full text storyboard |
| `/grid` | Stage 2: generate the page 1 storyboard grid |
| `/next` | Stage 2: generate the next storyboard grid with cross-page continuity |
| `/grid-all` | Stage 2: batch-generate all storyboard grids |
| `/prompt` | Stage 2: output the internal prompts for all pages only |
| `/help` | Show usage guidance |

Typical flow:

```text
/start
/asset
[send assets + script]
/storyboard
/grid
/next
/next
```

Prompt-only flow:

```text
/start
/asset
[send assets + script]
/storyboard
/prompt
```

### Workflow Structure

#### Stage 1: Text Storyboard

Stage 1 outputs the full director-style text storyboard for the entire project instead of stopping after page 1. Default behavior:

- each page is exactly 15 seconds
- each page usually contains 4 to 6 shots, with rhythm-based variation allowed
- default output language is Simplified Chinese
- every shot must be filmable, visually concrete, and video-generatable
- when asset information is incomplete, the skill still generates a usable version and marks uncertain fields as `待补充`

Core output structure:

```md
# 导演流程图式分镜表

## 使用资产

| 类型 | 名称 | 使用说明 |
|---|---|---|

## 第 1 页 / 0:00–0:15

| 镜头号 | 时间 | 景别 | 静态分镜画面 | 运镜方式 | 画面描述 / 动作 | 台词 / 旁白 | 音效 / 音乐 | 备注 |
|---|---|---|---|---|---|---|---|---|

---

## 底部参数栏
【整体风格 / 影像质感】
【摄影参数】
【动作设计】
【音效设计】
【总时长】
```

#### Stage 2: 3x3 Storyboard Grid

Stage 2 converts each 15-second storyboard page into one fixed 3x3 grid with 9 cells.

Key rules:

- one page equals one image
- always a fixed 3x3 grid with exactly 9 cells
- time order runs left to right, top to bottom
- if the original page only has 4 to 6 shots, it must be expanded into 9 continuous performance beats
- the user's film aspect ratio applies inside each cell, not to the full grid canvas
- no descriptive text is allowed inside the image except a small optional 1-9 sequence number in the top-left corner of each cell

`/grid` generates page 1, `/next` advances page by page, and `/grid-all` runs the whole project in one pass.

### Cross-Page Continuity

Starting from page 2, cell 9 of the previous page must connect to cell 1 of the current page like adjacent movie frames. The following elements must remain continuous:

- face shape, hairstyle, hair color, skin tone, body type
- costume, armor, accessories, mechanical structures
- action state and body orientation
- scene layout, object positions, and lighting direction
- prop state
- color temperature, grading, cinematic texture, and emotional atmosphere

If the model supports reference-image input, the previous grid image should be attached. If not, the visual state of the previous page's cell 9 must be described explicitly in the prompt.

### Form Interface

The repository includes UI schemas for form-driven runtime execution.

#### `interface/input.json`

Defines the input form, including:

- execution stage selection: `storyboard` / `grid` / `next` / `grid-all` / `prompt`
- project metadata: project name, type, duration, aspect ratio, visual texture, output language
- script input: file upload or direct text
- character assets, scene assets, props, style references, and color grading references
- stage-two context and advanced options

#### `interface/output.json`

Defines the output structure, including:

- current stage and execution status
- project-wide asset summary
- page planning table
- stage-one text storyboard
- stage-two grid results
- stage-two prompt results

### Repository Structure

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

### Design Rules

- Stage 1 and Stage 2 must remain separate
- Stage 1 must output all pages in one pass rather than stopping midway
- Stage 2 must always use a fixed 3x3 grid and must not fall back to 2x2, 2x3, or shot-count-driven layouts
- psychological states must be translated into visible action, expression, lighting, framing, and sound
- user-provided character, costume, prop, scene, and style anchors must take priority
- dialogue scenes must follow professional shot-reverse-shot, axis, and reaction-shot principles

### Use Cases

- AI video pre-production
- short film storyboarding
- commercial storyboard planning
- MV / game cinematic previs
- shot design for text-to-video and image-to-video workflows
- multi-page continuity-driven visual planning

### Repository Contents

This repository includes the complete skill package:

- entry rules: `skill/SKILL.md`
- two-stage workflows: `skill/workflows/`
- input templates: `skill/templates/`
- continuity, shot-language, and image-quality rules: `skill/rules/`
- UI form schemas: `interface/`
- example input and output: `skill/examples/`

For future expansion, keep the two-stage structure and fixed 3x3 output contract intact instead of collapsing everything back into one oversized prompt file.
