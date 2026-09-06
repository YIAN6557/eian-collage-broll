---
name: eian-collage-broll
description: 将单段、多段或完整定稿口播文案制作成高级 editorial halftone paper-collage / 半调纸拼贴 B-roll 视频素材包。用户说“collage b-roll”“纸拼贴 b-roll”“半调拼贴”“拼贴风格配画面”“用这段文稿做拼贴动画”“eian-collage-broll”，或希望把口播转成拼贴视觉隐喻时，必须使用此 skill。采用 Gate 1 / Gate 2 两阶段审批，最终为每个 item 交付默认空白首帧、已确认定稿尾帧和完整视频生成提示词。
---

# eian-collage-broll

将单段、多段或完整定稿口播压成一个或多个 sharp visual idea，再交付面向约 5 秒、9:16 纸拼贴视频生成的素材包。

每个 item 的正式交付固定为：

1. 默认空白首帧 `first-frame.png`
2. 已确认定稿尾帧 `last-frame.png`
3. 完整视频生成提示词 `video-prompt.txt`

默认链路：

1. 文案输入与路由
2. Gate 1
3. Gate 2
4. Phase 3 生成正式素材包
5. Project Cleanup

Gate 1 和 Gate 2 是工作流中的两个审批闸门。尾帧确认后自动完成首帧、提示词和素材包。默认空白首帧；仅当用户明确要求时，可按本 Skill 规则保留基础结构。

## 环境要求

Gate 1 不依赖图片生成能力。Gate 2 需要可用的图片生成能力；在 Codex 环境中默认使用内置 `image_gen` 生成尾帧，并以定稿尾帧作为编辑输入生成默认空白首帧。

## 唯一职责源

- `SKILL.md` 负责何时触发、输入后的状态机、Gate / Phase、approval、item 推进、最终 delivery 与 Project Cleanup 触发。
- [broll-candidate-extraction.md](references/broll-candidate-extraction.md) 负责 Type A / Type B / Type C、Candidate Extraction、分段复核、原文边界与用户选择。
- [references/visual-language.md](references/visual-language.md) 是最终图片外观的 single source of truth。
- [references/image-production.md](references/image-production.md) 负责 visual spec、imagegen prompt、尾帧生产与 QA、contact sheet、confirmed last frame、首帧编辑与 QA，以及 `video-prompt.txt`。
- [references/cleanup.md](references/cleanup.md) 负责当前项目完成后的 Project Cleanup 规则。

`README.md`、`agents/openai.yaml` 与 `evals/evals.json` 只作为摘要、调用入口或行为测试。为了 prompt 执行或 QA 保留的重复内容是 implementation mirror；若与对应职责源冲突，以上述职责源为准。

## Gate 1 前置输入路由

进入 Gate 1 前，先按照 [broll-candidate-extraction.md](references/broll-candidate-extraction.md) 判断输入类型及每个待制作单元是否已经适合作为独立 B-roll 制作单元。

- **Type A：单段约 5 秒口播**：符合独立制作单元条件时直接进入 Gate 1；如内部实际包含多个能够独立成立的主要语义或视觉命题，则按该文件执行分段复核，并由用户决定保留原段、全部建议分段或部分建议分段进入 Gate 1。
- **Type B：用户已经拆好的多段口播**：先尊重用户原始分段并逐段判断；符合条件的原段直接进入 Gate 1，只对不符合条件的原段按该文件执行分段复核，并由用户决定该原段最终如何进入 Gate 1。
- **Type C：完整定稿口播**：按照 [broll-candidate-extraction.md](references/broll-candidate-extraction.md) 执行 B-roll Candidate Extraction，只展示最终候选原文并等待用户选择。只有用户选中的候选进入 Gate 1。

B-roll Candidate Extraction 只负责候选选择和原文边界，不负责正式视觉隐喻设计。

Candidate Selection 或分段确认只决定哪些原文单元进入制作，不是视觉设计 approval gate，也不能替代 Gate 1 的视觉确认。

### Gate 1：隐喻确认

收到文稿后，先提取视觉隐喻，并确定核心意思、情绪、一句话视觉命题、当前 item 的 visual groups 与各组主次 / visual role、placement / composition direction（包括主要 breathing zone）、negative space / breathing zone direction、background direction 与主色 / 辅助色方向，以及预期组装顺序。不生成图片。

向用户交付每条的：

- 核心意思
- 一句话视觉命题
- 当前 item 的页面组件，包括 primary visual groups 及其 components

然后明确停下，等待用户回复“可以”“通过”“全部通过”或给出逐条修改意见。

Gate 1 确认时，当前 item 所选 Approved Palette 与具体 component-level 用色随该视觉方案一并作为 confirmed state 保存；不新增用户展示项或审批 Gate。

每张图片默认包含 3–6 个 primary visual groups，优先使用 3–4 个。一个 primary visual group 可以由多个彼此相关、重复出现、小型或碎片化的 components 共同组成；允许局部形成密集组件群，但这些 components 必须共同服务于同一视觉作用，并具有明确归属和层级。group 数量限制的是主要视觉关系，而不是可见单体物件总数。

如果用户只确认部分编号，只让通过的条目进入 Gate 2；未通过条目继续修改隐喻。

### Gate 2：尾帧生成与确认

Gate 2 只负责尾帧阶段：生成尾帧所需 visual spec 与 imagegen prompt，完成尾帧生成、QA、contact sheet 与用户确认；不生成首帧或视频。

隐喻确认后，流程为：visual spec → imagegen prompt → 生成 Gate 2 candidate → 尾帧 QA → 展示给用户 → 用户确认或要求修改 → 必要时重新生成，直到确认定稿。

通过 QA 的候选尾帧保存为 `<item>/gate2-candidate-v1.png`；重生成时使用 `gate2-candidate-v2.png`、后续递增。当前待确认 candidate 必须在 `<project>/brief.md` 中绑定 `item_id + candidate_version + gate1_revision`；生成后同步更新当前 item state 的 `latest_candidate_version` 与 `candidate_gate1_revision`。生成带编号的 `last-frame-contact-sheet.jpg`，后续版本使用 `last-frame-contact-sheet-v2.jpg`、`v3`、`v4` 并保留旧版；contact sheet 中每张图的标签至少明确 `item identity + candidate version`（例如 `02 · candidate-v3`），向用户展示并再次停下。只有实际展示给用户的版本才能写入 `presented_candidate_version`。

只有属于当前 item、属于当前 `gate1_revision`、已经实际展示给用户且被用户明确确认的 candidate 才允许复制或重命名为 `<item>/last-frame.png`，并写入 `confirmed_candidate_version`。candidate 不等于 confirmed last frame；未确认 candidate 在被用户明确确认前不得成为正式 `last-frame.png`；被明确拒绝或要求修改的 candidate 仅保留用于对比，不再作为正式尾帧候选。如果“上一张”“那张可以”等确认在多个 item、candidate 或 revision 中无法唯一确定，不得猜测，继续停留在 Gate 2。

如果用户只确认部分尾帧，已确认定稿的条目自动进入 Phase 3 生成素材包；未确认条目留在 Gate 2 修改。通过 Gate 2 后自动生成空白首帧和完整视频生成提示词。同一 confirmed candidate 的 `last-frame.png`、`first-frame.png`、`video-prompt.txt` 已齐全且 `phase3_status = completed` 时，重复确认不得重跑 Phase 3；只有用户明确要求修改或重新生成当前 item 时才重新进入对应阶段。

## 成功标准

- 图片视觉规范见 [references/visual-language.md](references/visual-language.md)。
- 默认面向约 5 秒、9:16 视频生成；Skill 最终交付默认空白首帧、确认定稿尾帧和完整视频生成提示词；仅当用户明确要求时，首帧可按本 Skill 规则保留一个基础结构或基础物件。不生成成片文件

## 不适用本 Skill 的情况

- 需要精确控制图层、遮挡、镜头穿越或可编辑时间线，或需要真实人物产品广告、口播演员，或用户明确要求可逐层修改的透明素材：本 Skill 不适用。

## 主体参考图

用户在使用本 Skill 期间可以上传包含清晰面部信息的参考照片；参考主体不要求必须是真人，也可以是动物或其他具有明确面部特征的主体。

每个 production item 的 `subject.type` 必须为 `none`、`human` 或 `nonhuman`：无具面部主体为 `none`；人类主体为 `human`；动物或其他具面部特征的非人主体为 `nonhuman`。

收到参考照片后，先检查是否能够清晰、完整识别面部区域及主要面部特征。若面部缺失、严重遮挡、过小、过度模糊或无法可靠识别，不将该照片设为正式主体参考图，并建议用户更换照片。

通过检查的用户原始上传 reference 保持为用户原始文件，不属于 Skill-managed 文件且绝不由 Skill 删除。仅将其复制为本 Skill 的持久主体参考图：managed copy 必须保存于 `<project>/managed-references/`，位于所有 `<item>/` 目录之外，并在 `<project>/brief.md` 中记录当前 item 对该实际 managed reference 的 association；它持续用于后续需要对应主体身份参考的生成，直到用户明确要求删除或更换。

用户要求更换时，必须先要求用户上传新的参考照片；新照片通过面部检查后，以新的 Skill-managed copy 替换当前 item 的 association，并保持当前 item 的 `reference_subject_provided = true`，只删除旧的 Skill-managed copy 及其 association，不得继续引用、恢复或复用旧 managed copy；绝不删除用户原始上传 reference。

用户明确要求删除主体参考图时，只删除当前 Skill-managed copy 及其持久 association，并将当前 item 的 `reference_subject_provided = false`、当前 reference source / association 置空；删除后，后续生成不得继续使用该 copy，用户原始上传 reference 仍绝对不得删除。

主体参考图属于长期持久化资产，不因单个项目结束或 Project Cleanup 被自动删除。

## 默认项目目录

使用北京时间 `Asia/Shanghai` 命名：

```text
~/eian-collage-broll-projects/YYYY-MM-DD-collage-broll-标题/
```

批量项目推荐结构：

```text
<project>/
├── brief.md
├── managed-references/
├── imagegen-prompts.md
├── gate2-qa.md
├── last-frame-contact-sheet.jpg
├── 01-概念名/
│   ├── visual-spec.json
│   ├── gate2-candidate-v1.png
│   ├── gate2-candidate-v2.png
│   ├── first-frame.png
│   ├── last-frame.png
│   └── video-prompt.txt
├── 02-概念名/
│   ├── visual-spec.json
│   ├── gate2-candidate-v1.png
│   ├── first-frame.png
│   ├── last-frame.png
│   └── video-prompt.txt
└── ...
```

`<project>/brief.md` 是唯一持久状态文件，不允许在 item 目录另建 `brief.md`。每个生产 item 必须具有稳定的 item identity，并与对应 `<item>/` 目录一一绑定；每个 item state 在现有 Gate 1 confirmed fields 基础上保存 `item_id`、`stage`、`gate1_revision`、`latest_candidate_version`、`presented_candidate_version`、`confirmed_candidate_version`、`candidate_gate1_revision` 与 `phase3_status`，以及该 item 的 source script、core meaning、emotion、confirmed visual proposition / visual metaphor、confirmed visual groups 与各组当前 item 的主次 / visual role、confirmed placement / composition direction（包括主要 breathing zone）、confirmed negative-space direction、confirmed background direction、confirmed primary / secondary color direction、confirmed Approved Palette、confirmed component-level color assignment、confirmed assembly order、`subject.type`、`reference_subject_provided` 与当前 item 使用的实际 managed reference source / association（如存在）。首次 Gate 1 确认时 `gate1_revision = 1`；已确认方案发生实际修改并再次确认时 revision 加 1；重复确认、恢复上下文或重新展示不增加 revision。Gate 1 revision 改变后，旧 revision 生成的 candidate 只能保留用于对比，不得成为 `last-frame.png`。如果当前 item 已存在基于旧 Gate 1 revision 完成的 confirmed candidate 或 Phase 3 素材包，则 Gate 1 revision 改变后，这些旧 downstream results 不再视为当前 revision 的有效 confirmed / completed state；旧 `confirmed_candidate_version` 不再代表当前 revision 的有效 confirmed candidate；当前 item 不得继续保持 `phase3_status = completed`；只有新 revision 重新完成 Gate 2 和 Phase 3 后才能再次写为 `completed`。Gate 2 恢复或批量生产时，只能读取 `<project>/brief.md` 中与当前 stable item identity 对应的 state，不得跨 item 读取或复用其它 item 状态。

`last-frame.png` 必须就是 Gate 2 已确认定稿尾帧，不额外制造另一套视频使用尾帧。`first-frame.png` 必须由该定稿尾帧编辑派生；`video-prompt.txt` 是完整视频生成提示词。

## Phase 1：设计视觉隐喻

Gate 1 的视觉方案必须遵守 [references/visual-language.md](references/visual-language.md) 定义的图片视觉语言。Gate 1 确认后，将 confirmed visual groups 与各组当前 item 的主次 / visual role、placement / composition direction（包括主要 breathing zone）、negative-space direction、背景方向、主色 / 辅助色方向、Approved Palette、component-level color assignment、`subject.type`、`reference_subject_provided`、当前 item 使用的实际 managed reference source / association（如存在）与 assembly order 写入 `<project>/brief.md` 中该 item 的 stable item identity state；同时按该 state 的实际阶段更新 runtime fields。该 assembly order 是后续 visual spec 与 `video-prompt.txt` 必须继承的已确认叙事顺序。Gate 1 `assembly order` 是用户确认的 item-level narrative assembly order；`primary_visual_groups[].assembly_motion` 是单个 visual group 内 components 的具体进入、连接或放置动作；`motion_plan` 将 confirmed Gate 1 assembly order 与 group-level assembly motion 组合为 item-level production plan；`resolved assembly sequence` 是最终完全物化后直接写入 `video-prompt.txt` 的具体动作序列。后一级必须继承前一级，不得重新设计已确认的 assembly order。Gate 2 必须继承这些 confirmed composition 结果，不得重新设计已确认的主次关系、填掉留白或主动增加 groups。

## Phase 2：生成并确认尾帧

按 [references/image-production.md](references/image-production.md) 从 `<project>/brief.md` 中与当前 stable item identity 对应的 Gate 1 confirmed state 执行 visual spec、imagegen prompt、candidate 尾帧生成、尾帧 QA、contact sheet 与用户确认；只有 confirmed candidate 才写入 `last-frame.png`。

## Phase 3：生成素材包

此阶段不是审批 Gate。某个 item 的尾帧一经 Gate 2 确认定稿，就自动为该 item 默认生成空白首帧；如果用户明确要求首帧保留一个基础结构或基础物件，则按该例外生成对应首帧。两种情况均继续自动执行首帧 QA、写入完整视频生成提示词并交付素材包；不新增审批 Gate，无需等待同批其他 item。

按 [references/image-production.md](references/image-production.md) 执行确认尾帧 → 首帧编辑 → 首帧 QA → `video-prompt.txt` → 素材包。

只有 confirmed candidate 已成为 `last-frame.png`、`first-frame.png` QA 通过且 `video-prompt.txt` 已完成后，才将当前 item 的 `phase3_status` 写为 `completed`。

当当前项目所有 active items 均完成 Phase 3 后，自动按 [references/cleanup.md](references/cleanup.md) 执行项目级 cleanup。Project Cleanup 是整个流程最后一步。

## 默认交付

向用户交付每条：

- `<item>/first-frame.png`
- `<item>/last-frame.png`
- `<item>/video-prompt.txt`
