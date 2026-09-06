# eian-collage-broll

把单段、多段或完整定稿口播文稿压成一个或多个 sharp visual idea，制作高级编辑风**半调纸拼贴（halftone paper-collage）B-roll 视频素材包**。

Turn voiceover input into a premium editorial paper-collage assemble-from-empty asset package for downstream video generation.

## 效果

- 元素从空场逐件滑入、卡位、组装（stop-motion 质感），不是淡入或慢 zoom
- 每个 item 交付 9:16、约 5 秒纸拼贴视频生成所需的 `first-frame.png`、`last-frame.png` 与 `video-prompt.txt`

完整视觉语言以 [references/visual-language.md](references/visual-language.md) 为准；图片生产、提示词和 QA 的执行规则见 [references/image-production.md](references/image-production.md)。

## 工作流：两闸门审批 + 自动素材包

输入先判断 Type A / Type B / Type C；天然属于单个约 5 秒、语义完整且只有一个主要视觉命题的 Type A 优先于 Type C，直接进入 Gate 1，不执行 B-roll Candidate Extraction；用户已经划分好的 Type B 尊重原有分段并逐段判断：符合独立制作单元条件的原段直接进入 Gate 1；仅不符合条件的原段执行分段复核，并由用户决定该原段最终进入 Gate 1 的原文单元；只有 Type C 执行 B-roll Candidate Extraction，并只让用户选中的原文候选进入 Gate 1。Type C 的 Candidate Selection 只是候选选择检查点，不是视觉审批 Gate。

1. **Gate 1 · 隐喻确认** — 向用户展示核心意思、一句话视觉命题与当前 item 的页面组件（包括 primary visual groups 及其 components），等待确认；内部保存完整视觉方案，不生成图片或视频
2. **Gate 2 · 尾帧生成与确认** — 只负责生成尾帧所需 visual spec 与 imagegen prompt，完成尾帧 candidate、QA、contact sheet 与用户确认；只有明确确认的 candidate 才成为 `last-frame.png`，不生成首帧或视频
3. **自动素材包** — 每个确认尾帧默认自动编辑出空白首帧、执行首帧 QA、写入完整视频生成提示词并交付三项素材；仅当用户明确要求时才可按 Skill 规则保留基础结构

批量模式支持部分通过：每个 item 独立推进；完成素材包的 item 不会因其他 item 的修改而重跑。

## 环境要求

| 依赖 | 说明 |
|------|------|
| Codex 环境 | Gate 2 尾帧生成与空白首帧编辑依赖内置 `image_gen` 工具 |

Gate 1 不依赖图片生成能力。Skill 不调用任何视频生成模型，也不生成成片文件。

## 使用

对你的 agent 说：

```text
collage b-roll：很多人以为 AI 是来替你思考的，其实它更像一面镜子，会把你问题里的漏洞照出来。
```

触发词：`collage b-roll`、`纸拼贴 b-roll`、`半调拼贴`、`拼贴风格配画面`、`eian-collage-broll`。

然后按输入路由 → Gate 1 → Gate 2 → 自动素材包逐步完成即可。批量给多段文稿也可以：符合独立制作单元条件的用户原段保留为独立 item；仅不符合条件的原段进行分段复核，由用户决定保留原段、接受全部建议分段或选择部分建议分段进入 Gate 1。

## 目录结构

```text
eian-collage-broll/
├── README.md
├── LICENSE
├── SKILL.md
├── agents/
│   └── openai.yaml
├── evals/
│   └── evals.json
└── references/
    ├── broll-candidate-extraction.md
    ├── visual-language.md
    ├── image-production.md
    └── cleanup.md
```

## FAQ

**为什么强制两次人工确认？**
Gate 1 先确认视觉隐喻，Gate 2 再确认实际完成态尾帧；这样后续的空白首帧和视频提示词都有明确、稳定的最终目标。

**空白首帧怎么得到？**
默认由确认尾帧进行前景移除式编辑得到，保留同一画布、背景纸面状态和纸面质感；不会从零生成相似背景。只有用户明确要求视频不要从完全空白开始时，才可按 Skill 规则保留一个基础结构或基础物件。

**能交付什么？**
每个 item 固定交付 `first-frame.png`、`last-frame.png` 和 `video-prompt.txt`。
