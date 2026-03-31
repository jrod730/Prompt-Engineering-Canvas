---
name: prompt-engineering-canvas
description: A structured, repeatable workflow for designing, scoring, and iterating on LLM prompts. Use this skill whenever a user wants to build, improve, or formalize a prompt — for any or any agent. Trigger on phrases like "help me write a prompt", "build a system prompt", "design a prompt for", "how should I prompt Claude to", "let's engineer a prompt", "make a prompt for", or any time a user is about to hardcode instructions into an AI feature without a structured process. Also trigger when a user says "my prompt isn't working" or asks to improve an existing prompt.
---

# Prompt Engineering Canvas

A guided, repeatable workflow for product and dev teams to design production-grade prompts with versioned specs, scorecards, and iteration plans.

## Overview

This skill guides you through 6 phases:

1. **Intake** — Understand the goal
2. **Canvas Build** — Fill all 8 prompt components
3. **Draft** — Assemble the system prompt
4. **Scorecard** — Evaluate the draft quantitatively
5. **Iteration Plan** — Generate targeted improvement suggestions
6. **Output Package** — Produce final versioned artifacts

Read `references/canvas-components.md` for deep guidance on each component.
Read `references/scorecard-rubric.md` for scoring criteria and thresholds.
Read `references/iteration-patterns.md` for the full library of iteration strategies.

---

## Phase 1 — Intake

Ask the user these questions (all at once, not one by one):

1. **What is this prompt for?** (feature name, where it lives in the product)
2. **What model will run it?** (Claude Sonnet, Haiku, etc.)
3. **What's the single most important thing the output must do?**
4. **What does a bad output look like?** (failure mode)
5. **Do you have an existing prompt draft?** (if yes, paste it — skip to Phase 4)

Do not proceed to Phase 2 until you have answers to all 5.

---

## Phase 2 — Canvas Build

Fill in all 8 canvas components. For each one, ask the user targeted questions, then write the component yourself based on their answers. Show each component to the user before moving to the next.

### Component 1: Objective
- What is the single job this prompt must do?
- What does success look like in one sentence?

Output format:
```
OBJECTIVE: [One sentence. Verb-first. Measurable.]
SUCCESS CRITERION: [How you know the output is correct]
```

### Component 2: Persona / Role
- Should the model adopt a specific expertise or voice?
- What tone is appropriate for the end user?
- Are there things this persona should never say or do?

Output format:
```
ROLE: [Title or archetype]
VOICE: [Tone descriptors — e.g., "concise, clinical, never apologetic"]
CONSTRAINTS: [Persona-level don'ts]
```

### Component 3: Context
- What background does the model need that it won't have from the conversation?
- Is there domain knowledge to inject (industry terms, company-specific logic)?
- What does the model need to know about the user it's serving?

Output format:
```
DOMAIN CONTEXT: [Background the model needs]
USER CONTEXT: [What to know about the end user]
```

### Component 4: Input Variables
- What dynamic data gets injected at runtime?
- What format does each variable come in?
- Which variables are optional vs. required?

Output format:
```
VARIABLES:
- {{variable_name}}: [description] [required/optional] [format]
```

### Component 5: Output Format
- What structure should the output have? (JSON, markdown, plain text, XML)
- What's the ideal length range?
- Are there required fields or sections?
- Will this output be parsed programmatically or read by a human?

Output format:
```
FORMAT: [Structure type]
LENGTH: [Target range]
REQUIRED FIELDS: [List]
PARSING: [Human / Programmatic / Both]
```

### Component 6: Constraints & Guardrails
- What should the model never do?
- Are there compliance, legal, or tone constraints?
- What edge cases need explicit handling?

Output format:
```
NEVER: [Hard prohibitions]
ALWAYS: [Hard requirements]
EDGE CASES: [Explicit handling rules]
```

### Component 7: Few-Shot Examples
- Do you have 1–3 example input/output pairs?
- If not, should we generate them from the objective?

Generate examples if the user doesn't have them. Format:
```
EXAMPLE 1:
  Input: [...]
  Output: [...]

EXAMPLE 2:
  Input: [...]
  Output: [...]
```

Note: For Claude Sonnet and Opus, 1–2 strong examples usually beats 5 weak ones.

### Component 8: Evaluation Criteria
- How will you measure if this prompt is working in production?
- What are 3–5 things a good output always has?
- What are 2–3 things a bad output always has?

Output format:
```
PASSING CRITERIA (must have all):
- [ ] [Criterion]

FAILING CRITERIA (any = fail):
- [ ] [Criterion]
```

---

## Phase 3 — Draft Assembly

Once all 8 components are filled, assemble the system prompt using this structure:

```
[ROLE BLOCK]
You are a [role]. [Voice descriptors]. [Persona constraints].

[CONTEXT BLOCK]
[Domain context]. [User context].

[TASK BLOCK]
Your job is to [objective]. [Success criterion].

[CONSTRAINTS BLOCK]
Never: [list]
Always: [list]
Edge cases: [list]

[OUTPUT FORMAT BLOCK]
Respond in [format]. [Length]. [Required fields].

[EXAMPLES BLOCK]
Here are examples of ideal outputs:

Example 1:
Input: ...
Output: ...

[VARIABLE PLACEHOLDERS]
The following will be injected at runtime:
{{variable_name}} — [description]
```

Present the assembled draft to the user. Label it `v0.1`.

---

## Phase 4 — Scorecard

Score the draft (or existing prompt if user provided one) across 7 dimensions. Score each 1–5. Compute a weighted total out of 100.

| Dimension | Weight | Score (1–5) | Notes |
|---|---|---|---|
| Clarity of objective | 20% | | |
| Role/persona precision | 10% | | |
| Context sufficiency | 15% | | |
| Output format specificity | 15% | | |
| Constraint completeness | 15% | | |
| Example quality | 15% | | |
| Eval criteria measurability | 10% | | |

**Weighted Score = Σ(score × weight × 20)**

Thresholds:
- **85–100**: Production-ready. Ship it.
- **70–84**: Needs 1–2 targeted fixes before shipping.
- **50–69**: Significant gaps. Iterate before testing in production.
- **<50**: Rebuild. Return to Phase 2.

Show the full scorecard table, the weighted score, the threshold label, and a 2-sentence explanation of the biggest gap.

---

## Phase 5 — Iteration Plan

Based on the scorecard, generate a prioritized iteration plan. For each dimension scoring ≤ 3, produce one iteration suggestion using this format:

```
ITERATION [N] — [Dimension Name]
Current score: [X]/5
Problem: [What's wrong with the current version]
Fix: [Specific change to make — actionable, not generic]
Expected score after fix: [Y]/5
Effort: [Low / Medium / High]
Test: [How to verify the fix worked]
```

After listing all iterations, provide a **recommended sequence** (order to tackle them in, based on impact/effort ratio).

Read `references/iteration-patterns.md` for a full library of proven fix patterns by dimension.

---

## Phase 6 — Output Package

Produce three artifacts:

### Artifact 1: Prompt Spec Doc (versioned)
Filename: `[feature-name]-prompt-spec-v[X.Y].md`

```markdown
# [Feature Name] Prompt Spec
**Version**: [X.Y]
**Date**: [YYYY-MM-DD]
**Author**: [Team member]
**Model target**: [Claude model]
**Status**: [Draft / Review / Production]

## Changelog
- v0.1: Initial draft

## Canvas Components
[All 8 components, filled in]

## System Prompt
[Full assembled prompt]

## Scorecard
[Score table + weighted total]

## Iteration Plan
[All iteration suggestions in sequence]

## Eval Test Cases
[3–5 input/expected output pairs for regression testing]
```

### Artifact 2: Ready-to-Use System Prompt
Filename: `[feature-name]-system-prompt-v[X.Y].txt`

The clean system prompt only — no comments, no canvas components. Copy-paste ready.

### Artifact 3: Scorecard Summary
Filename: `[feature-name]-scorecard-v[X.Y].md`

One-page scorecard table + weighted score + iteration plan summary. For async team review.

---

## Iteration Loop

After delivering the package, offer to run another iteration:

> "Ready to iterate? Tell me which suggestions you want to tackle, or share outputs from your first test run and I'll diagnose what's still off."

When the user returns with test results or feedback:
1. Re-score the prompt on affected dimensions
2. Update the spec doc (bump minor version: v0.1 → v0.2)
3. Update the changelog
4. Re-run Phase 5 if new gaps emerge

---

## Quick Reference: When to Use Which Phase

| Situation | Start at |
|---|---|
| Brand new prompt, no draft | Phase 1 |
| Have a rough idea, no draft | Phase 1 |
| Have a draft, want to evaluate | Phase 4 |
| Have scores, want fixes | Phase 5 |
| Have a final prompt, want docs | Phase 6 |
| Returning with test results | Phase 4 (re-score) |
