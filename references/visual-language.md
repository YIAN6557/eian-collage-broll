# Visual Language

This module is the single source of truth for image appearance. Workflow and delivery are defined in `SKILL.md`; `image-production.md` is the implementation mirror for production, prompts, QA, contact sheets, and frame continuity. If an implementation mirror conflicts with this module on image appearance, this module wins.

## 1. 背景 / Background

背景使用暖中性色纸面作为统一底板，带轻微纸纤维与旧纸颗粒感。

## 2. 具面部主体 / Face-bearing Subject

仅当当前 item 的 `subject.type` 为 `human` 或 `nonhuman` 时应用本节；`none` 时不得在画面或 Prompt 中自行新增具面部主体。

具面部主体视觉语言

具面部主体采用整体压暗的黑白照片剪贴风格，具有不规则手工撕剪边缘，呈现照片蒙太奇、剪纸叠层与网点印刷特征，并与彩色卡纸组件形成强烈的色彩、明度与材质反差。`nonhuman` 时保留可辨认的物种或主体特征，其毛发、羽毛、鳞片、皮肤或其他主体表面以黑白照片剪贴与灰网点处理。

主体身份与形体连续性

主体身份必须保持一致；面部可见时，应保留足以识别同一主体的核心面部特征，且拼贴化、网点化、压暗处理、姿势、尺度和形体变化不得造成身份漂移。单张静帧中，主体必须是一张连续、一体化的照片剪贴形体，不得出现肢体或可见部位断开、悬空、错误连接或分体纸偶式拼装；`human` 可使用非写实比例、自由裁切和夸张头身比，但可见手必须连接手臂、脚必须连接腿；`nonhuman` 的可见部位按该主体自身的自然连接关系连续，不套用人类解剖。

主体可按构图层级与叙事需要呈现为全身、局部、背面、头部或面部特写，不要求完整呈现；主体的尺度、取景、裁切和呈现方式均优先服务构图层级与叙事关系。

## 3. 非主体组件 / Non-character Components

### 3.1 质感 / Material & Texture

除具面部主体外，其它主要组件采用彩色卡纸浮雕图形语言。色块保持纯粹、明确，轮廓简洁，具有清晰的裁切边缘、纸张厚度与实体卡纸质感。组件可以通过叠层、折叠、错位和高低关系形成浮雕式结构，但不得退化为写实材质、光滑 3D 或统一绘画元素。彩色卡纸组件与整体压暗的黑白照片剪贴主体（如有）形成强烈的色彩、明度与材质反差。

### 3.2 颜色 / Color

每张图片为非主体组件从以下 Approved Palettes 中选择 1 组三色配色，并在当前图片中保持该配色体系。

每个非主体 component 都必须具有明确、稳定的实体卡纸颜色。

同一个 primary visual group 内的非主体 components 只允许使用选定配色中的 1–2 种颜色：

- 其中 1 种为该组主色，可用于主要组件和较大面积色块；
- 如使用第 2 种颜色，则只能作为少量点缀色，用于局部强调、小型组件或局部区分，不得大面积使用，也不得与主色平均分配视觉重量。

不同 primary visual groups 可以从同一组三色配色中选择不同的主色，因此整张图片可以同时使用该配色中的 3 种颜色，但不得因为单个 group 包含多个 components 而无限增加颜色。

当图片包含超过 3 个 primary visual groups 时，不新增颜色；不同 visual groups 可以重复使用同一主色，所有非主体 components 仍必须限制在当前选定的三色 Approved Palette 内。

具面部主体始终保持黑白照片剪贴语言，不计入非主体组件的颜色数量限制；例如黑白人物或非人主体与红色放大镜可以共同属于同一个 primary visual group。

不得引入当前选定配色之外的额外高饱和颜色，不使用渐变、浑浊综合色或无明确归属的随机色彩。

#### Approved Palettes

1. **Vivid / 焕彩**
   - 金黄色 `#F5C51B`
   - 浅湖蓝 `#00A6C7`
   - 橘红色 `#E84A3C`

2. **Cosmos / 星云**
   - 宝石蓝 `#3668FF`
   - 珊瑚粉 `#FF6F8F`
   - 酸橙绿 `#8FD12E`

3. **Safari / 原野**
   - 草木绿 `#69B53B`
   - 靛青蓝 `#3F5FD8`
   - 杏子橙 `#FFB18A`

4. **Ember / 青焰**
   - 暖柿橙 `#F57C28`
   - 暖姜黄 `#F4C430`
   - 墨湖绿 `#008972`

5. **Zest / 鲜活**
   - 砖红 `#E63946`
   - 湖蓝 `#457B9D`
   - 米白 `#F1FAEE`

6. **Groove / 律动**
   - 深紫 `#6D46F3`
   - 亮青色 `#00B8A9`
   - 杏子橙 `#FF9955`

7. **Carnival / 狂欢**
   - 玫红色 `#FF4F97`
   - 宝蓝色 `#3A5DFF`
   - 柠檬黄 `#F6D034`

8. **Bounce / 跳跃**
   - 柑橘橙 `#FF7B32`
   - 孔雀蓝 `#0088D8`
   - 葡萄紫 `#7C5AF0`

9. **Parade / 巡游**
   - 番茄红 `#F04B37`
   - 薰衣草紫 `#8A67F6`
   - 奶油黄 `#FFD65A`

## 4. 投影 / Shadow

所有前景元素必须在底板纸面上留下方向统一、偏移明显、几乎没有柔化的强烈清晰硬投影。投影必须让具面部主体（如有）与卡纸组件呈现出被垫高、悬起或叠放在纸面上的浮雕效果，用于强化纸片厚度、前后层次与实体摆拍感；不得使用柔和、扩散或仅作为轻微接触阴影的处理。

## 5. 构图与空间逻辑 / Composition & Spatial Logic

构图采用平面拼贴式空间组织，不受真实透视与真实尺度约束。主体（如有）与组件可根据叙事重点自由放大、缩小和错位排列，允许通过明显的大小差建立主次；元素的大小、位置与组合关系优先服从视觉层级、图形关系与文案隐喻关系。主体采用黑白照片剪贴语言，除主体外的其它组件采用彩色卡纸浮雕语言，两者必须保持不同的材质层级与视觉语法。整体必须呈现“剪贴主体（如有）+ 卡纸场景”的分层拼贴，不得收敛成统一绘画空间或统一 3D 空间。

## 6. 视觉层级与画面密度 / Visual Hierarchy & Density

画面必须建立明确的主次层级，确保视觉重点清晰。主要主体（如有）、核心组件与辅助组件之间应通过尺度、位置、叠层和留白形成明确层级，不得平均分配视觉重量。辅助元素不得抢夺主要视觉重点。所有前景组件，包括主体（如有）与非主体 visual groups，连同其形成的投影视觉占用区域，整体不得超过纸面约 60%；至少约 40% 的纸面应保持可见。

## Content Restrictions

Final generated images must contain no readable text, letters, numbers, subtitles, logos, watermarks, or UI.

Avoid glossy 3D and photoreal environments.
