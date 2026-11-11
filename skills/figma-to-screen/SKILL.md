---
name: figma-to-screen
description: Carry a Figma design into an existing web screen (HTML templates and CSS) and prove the result matches — what counts as evidence for a value, collecting every interaction state before writing CSS, applying values through the project's design tokens, markup and accessibility basics, and side-by-side capture comparison as the completion criterion. Use when the user shares a Figma link or frame, or mentions 시안, 피그마, 퍼블리싱, 시안대로, 시안과 다르다, 간격·색·글자 크기 맞추기, 호버, 반응형.
metadata:
  reviewed: 2026-11-11
---

# Figma to Screen

This skill decides what to trust and when the work is done. How to call the Figma tools is the
Figma plugin's own skills; load those before calling its tools.

A repository's `CLAUDE.md` and design documents override this file on tokens, file layout,
naming, and where decisions are recorded. Read them first.

## Never

- Treat generated code from Figma as the source of a color, border, weight or spacing value.
- Write a raw color, size or radius where the project has a token for it.
- Win against an old rule with higher specificity or `!important`. Remove or change the old rule.
- Rename or remove an `id`, `class` or `data-*` without searching the scripts and tests that
  look it up by name.
- Report the work as done without the capture comparison in step 4.
- Write to a Figma file unless the user asks.

## Evidence ranking

When sources disagree, the higher one wins.

| Rank | Source | Use for |
|---|---|---|
| 1 | Values the user pastes from Figma Dev Mode | Final decision |
| 2 | Pixels of a node screenshot | Confirming color and border |
| 3 | Variables bound to the node | Which token the node uses |
| 4 | Generated code | Structure and spacing hints only |

Generated code can omit a fill that exists, or include one that belongs to another state.

## Procedure

### 1. Read the project

- Find the token file and the stylesheet that already styles the element.
- Note the load order of stylesheets and any rule that already targets the same selector,
  including brand- or theme-scoped rules.

### 2. Collect, for the whole region

Fill this matrix for every interactive element in the region before writing CSS.

| | Default | Hover | Pressed | Focus | Disabled | Selected |
|---|---|---|---|---|---|---|
| Background · border · text · icon | required | required | required | required | if any | if any |

- A frame is a snapshot of one state. Decide which state it shows first.
- If one element differs from its siblings, suspect a different state before treating the
  difference as its default.
- Fill gaps from the component definition. Ask the user about what is still missing in one
  batched question for the region, not one question per element.

### 3. Apply

- Use the project's tokens. When the design has a value no token covers, add a token the way
  the repository prescribes and say so; do not inline the value.
- Remove the old rule that would override the new one, then add the new rule.
- No inline `style` attributes.
- Markup:
  - An action is a `<button>`, navigation is an `<a href>`. Not a clickable `<div>`.
  - Every input has a label. Every meaningful image has `alt`; decorative ones have `alt=""`.
  - Keyboard focus stays visible. Do not remove an outline without a replacement.
- Check text contrast against WCAG 2.1 AA (4.5:1 for body text, 3:1 for large text). KWCAG 2.1
  uses the same thresholds. If the design fails, keep the design's value and report it; the
  color decision is the designer's.
- When wording changes, update every language file the project has.

### 4. Verify by capture

1. Start the app and open the changed screen.
2. Capture the Figma node and the same region of the running screen at the same width.
3. Compare the same points on both: background, border, text color, size, spacing.
4. Trigger each state from the matrix on the running screen and compare again.
5. Check the browser console for errors, and the screen at a narrow and a wide width.
6. If the stylesheet is shared by other brands or themes, open one of them too.

Save captures outside the repository. Use synthetic data only; a capture must not show real
personal data.

### 5. Report

In plain Korean:

- A table: element · state · Figma value · screen value · match.
- Paths of the before and after captures.
- What could not be verified, and what is still an open question for the user.

## When the screen does not match

Look at the running screen before looking at Figma again.

| Symptom | Check first |
|---|---|
| A style does not apply at all | The stylesheet is loaded, and the file is committed, not just present locally |
| A value is overridden | A later-loaded rule with equal specificity, or a brand/theme-scoped rule |
| Hover looks permanent | The frame showed the hover state and it was applied as the default |
| The edit does not show on reload | Asset caching; use the project's development setting for it |

## Checklist

- [ ] Repository rules and token file read.
- [ ] State matrix filled for the whole region; open cells asked in one batch.
- [ ] No raw values, no `!important`, no inline styles.
- [ ] Old overriding rules removed.
- [ ] Names used by scripts and tests unchanged, or changed together.
- [ ] Figma and screen captures compared for every state.
- [ ] Console clean; narrow and wide widths checked.
- [ ] Contrast failures reported, not silently changed.
