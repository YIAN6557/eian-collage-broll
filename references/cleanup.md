# Project Cleanup

Project Cleanup 是当前项目完成后的自动收尾步骤，不是用户审批 Gate。

## 触发条件

仅当当前项目的 active items 均已明确完成 Phase 3 时执行。active item 与 Phase 3 completion 必须优先从 `<project>/brief.md` 中的 state 验证；不得仅根据目录名、文件数量或猜测推断 active items 或其完成状态。

在开始任何删除前，对全部 active items 一次性完成 preflight：

1. 从 `<project>/brief.md` 验证每个 active item 的 `phase3_status = completed`。
2. 验证每个 active item 均有位于其自身 `<item>/` 目录内的 `first-frame.png`、`last-frame.png` 和 `video-prompt.txt`。
3. 验证两个 PNG 均为可读取的有效 9:16 PNG 正式交付物，`video-prompt.txt` 为可读取且非空的正式提示词。
4. 验证每个待清理文件都位于当前已解析的项目根目录内、不是符号链接，且能明确证明由 Skill 为当前项目生产、QA、对比或调试所生成。

只有 `<project>/brief.md` state 正确且全部正式交付文件通过上述文件级 preflight，才能 cleanup。任一 active item 尚未完成 Phase 3，任一正式交付物缺失、无效或状态无法确认，或任一待删文件归属无法确认时：不执行 cleanup、不做任何部分删除、保留全部中间文件，并报告具体问题。

## 可删除范围

仅在全量 preflight 通过后，删除当前项目内已明确属于该项目、且明确由 Skill 生成的中间文件。包括：

- `<item>/gate2-candidate-v*.png`
- `<item>/visual-spec.json`
- 已记录为 Skill 生成的临时 imagegen prompt
- 已记录为 Skill 生成的 item-level QA 或 temporary notes
- `<project>/last-frame-contact-sheet*.jpg`
- `<project>/gate2-qa.md`
- `<project>/imagegen-prompts.md`，以及其他仅用于生产、QA、对比或调试的已确认 Skill-generated intermediates

不得因为名称相似、位于 item 目录内，或“看起来像中间文件”而删除文件。无法明确判断某个文件是否可删除时，必须 fail-closed：不删除该文件；若该文件会使 active item 目录无法只保留正式交付物，则整个 cleanup 不执行且报告该冲突。

`<project>/brief.md` 是 persistent state，Project Cleanup 永远不得删除。当前有效的 Skill-managed reference 是 persistent asset，必须位于 item 目录之外，Project Cleanup 永远不得删除。用户原始上传 reference 同样绝对不得删除；replace / delete reference 只能由用户明确请求触发，不能由 cleanup 触发。

绝对不得删除用户上传的原始文件、用户提供的外部参考图、不属于当前项目的文件、长期持久化资产，或归属不明的文件。不得跟随符号链接或跨出当前项目根目录。此类受保护文件即使位于当前项目内也必须保留；若它位于 active item 目录内并妨碍该目录达到正式交付物状态，停止整个 cleanup，不删除任何文件。

## 完成状态

cleanup 成功后，每个已清理 active item 目录只保留：

```text
<item>/
├── first-frame.png
├── last-frame.png
└── video-prompt.txt
```

Project Cleanup 自动执行，不新增用户确认、审批或其他 Gate。它是项目流程的最后一步；失败时只报告并保留文件，不请求以 cleanup 为目的的新审批。
