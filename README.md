<p align="right">
  <strong>语言 / Language:</strong>
  <a href="#中文">中文</a> ·
  <a href="#english">English</a>
</p>

<a id="中文"></a>

# 3x3 Storyboard Skill

把剧本、故事想法和视觉素材，直接整理成可执行的导演分镜流程。这个 skill 的重点不是随便出几张参考图，而是先把故事拆成可拍、可生成、可继续推进的文字分镜，再把每一页稳定转成固定 `3x3` 九宫格分镜图。

## 功能亮点

- 两段式流程：先文字分镜，后九宫格分镜
- 固定 `3x3` 输出，不会乱变布局
- 支持 `/next` 的跨页连续生成
- 支持只出提示词，不强制立刻生图
- 支持剧本、素材、表单三种信息一起使用

## 它适合做什么

- 把剧本快速拆成导演可读的分镜表
- 把短片、广告、MV、剧情片、游戏 CG 的镜头结构先跑顺
- 把每 `15` 秒内容稳定转成一张 `3x3` 九宫格分镜图
- 在多页连续生成时保持角色、服装、场景、动作和光线连续
- 只输出图片提示词，不立即出图

## 你最终会拿到什么

- 全片文字分镜表
- 每页 `15` 秒的镜头拆解
- 每页一张固定 `3x3`、共 `9` 格的分镜图
- 可批量生成的多页分镜提示词
- 支持 `/next` 的跨页连续流程

## Installation / 安装

如果你作为本地 skill 使用，直接把这个目录放进 Codex skills 目录即可：

```bash
cp -R "3x3 storyboard skill" ~/.codex/skills/3x3-storyboard
```

## 最短使用路径

```text
/start
/asset
[发送剧本、故事想法、角色/场景/道具/风格素材]
/storyboard
/grid
/next
```

如果你只想先拿提示词：

```text
/start
/asset
[发送素材]
/storyboard
/prompt
```

## Usage / 用法

最常见的调用方式：

```text
请用这个 skill 把我的剧本先拆成完整文字分镜，再从第一页开始生成 3x3 九宫格分镜图。
```

## 常用命令

| 命令 | 作用 |
|---|---|
| `/start` | 启动工作区并显示流程入口 |
| `/asset` | 输出素材填写模板 |
| `/storyboard` | 先生成全片文字分镜表 |
| `/grid` | 生成第一页 `3x3` 九宫格分镜图 |
| `/next` | 生成下一页分镜图，并继承上一页状态 |
| `/grid-all` | 一次性生成所有页面的九宫格分镜图 |
| `/prompt` | 只输出阶段二生图提示词 |
| `/help` | 查看命令说明 |

## 工作方式

### 阶段一：文字分镜

先把整部片子拆成导演可读的文字分镜表，而不是只出第一页。

默认规则：

- 每页固定 `15` 秒
- 每页通常 `4-6` 个镜头，再按需要扩成阶段二的 `9` 格
- 输出语言默认简体中文
- 素材不完整时照样先产出可执行版本，并标记待补信息

### 阶段二：九宫格分镜图

把阶段一的每一页转换成一张固定 `3x3`、共 `9` 格的分镜图。

关键规则：

- 一页对应一张图
- 永远固定 `3x3`
- 时间顺序按从左到右、从上到下
- 镜头不够 `9` 格时，会补拆成连续动作瞬间
- 影片画幅只作用在单格画面内部，不强行作用于整张九宫格

## 这个 skill 的强项

- 不限制题材
- 先做导演分镜逻辑，再做画面生成
- 多页连续能力强，适合长一点的项目
- 适合“先把故事拆顺，再继续生成”的生产流程

## 适用场景

- AI 视频预制作
- 短片分镜
- 广告提案可视化
- MV 或游戏 CG 镜头预演
- 图生视频 / 文生视频前的镜头规划

## 输入与输出

表单入口在 [interface/input.json](./interface/input.json)，适合做可视化调用。

核心输入包括：

- 执行阶段
- 项目信息
- 剧本或故事文本
- 角色资产
- 场景资产
- 道具资产
- 风格和调色参考

输出结构在 [interface/output.json](./interface/output.json)，核心结果包括：

- 全片资产汇总
- 页面规划
- 文字分镜表
- 九宫格分镜结果
- 生图提示词结果

## 仓库结构

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
    ├── workflows/
    ├── templates/
    ├── rules/
    └── examples/
```

## 你应该先看哪里

- [skill/SKILL.md](./skill/SKILL.md)：skill 入口规则
- [skill/workflows/01_script_to_text_storyboard.md](./skill/workflows/01_script_to_text_storyboard.md)：阶段一
- [skill/workflows/02_text_storyboard_to_grid_prompt.md](./skill/workflows/02_text_storyboard_to_grid_prompt.md)：阶段二
- [skill/rules/continuity_rules.md](./skill/rules/continuity_rules.md)：跨页连续规则

---

<a id="english"></a>

## English

`3x3 Storyboard Skill` turns scripts and visual assets into a two-stage storyboard workflow:

1. build a full text storyboard for the whole project
2. convert each `15-second` page into one fixed `3x3` storyboard grid

Use it when you want a practical pre-production flow instead of loose moodboards.

## Best for

- short films
- ads
- MVs
- game cinematics
- AI video planning
- multi-page storyboard generation with continuity

## Fast path

```text
/start -> /asset -> send script/assets -> /storyboard -> /grid -> /next
```

## Main outputs

- full text storyboard
- one `3x3` grid per page
- cross-page continuity
- prompt-only mode for downstream image generation

## Key files

- [skill/SKILL.md](./skill/SKILL.md)
- [interface/input.json](./interface/input.json)
- [interface/output.json](./interface/output.json)

## License / 许可

仓库内如有单独许可文件，以仓库实际文件为准；如无，请按你的发布策略补充。
