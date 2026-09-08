# eian-collage-broll

[简体中文](README.zh-CN.md)

> A creator-led editorial halftone paper-collage B-roll workflow that translates finished voiceover into vivid visual metaphors and develops them into asset packages ready for downstream motion production.

`eian-collage-broll` is a creative workflow, not an automatic video generator. It turns a locked voiceover passage into an intentional final collage frame, an opening frame that can assemble into it, and a fully specified motion prompt. The creator retains the final aesthetic judgement at both approval gates.

## What it is built to protect

Four creative principles guide every item:

1. **Meaning before imagery.** Start with what the voiceover means and the relationship it needs the viewer to understand—not a generic list of attractive objects.
2. **Composition before motion.** Establish a finished visual relationship, hierarchy, palette, placement, breathing room, and assembly order before describing movement.
3. **Creator judgement stays with the creator.** Gate 1 approves the visual proposition; Gate 2 approves the actual generated end frame. Neither is inferred from a candidate selection, a draft prompt, or an automated check.
4. **Assemble from empty.** The default first frame is the confirmed end frame edited back to its empty paper surface. The downstream motion is a physical paper-collage assembly, not a fade, zoom, or dissolve.

The visual language combines a warm, textured paper ground; crisp, hard shadows; colour card-paper components; and, when appropriate, a black-and-white photo-collage human or nonhuman subject. The appearance rules live in [Visual Language](references/visual-language.md).

## Workflow

```text
Finished voiceover
  → input routing and, when needed, candidate selection
  → Gate 1: creator confirms the visual metaphor and composition
  → Gate 2: creator confirms the generated completed last frame
  → motion-ready asset package for each approved item
  → Project Cleanup after every active item has completed its package
```

Input routing respects the source material. A self-contained passage with one visual proposition can enter Gate 1 directly; multi-part or full voiceover can be reviewed and selected as contiguous source passages first. Selection decides *which* source units are made. It is not a substitute for Gate 1's creative approval. See [B-roll Candidate Extraction](references/broll-candidate-extraction.md).

### Gate 1 — visual metaphor and composition

Before an image is made, the creator reviews each proposed item's core meaning, one-sentence visual proposition, and page components (including primary visual groups and their components). The confirmed design also holds the hierarchy, placement, breathing zone, background direction, approved palette, component colour assignments, and assembly order.

Gate 1 is where the work stays conceptually honest: a strong collage is designed from the sentence's meaning, rather than retrofitted around an image.

### Gate 2 — completed last frame

After Gate 1, the workflow produces and quality-checks completed-frame candidates and presents a labelled contact sheet. Only a candidate the creator has actually reviewed and explicitly confirmed becomes `last-frame.png`. A candidate is not a deliverable merely because it passed a check or looks plausible.

### Motion-ready asset package

For every item whose last frame is confirmed, Phase 3 automatically creates these exact deliverables:

| File | Role |
| --- | --- |
| `first-frame.png` | The default empty opening frame, edited from the confirmed last frame while preserving its real paper background. A creator may explicitly request one legal retained base instead. |
| `last-frame.png` | The creator-confirmed, completed collage end frame. |
| `video-prompt.txt` | A fully materialized prompt for a downstream image-to-video system, including the confirmed assembly sequence and final-composition constraints. |

This Skill produces the assets for downstream motion generation. It does **not** render or deliver a finished video file.

### Project Cleanup — once, at project level

`Project Cleanup` is the last workflow step, not an approval gate and not a per-item action. It runs only after **all active items** have completed their asset packages and a project-wide preflight has verified their three deliverables. It then removes only proven Skill-generated intermediates; persistent project state, final deliverables, managed references, user originals, unknown files, and protected files remain untouched. The complete fail-closed policy is in [Project Cleanup](references/cleanup.md).

## A short walkthrough

**Finished voiceover**

> “Alignment begins when scattered opinions become one workable plan.”

**Gate 1 proposition**

> Loose paper fragments representing competing viewpoints travel across a tilted blueprint and lock into one clear route: scattered opinions become a shared plan.

The creator reviews the proposed visual groups, palette, open paper space, and physical assembly order, then confirms or revises the proposition. Nothing is generated at this point.

**Gate 2 outcome**

The workflow presents completed-frame candidates: a warm paper field, a tilted blueprint, and coloured paper fragments snapped into a single route. The creator confirms the reviewed end frame.

**Delivery**

- `first-frame.png` — the same paper field before the blueprint and fragments arrive.
- `last-frame.png` — the confirmed blueprint-and-route composition.
- `video-prompt.txt` — the concrete sequence for fragments sliding in, snapping onto the blueprint, and holding on the confirmed final frame.

The result is a motion-ready package, not a generated clip.

## Showcase final frames

<table>
  <tr>
    <td width="50%"><img src="assets/readme/01-sorting-work.png" alt="Completed paper-collage frame of a creator sorting work between blue and yellow document stacks." width="100%"></td>
    <td width="50%"><img src="assets/readme/02-automated-flow.png" alt="Completed paper-collage frame of an automated production flow moving toward a finished composition." width="100%"></td>
  </tr>
  <tr>
    <td width="50%"><img src="assets/readme/03-reusable-skill.png" alt="Completed paper-collage frame of a reusable Skill producing a series of visual outputs." width="100%"></td>
    <td width="50%"><img src="assets/readme/04-materials-to-motion.png" alt="Completed paper-collage frame of materials transforming into a sequence of motion frames." width="100%"></td>
  </tr>
</table>

## Installation

For a first-time install, when `~/.codex/skills/eian-collage-broll` does not already exist, clone the private repository into Codex's local Skills directory:

```bash
git clone https://github.com/YIAN6557/eian-collage-broll.git ~/.codex/skills/eian-collage-broll
```

For an installation created from that clone, update it with:

```bash
cd ~/.codex/skills/eian-collage-broll
git pull
```

After either installation or update, start a new Codex task or reload the relevant Codex session before invoking the Skill.

## Getting started

### Requirements

- A finished voiceover passage, user-separated passages, or a complete locked voiceover. The Skill keeps selected source passages contiguous and does not rewrite the voiceover into a new script.
- The creator's availability to make the Gate 1 and Gate 2 decisions.
- An environment with image generation for Gate 2. In Codex, the workflow uses the built-in `image_gen` capability for completed-frame generation and first-frame editing. Gate 1 does not require image generation.

### Invoke it in natural language

Give your agent the finished voiceover and name the desired workflow, for example:

```text
collage b-roll：当大家都说“对齐”时，真正的工作，是把散落的观点拼成一张能执行的蓝图。
```

The Skill also responds to phrases such as `纸拼贴 b-roll`, `半调拼贴`, `拼贴风格配画面`, `用这段文稿做拼贴动画`, and `eian-collage-broll`.

For a full voiceover, expect candidate extraction and your selection before Gate 1. For a self-contained passage with one main visual proposition, expect it to enter Gate 1 directly.

## When to use it

Use this workflow when you need:

- a finished voiceover translated into a clear editorial visual metaphor;
- approximately five-second, vertical (9:16 by default) paper-collage motion assets per selected item;
- a creator-controlled process that approves both the idea and the finished end-frame image;
- a downstream image-to-video prompt that preserves a confirmed composition rather than inventing a new one.

It is not the right workflow when you need:

- a finished video rendered by this Skill;
- an exactly editable timeline, precise layer or occlusion control, camera travel, or a shot-by-shot animation system;
- transparent assets intended for independent layer editing;
- real-person product advertising or a speaking on-camera performer.

## Optional subject references

A human, animal, or other face-bearing subject reference is optional. It is used only when the current item's approved design calls for that kind of subject.

- Each item independently resolves `subject.type` as `none`, `human`, or `nonhuman` before reference handling.
- When the type is `none`, no subject reference is attached or introduced.
- A submitted reference must make the face and principal features clear enough to assess. Unclear, tiny, heavily obscured, or blurred faces are not accepted as the formal subject reference.
- Valid human and nonhuman references may become a managed default reference of the same type, while Gate 2 reads only the association made for the current item.
- The original user upload is never deleted by this workflow. Persistent managed references are also outside project cleanup.

The authoritative lifecycle and state rules are in [SKILL.md](SKILL.md).

## Project map

| File | Responsibility |
| --- | --- |
| [SKILL.md](SKILL.md) | Invocation, input state, Gates, per-item progression, final delivery, references, and cleanup trigger. |
| [B-roll Candidate Extraction](references/broll-candidate-extraction.md) | Type A/B/C routing, contiguous-source boundaries, candidate extraction, and creator selection. |
| [Visual Language](references/visual-language.md) | The single source of truth for image appearance. |
| [Image Production](references/image-production.md) | Visual specification, image prompts, end-frame and first-frame QA, and downstream motion-prompt materialization. |
| [Project Cleanup](references/cleanup.md) | Project-wide preflight and fail-closed cleanup rules. |
| [Invocation metadata](agents/openai.yaml) | Agent-facing Skill metadata. |
| [Evaluations](evals/evals.json) | Behavioural evaluation cases. |
| [LICENSE](LICENSE) | MIT License terms. |

When a summary conflicts with the file that owns a responsibility, follow that responsibility file.

## FAQ

### Why are there two approval gates?

They protect different decisions. Gate 1 confirms the visual meaning and composition before generation. Gate 2 confirms the actual completed image after it has been generated and reviewed. This keeps the creator—not an unreviewed candidate—in charge of both the idea and the final aesthetic result.

### Is `first-frame.png` generated from scratch?

No. By default it is an edit of the confirmed `last-frame.png`: foreground collage components and their shadows are removed while the same real paper surface, texture, grain, and 9:16 canvas are preserved. A retained base is allowed only when the creator explicitly asks for it.

### Does every item have to wait for the whole batch?

No. Approved items advance independently into their asset packages. Project Cleanup waits, because it is deliberately a project-level operation and begins only after all active items have completed Phase 3.

### Can the workflow change the source voiceover?

No. It can identify continuous source passages that are suitable as items, but the selected passages retain their original wording and order. The visual proposition is an interpretation of the voiceover, not a rewrite of it.

### Does a reference image force a person or animal into every frame?

No. The current item's approved `subject.type` decides whether a reference is used. A persistent reference never overrides a `none` design.

### Where is the finished video?

There is none in this workflow. The three deliverables are designed to be supplied to a downstream motion-generation system.

## Contributing

Contributions should preserve the creative contract rather than turning the workflow into an automatic video generator. Before proposing a change, identify the file that owns the affected responsibility in the project map above. Keep Gate 1 and Gate 2 as explicit creator approvals, preserve the three final filenames, and keep cleanup project-level and fail-closed.

This package is supplied under the [MIT License](LICENSE).
