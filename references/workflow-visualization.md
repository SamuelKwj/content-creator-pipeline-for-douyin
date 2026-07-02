# Workflow Visualization

> Standard approach for visualizing the cheat-content workflow as mind maps or flowcharts.
> Triggers: "make a mind map" / "draw a flowchart" / "visualize the workflow".

---

## Dual-Output Strategy (Default)

Produce two artifacts — one editable, one presentable:

| Track | Tool | Output | Purpose |
|-------|------|--------|---------|
| Editable draft | `excalidraw` | `.excalidraw` JSON | Drag into excalidraw.com for manual editing |
| Final image | `baoyu-infographic` | PNG | Screenshot for sharing / cover image |

Can produce just one if the user specifies.

**Fallback**: If the user only wants a plain-text mind map (`.md`), write a markdown tree outline directly — no image generation needed.

---

## Excalidraw Mind Map Layout Template

### Canvas Size
- Canvas ~1500×900px, nodes 260×140px, spacing 80px

### Node Layout (Center-radial + horizontal rows)
```
            [s1]   [s2]   [s3]   [s4]    ← Top row: Spark → Seed → Score → Predict
                                  ↓
[law]  [aux]  [core(Spark)]     [s4.5]  ← Middle: Rules / Aux / Center / Post-predict
                                  ↓
            [s8]   [s7]   [s6]   [s5]    ← Bottom: Calibrate ← Retro ← Publish ← Shoot
```

### Color Coding
| Stage | Hex | Meaning |
|-------|-----|---------|
| Center (Spark) | `#fff3bf` yellow | Origin |
| ①② Input (Spark/Seed) | `#a5d8ff` blue | Input flow |
| ③④ Assessment (Score/Predict) | `#d0bfff` purple | Evaluation |
| ④.5 Post-predict pipeline | `#c3fae8` teal | Auxiliary output |
| ⑤ Shoot | `#ffd8a8` orange | Intermediate |
| ⑥⑦ Publish/Retro | `#b2f2bb` green | Output flow |
| Rules / ⑧ Calibrate | `#ffc9c9` red | Warning / Feedback |

### Arrow Rules
- Solid arrows: Main flow (Center → ① → ② → ③ → ④ → ④.5 → ⑤ → ⑥ → ⑦)
- Dashed arrows: Feedback loops (⑦ → ⑧, ⑧ → Center)
- Use `startBinding` + `endBinding` tied to shapes, never hand-calculated coordinates

### Pitfalls
- Text must use container binding (`boundElements` + `containerId`), not `label` field
- Standard envelope must have `type/version/source/elements/appState` five fields
- Font size ≥ 16, titles ≥ 20
- Avoid emoji in text (excalidraw font doesn't render them)

---

## baoyu-infographic Mind Map Recipe

### Default Combination

| Dimension | Choice | Reason |
|-----------|--------|--------|
| Layout | `tree-branching` | Center-radial, mind map archetype |
| Style | `morandi-journal` | Warm hand-drawn + notebook texture |
| Aspect | `portrait` | Tall image for mobile viewing |

### Alternative Combinations

| Use Case | Layout + Style |
|----------|---------------|
| High-density full overview | `dense-modules` + `morandi-journal` |
| Teaching / explanation | `hub-spoke` + `hand-drawn-edu` |
| Strong structural contrast | `bento-grid` + `craft-handmade` |
| Dark tech aesthetic | `tree-branching` + `cyberpunk-neon` |

### Prompt Keywords (Mind Map Specialized)

When writing image_generate prompts, always include:

1. Explicit layout: `tree-branching layout` / `hub-spoke radial`
2. Explicit style: `morandi-journal style` + `warm Morandi tones (sage green, dusty pink, muted ochre)`
3. Center node: `CENTER ROOT NODE` described separately
4. Branch nodes: Each branch on its own line, with emoji index + title + 1-2 sentence description
5. Marginalia: `small handwritten notes` for human touch
6. Visual details: `washi tape decorations`, `subtle paper texture`, `handwritten font feel`
7. Closing rule: `high information density, all text crisp and legible`

---

## File Output Rules

| Type | Path |
|------|------|
| Markdown outline | `<desktop>/<topic>_workflow_mindmap.md` |
| Excalidraw | `<desktop>/<topic>_workflow.excalidraw` |
| Final PNG | image_generate default cache path, report the absolute path |

---

## Checklist (Before Finalizing)

- [ ] Content comes from AGENTS.md / actual directory, not from memory
- [ ] Each stage includes: trigger word, flow, output file, rules (if applicable)
- [ ] Eight-stage closed-loop arrows correct (including feedback loop ⑦ → ⑧ → Center)
- [ ] Four core rules shown as a separate block
- [ ] Current state / published works filled with real data
