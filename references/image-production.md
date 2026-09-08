# Image Production

此模块集中图片生产、Prompt 与验收。输入路由、Gate、阶段顺序、项目目录与最终交付见 [SKILL.md](../SKILL.md)。图片视觉规范以 [visual-language.md](visual-language.md) 为唯一职责源；本文件中为实际生产、Prompt 与 QA 保留的视觉内容是 implementation mirror，如与其冲突则以 `visual-language.md` 为准。本文件不重新定义 Gate 状态机；Gate 状态以 `SKILL.md` 为准。

## Phase 2：生成并确认尾帧

隐喻确认后，先写自包含的 `<item>/visual-spec.json`，再写 imagegen prompt。写入 `<item>/visual-spec.json` 前，所有模板变量必须完全物化；`reference_subject_provided` 必须写为合法 JSON boolean literal `true` 或 `false`，不得写成 `"true"`、`"false"` 或 `{{reference_subject_provided}}`。不得将任何其他模板占位符写入实际 JSON；实际写入完成后必须能够被标准 JSON parser 正常解析。

任何真正发送给 `image_gen` 或写入最终 `<item>/video-prompt.txt` 的 Prompt 都必须已经完全物化。实际执行 Prompt 中禁止存在 `{{...}}`、`[expand ...]`、`[一句话视觉命题]`、`[specified base]`、`[the relationship ...]`、`[directly expand ...]`、任何其他模板占位符、内部 fallback 选择说明、要求下游模型读取 `brief.md` / `visual-spec.json` 或其他 Skill 内部文件路径依赖；模板本身可以继续保留这些 placeholder 作为 authoring notation。

### Prompt 变量来源与物化

| 要物化的内容 | 唯一来源 | 写入方式 |
|---|---|---|
| 核心意思、情绪、视觉命题、完成态关系、visual groups、主次、位置、留白、背景方向、Approved Palette、component-level 颜色与 assembly order | `<project>/brief.md` 中与当前 item stable item identity 对应的 state | 只读取当前 item；按当前 item 的已确认内容完整展开，不重新设计或跨 item 复用。 |
| 主体类型、主体风格、身份与连续性、palette、component colors、group 内动作与 item-level production plan | 当前 `<item>/visual-spec.json` 的已物化值 | `subject.type` 为 `none` 时省略整个主体段；为 `human` 或 `nonhuman` 时完整写入对应主体段。 |
| Image 1、Image 2 与保留的基础结构 | 当前 item 的 `first-frame.png`、已确认 `last-frame.png` 与用户明确指定的 retained base | 视频 Prompt 只把 Image 1 和 Image 2 作为供给图像；首帧编辑 Prompt 只把已确认 `last-frame.png` 作为编辑输入。 |

表中的来源只用于 Skill 内部物化；实际 Prompt 必须只包含最终展开后的内容，不得提及这些文件、字段名或物化过程。

### Visual spec

```json
{
  "script_meaning": "",
  "emotion": "",
  "visual_metaphor": "",
  "style_signature": "背景使用暖中性色纸面作为统一底板，带轻微纸纤维与旧纸颗粒感。整体必须呈现“剪贴主体（如有）+ 卡纸场景”的分层拼贴，不得收敛成统一绘画空间或统一 3D 空间。",
  "aspect_ratio": "9:16",
  "background": {
    "surface": "背景使用暖中性色纸面作为统一底板",
    "paper_grain": "带轻微纸纤维与旧纸颗粒感"
  },
  "subject": {
    "type": "{{subject_type}}",
    "reference_subject_provided": {{reference_subject_provided}},
    "reference_subject_source": "",
    "style": "{{resolved_subject_style_or_empty}}",
    "identity_and_continuity": "{{resolved_subject_identity_and_continuity_or_empty}}"
  },
  "non_character_components": {
    "material": "除具面部主体外，其它主要组件采用彩色卡纸浮雕图形语言。色块保持纯粹、明确，轮廓简洁，具有清晰的裁切边缘、纸张厚度与实体卡纸质感。组件可以通过叠层、折叠、错位和高低关系形成浮雕式结构，但不得退化为写实材质、光滑 3D 或统一绘画元素。彩色卡纸组件与整体压暗的黑白照片剪贴主体（如有）形成强烈的色彩、明度与材质反差。",
    "approved_palette": {
      "name": "",
      "colors": []
    },
    "color": "每个非主体 component 都必须具有明确、稳定的实体卡纸颜色。同一个 primary visual group 内的非主体 components 只允许使用当前 Approved Palette 中的 1–2 种颜色，其中 1 种为主色；如使用第 2 种颜色，则只能作为少量点缀，不得大面积使用，也不得与主色平均分配视觉重量。不同 visual groups 可以重复使用同一主色；当图片包含超过 3 个 visual groups 时不得新增颜色，所有非主体 components 始终限制在当前选定的三色 palette 内。具面部主体保持黑白照片剪贴语言，不计入非主体组件颜色数量限制。"
  },
  "shadow": "所有前景元素必须在底板纸面上留下方向统一、偏移明显、几乎没有柔化的强烈清晰硬投影。投影必须让具面部主体（如有）与卡纸组件呈现出被垫高、悬起或叠放在纸面上的浮雕效果，用于强化纸片厚度、前后层次与实体摆拍感；不得使用柔和、扩散或仅作为轻微接触阴影的处理。",
  "primary_visual_groups": [
    {
      "name": "",
      "role": "当前 item 已确认的主次 / visual role",
      "components": [],
      "component_colors": {
        "<non-character component name>": "<exact hex from approved_palette.colors>"
      },
      "placement": "",
      "relationship": "",
      "assembly_motion": ""
    }
  ],
  "composition": {
    "layout": "构图采用平面拼贴式空间组织，不受真实透视与真实尺度约束。主体（如有）与组件可根据叙事重点自由放大、缩小和错位排列，允许通过明显的大小差建立主次；元素的大小、位置与组合关系优先服从视觉层级、图形关系与文案隐喻关系。主体采用黑白照片剪贴语言，除主体外的其它组件采用彩色卡纸浮雕语言，两者必须保持不同的材质层级与视觉语法。整体必须呈现“剪贴主体（如有）+ 卡纸场景”的分层拼贴，不得收敛成统一绘画空间或统一 3D 空间。",
    "negative_space": "所有前景组件，包括主体（如有）与非主体 visual groups，连同其形成的投影视觉占用区域，整体不得超过纸面约 60%；至少约 40% 的纸面应保持可见。",
    "visual_hierarchy": "画面必须建立明确的主次层级，确保视觉重点清晰。主要主体（如有）、核心组件与辅助组件之间应通过尺度、位置、叠层和留白形成明确层级，不得平均分配视觉重量。辅助元素不得抢夺主要视觉重点。"
  },
  "motion_plan": "{{resolved_item_level_production_plan}}",
  "avoid": "readable text, letters, numbers, subtitles, logos, watermark, UI, photoreal non-character components or environments, glossy 3D, unified painting space, unified 3D space"
}
```

`<item>/visual-spec.json` 必须从 `<project>/brief.md` 中与当前 item stable item identity 对应的 state 读取 Gate 1 confirmed state。`subject.type` 必须为 `none`、`human` 或 `nonhuman`；`none` 时 `subject.style` 与 `subject.identity_and_continuity` 为空，且实际 Prompt 不写入主体要求，其余类型必须分别完全物化当前 item 的对应主体风格、身份和连续性要求。`reference_subject_source` 在 `none` 或没有 reference 的 `human` / `nonhuman` 时必须为空且 `reference_subject_provided` 为 `false`；有 reference 时必须为 `true`，并指向当前 item 实际关联的 managed reference，不允许 placeholder。`primary_visual_groups[].role` 保存当前 item 已确认的主次 / visual role；`component_colors` 以 `components` 中每个非主体 component 的名称为 key、其已确认的 Approved Palette HEX 值为 value；主体不写入该对象。`composition.layout`、`composition.negative_space` 与 `composition.visual_hierarchy` 保存当前 item 已确认的构图方向、负空间 / breathing zone direction 与层级关系。Gate 1 `assembly order` 是用户确认的 item-level narrative assembly order；`primary_visual_groups[].assembly_motion` 是单个 visual group 内 components 的具体进入、连接或放置动作；`motion_plan` 将 confirmed Gate 1 assembly order 与 group-level assembly motion 组合为 item-level production plan；`resolved assembly sequence` 是最终完全物化后直接写入 `video-prompt.txt` 的具体动作序列。后一级必须继承前一级，不得重新设计已确认的 assembly order。Gate 2 必须继承这些 confirmed values，不得重新设计已确认的主次关系、填掉留白或主动增加 groups。只有 Gate 1 没有提供更具体顺序时，才使用“基础结构 → 主体或关键卡片 → 连接件 → 动作 → 最终结果”作为 fallback。

`primary_visual_groups` 必须完整继承 Gate 1 已确认的组；组数与 components 归属由 [SKILL.md 的 Gate 1](../SKILL.md#gate-1隐喻确认) 定义，这里不得为了凑模板数量自行增删。

### 色彩规则

从当前 item 的 confirmed state 继承背景、Approved Palette 与 component-level 用色；颜色的适用范围、组内主色 / 点缀色限制与跨组复用统一见 [visual-language.md §1、§3](visual-language.md)。

### 代表性 Gate 2 尾帧 Prompt 模板

使用 Codex 内置 `image_gen` 工具。可遵守 imagegen skill 的工具调用规范，但当前 eian-collage-broll 中与视觉目标、构图、素材内容及审批流程有关的规则优先。

当 `reference_subject_provided = true` 时，生成 Gate 2 candidate 必须同时使用完整物化后的 imagegen prompt，并把 `reference_subject_source` 指向的、当前 item 实际关联的 managed reference image 作为 `image_gen` 的真实 image input。禁止只在文字 Prompt 中写“preserve identity”而不传入该图片；也禁止让下游模型自行读取 `brief.md`、`visual-spec.json` 或文字形式的文件路径。不得使用其它 item 的 reference。

```text
Asset type: completed last frame for a 9:16 image-to-video paper-collage B-roll clip.
Goal: Create the exact completed last frame of a finished editorial paper-collage image expressing [fully expand the current item's confirmed one-sentence visual proposition]. This is the final completed frame for a later assemble-from-empty video, not a standalone illustration.
Finished relationship: [fully expand the one relationship that must make the confirmed visual metaphor understandable at a glance].
Mood: [fully expand the current item's confirmed emotion as a visual feeling without adding a new narrative element].
Background: 背景使用暖中性色纸面作为统一底板，带轻微纸纤维与旧纸颗粒感。Background direction: [fully expand the current item's confirmed background direction within that paper base].
Subject: [when the current item's subject.type is human or nonhuman, fully expand its resolved subject style and identity-and-continuity requirements; when subject.type is none, omit this entire Subject line and do not introduce a face-bearing subject].
Palette: Use the current item's confirmed Approved Palette only: [fully expand the selected palette name and its three exact colors]. Preserve the confirmed component-level color assignment for every non-character component.

Non-character components: 除具面部主体外，其它主要组件采用彩色卡纸浮雕图形语言。每个非主体 component 必须使用当前 item 已确认的明确实体卡纸颜色。同一个 primary visual group 内的非主体 components 只允许使用当前 Approved Palette 中的 1–2 种颜色，其中 1 种为该组主色；如使用第 2 种颜色，则只能作为少量点缀，不得大面积使用，也不得与主色平均分配视觉重量。不同 visual groups 可以重复使用同一主色；当 visual groups 超过 3 个时不得增加新颜色。具面部主体保持黑白照片剪贴语言，不计入非主体组件颜色限制。色块保持纯粹、明确，轮廓简洁，具有清晰裁切边缘、纸张厚度与实体卡纸质感。组件可以通过叠层、折叠、错位和高低关系形成浮雕式结构，但不得退化为写实材质、光滑 3D 或统一绘画元素。

Composition/framing: Use the following confirmed visual groups for this item: [fully expand every current item primary_visual_groups entry with name, role, components, component_colors, placement, and relationship]. Negative space / breathing zone: [fully expand the current item's confirmed negative-space and breathing-zone direction]. 构图采用平面拼贴式空间组织，不受真实透视与真实尺度约束。主体（如有）与组件可根据叙事重点自由放大、缩小和错位排列，允许通过明显的大小差建立主次；元素的大小、位置与组合关系优先服从视觉层级、图形关系与文案隐喻关系。主体采用黑白照片剪贴语言，除主体外的其它组件采用彩色卡纸浮雕语言，两者必须保持不同的材质层级与视觉语法。整体必须呈现“剪贴主体（如有）+ 卡纸场景”的分层拼贴，不得收敛成统一绘画空间或统一 3D 空间。画面必须建立明确的主次层级，主要主体（如有）、核心组件与辅助组件之间应通过尺度、位置、叠层和留白形成明确层级，不得平均分配视觉重量，辅助元素不得抢夺主要视觉重点。所有前景组件，包括主体（如有）与非主体 visual groups，连同其形成的投影视觉占用区域，整体不得超过纸面约 60%；至少约 40% 的纸面应保持可见。
Shadow: 所有前景元素必须在底板纸面上留下方向统一、偏移明显、几乎没有柔化的强烈清晰硬投影。投影必须让具面部主体（如有）与卡纸组件呈现出被垫高、悬起或叠放在纸面上的浮雕效果，用于强化纸片厚度、前后层次与实体摆拍感；不得使用柔和、扩散或仅作为轻微接触阴影的处理。
Avoid: no readable text, no letters, no numbers, no subtitles, no logos, no watermark, no UI, no glossy 3D, no photoreal environment.
```

#### 主体段落的完全物化示例

以下示例只说明主体段落如何完成物化；它们不是新的视觉规则，也不能替代当前 item 的 confirmed state。

- `none`：删除整个 `Subject:` 行，不以“无人物”“无主体”或其他替代文字填入，也不在图中自行加入具面部主体。
- `human`：`Subject: One continuous, overall-darkened black-and-white photo-collage human subject with irregular hand-torn edges, photo-montage, cut-paper layering, and halftone print characteristics. Preserve recognizable identity and continuous body connections; visible hands connect to arms and visible feet connect to legs. Do not use separated paper-doll body parts.`
- `nonhuman`：`Subject: One recognizable, overall-darkened black-and-white photo-collage dog subject with irregular hand-torn edges, photo-montage, cut-paper layering, halftone print characteristics, recognizable facial and species traits, gray halftone fur treatment, and one continuous natural canine body. Preserve its identity and natural body connections; do not apply human anatomy or separated paper-doll body parts.`

### 尾帧 QA

对照当前 item 的 Gate 1 confirmed state、`<item>/visual-spec.json` 与实际输出逐项检查：

- 视觉命题与完成态关系是否一眼可理解，构图是否服务命题，不强制居中。
- confirmed visual groups、必要 components、各组主次 / visual role、关键 relationships 是否完整且有明确归属。
- placement / composition、breathing zone、背景及主色 / 辅助色方向是否继承；允许自然实现差异，不要求精确像素坐标，不得重新设计主次、填掉留白或主动增加 groups。
- 输出是否为 9:16，`subject.type` 是否与当前 confirmed state 一致。
- `reference_subject_provided = true` 时，是否实际对照当前 item 关联的 managed reference；人类身份核心特征、非人主体或物种关键特征是否保留，黑白化、halftone、裁切与比例变化是否导致明显身份漂移。
- 按 [visual-language.md](visual-language.md) 的全部视觉条款检查：§1 纸面；§2 主体风格、身份与自然形体连续性及 `none` 分支；§3 非主体组件材质、Approved Palette、逐组件颜色、组内主色 / 点缀色与跨组复用；§4 硬投影；§5 平面拼贴空间；§6 层级与前景连同投影约 60% / 可见纸面至少约 40%；Content Restrictions。三色与非写实材质限制适用于非主体组件，不把黑白照片主体或暖中性背景误纳入三色限制。
- 非主体 component 的实体卡纸颜色是否与当前 item 已确认的 visual spec 一致；同批是否统一设计语言。

上述 confirmed-state 或视觉规范检查失败即为 FAIL；FAIL candidate 不得成为 `last-frame.png`。QA 结论写入 `<project>/gate2-qa.md`。通过 QA 后，按 [SKILL.md 的 Gate 2](../SKILL.md#gate-2尾帧生成与确认) 执行 candidate 版本保存、provenance 绑定、contact sheet 编号与展示、用户确认和正式尾帧晋升；该入口统一定义版本与状态字段，不在本模块重复维护。

## Phase 3：生成素材包

阶段触发、逐 item 自动推进与完成状态按 [SKILL.md 的 Phase 3](../SKILL.md#phase-3生成素材包) 执行；本节负责首帧编辑、QA 和视频 Prompt 物化。

### 代表性默认空白首帧编辑 Prompt 模板

使用已确认的 `<item>/last-frame.png` 作为 Codex `image_gen` 的编辑输入，执行前景移除式编辑。这是 removal / edit task，不是 redesign task。

默认情况下，`first-frame.png` 必须与定稿尾帧保持完全一致的 9:16 画布、实际存在的背景纸面状态、纸面质感和纸张颗粒，但移除所有具面部主体、物件、卡片、连接件、图形组件、结果元素以及所有后续需要 assemble-from-empty 出现的拼贴组件，并同时移除这些前景组件产生的所有 cast shadows；被遮挡的背景纸面必须补全，最终只留下空白纸面背景，不得有 ghost shadow、shadow silhouette 或其他移除痕迹。

```text
Task: Edit the supplied confirmed completed last frame into the exact default empty first frame for its assemble-from-empty video.
Source image: Use the supplied confirmed completed last frame only; this is a removal/edit task, not a redesign task.
Remove: Every foreground collage component that will assemble during the video—every face-bearing subject, object, card, connector, graphic component and result element—together with every cast shadow those removed components create.
Preserve: The exact 9:16 canvas and the same existing warm-neutral paper background surface, subtle paper fiber and old-paper grain state.
Reconstruct: Every paper-background area occluded by removed components or their shadows, matching the same existing paper surface.
Output: An empty paper background with no foreground component, ghost shadow, shadow silhouette or removal artifact.
Do not: Redesign, replace, regenerate, flatten into a uniform HEX color, or otherwise alter the confirmed background.
```

### 代表性 retained-base 首帧编辑 Prompt 模板

```text
Task: Edit the supplied confirmed completed last frame into the legal retained-base first frame for its assemble-from-empty video.
Source image: Use the supplied confirmed completed last frame only; this is a removal/edit task, not a redesign task.
Retain exactly: [fully expand the user-approved retained base], including its existing form, placement, scale, material, color / visual treatment and its own direction-unified, clearly offset, nearly undiffused, strong, crisp hard shadow. Do not alter, redraw, replace, move, scale or regenerate it.
Remove: Every other foreground collage component that will assemble during the video—every face-bearing subject, object, card, connector, graphic component and result element—together with every cast shadow those removed components create.
Preserve: The exact 9:16 canvas and the same existing warm-neutral paper background surface, subtle paper fiber and old-paper grain state.
Reconstruct: Every other paper-background area occluded by removed components or their shadows, matching the same existing paper surface.
Output: Only the user-approved retained base and its own approved hard shadow on the preserved paper background, with no other foreground component, ghost shadow, shadow silhouette or removal artifact.
Do not: Add another retained structure, redesign the background, or change the retained base.
```

禁止从零重新生成相似背景、重新设计背景、只依据 HEX 值创建单一均匀色面的首帧或改变背景视觉语言。这里的背景是指确认尾帧中实际存在的同一暖中性色纸面底板、轻微纸纤维与旧纸颗粒感；不得将其重新变为缺乏纸张纹理与材质感的旧式背景。被前景遮挡的背景区域可以补全，但必须延续同一纸面视觉状态。

只有用户明确要求视频不要从完全空白开始时，才允许首帧预留一个基础结构或基础物件；不得由模型自行决定保留结构，不得新增其他例外。

### 首帧自动 QA

首帧生成后自动检查：

默认空白首帧时：

- 是否只剩背景纸面
- 是否残留具面部主体、物件、卡片、连接件、图形组件、结果元素或其他本应后续 assemble-from-empty 出现的组件
- 是否残留已移除组件对应的 cast shadow、ghost shadow、shadow silhouette 或其他 removal artifact

如果用户明确要求保留一个基础结构或基础物件时：

- 允许该用户明确指定的基础结构或基础物件存在
- 允许该指定基础结构或基础物件自身保留方向统一、偏移明显、几乎没有柔化的强烈清晰硬投影
- 该 retained base 是否没有发生明显视觉漂移，包括 form、placement、scale、material、color / visual treatment 及其自身方向统一、偏移明显、几乎没有柔化的强烈清晰硬投影；不得将其重绘、替换、移动、缩放或重新设计
- 除明确允许保留的内容及其自身自然物理投影之外，其余后续需要 assemble-from-empty 出现的组件及其 cast shadows 仍必须移除
- 不得因为存在用户明确要求保留的基础结构而判定 QA 失败

两种首帧均须检查：

- 是否出现明显修补异常
- 背景纸面状态是否明显漂移
- 纸面质感是否仍与尾帧一致
- 是否仍保持 9:16

明显失败时，自动以同一确认尾帧重新编辑首帧。此 QA 不新增用户审批 Gate。

### 代表性约 5 秒视频 Prompt 模板

将每个 item 的完整提示词写入 `<item>/video-prompt.txt`。它是给下游视频生成使用的正式交付物，不在 Skill 内部执行。

生成 `video-prompt.txt` 前，按本文件 [Visual spec](#visual-spec) 定义的 assembly order → assembly_motion → motion_plan → resolved assembly sequence 物化顺序执行；fallback 仅在该处定义。默认空白与 retained-base 的起始状态须按当前 item 实际情况展开，Prompt 明确写出 Image 1 是空白纸面还是保留了用户指定的基础结构。

```text
Clip: Paper-collage stop-motion assembly in one continuous, locked-off, approximately five-second vertical shot.
Supplied frames: Use Image 1 as the exact supplied starting first frame and Image 2 as the exact confirmed completed last frame.
Opening: Open by exactly matching Image 1, preserving its exact warm-neutral paper surface, subtle paper fiber, old-paper grain, background state and any explicitly allowed retained base structure. Do not reconstruct, reduce, alter, redesign or regenerate Image 1. If a retained base exists, keep it exactly as it appears in Image 1. Image 2 remains the exact completed end state.
Assembly: [fully expand the current item's resolved assembly sequence, including each actual primary visual group's related components, their entry order, each slide-in, snap-into-place, physical placement, connection, action and final result]. For a retained-base start, keep the approved retained base fixed and assemble only the remaining components from Image 1's current state. End at, and hold, the supplied Image 2 composition.
Background: Preserve the exact confirmed visual treatment of Image 1 and Image 2: 背景使用暖中性色纸面作为统一底板，带轻微纸纤维与旧纸颗粒感。
Visual mood: [fully expand the current item's confirmed emotion as a visual feeling without adding a new narrative element].
Subject: [when the current item's subject.type is human or nonhuman, fully expand its resolved subject style and identity-and-continuity requirements and preserve them exactly from the confirmed last frame; when subject.type is none, omit this entire Subject line and do not introduce a face-bearing subject].
Palette: The confirmed non-character palette for this item is [fully expand the selected Approved Palette name and its three exact colors]. Component colors: [fully expand every non-character component name and its confirmed exact color]. Preserve these exact assignments. 每个非主体 component 必须保持其已确认的实体卡纸颜色。同一个 primary visual group 内只允许使用当前 palette 中的 1–2 种非主体组件颜色，其中第 2 种只能作为少量点缀，不得扩大面积、取代主色或重新分配颜色。不同 visual groups 可以重复使用同一颜色，不得产生 palette 之外的新颜色。
Material and shadow: 除具面部主体外，其它主要组件采用彩色卡纸浮雕图形语言。色块保持纯粹、明确，轮廓简洁，具有清晰裁切边缘、纸张厚度与实体卡纸质感。组件可以通过叠层、折叠、错位和高低关系形成浮雕式结构。所有前景元素必须保持方向统一、偏移明显、几乎没有柔化的强烈清晰硬投影。
Layout lock: [fully expand the current item's confirmed background direction, placement / composition direction, and negative-space / breathing-zone direction]. Composition: 构图采用平面拼贴式空间组织，不受真实透视与真实尺度约束。整体必须保持“剪贴主体（如有）+ 卡纸场景”的分层拼贴，不得收敛成统一绘画空间或统一 3D 空间。保持确认尾帧的主次层级。所有前景组件，包括主体（如有）与非主体 visual groups，连同其形成的投影视觉占用区域，整体不得超过纸面约 60%；至少约 40% 的纸面保持可见。Do not redesign the visual style, palette, hierarchy or final composition.
Do not: Use scene cuts, camera movement, zoom, morphing, fades, dissolves, opacity-based appearance, new objects, readable text, letters, numbers, subtitles, logos, watermark, UI or sound. Every appearing component must use a specific slide-in, snap-into-place, physical placement, connection or mechanical stop-motion action; never use opacity to make an element appear.
```

每条 prompt 都必须明确：默认情况下 Image 1 是对应空白首帧；用户明确要求保留基础结构时，Image 1 是对应的合法起始首帧。Image 2 是已确认定稿尾帧；不能只写“画面逐渐组装”。最终构图必须贴近 Image 2，不允许下游视频生成自由重新构图、改造主体关系、增加新物件或重新设计最终状态。

### 保存与交付素材包

按 [SKILL.md 的 Phase 3 与默认交付](../SKILL.md#phase-3生成素材包) 保存三项正式素材并写入完成状态；重复确认的幂等规则与跨 item 隔离同样以该入口为准。

### 常见问题

- 首帧残留组件或背景修补异常：自动基于同一确认尾帧重新编辑，不新增审批 Gate
- 组装感弱：在 `video-prompt.txt` 中把已确认 actual primary visual groups 内相关组件的 slide in / snap into place 顺序写得更明确
- 尾帧构图或假字有问题：在 Gate 2 重新生成尾帧并重新确认；不要用视频提示词修补尾帧
