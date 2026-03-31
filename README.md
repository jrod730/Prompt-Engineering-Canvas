# 🧠 Prompt Engineering Canvas

> A structured, repeatable workflow for designing production-grade AI prompts — built as a Claude skill for product and dev teams.

---

## 🤔 The Problem

Most AI prompts in production are written once, never documented, and iterated on by vibes.

No spec. No version history. No way to know why it was written that way. Someone edits the system prompt, outputs quietly degrade, and nobody knows why.

This skill fixes that.

---

## ✨ What It Does

The Prompt Engineering Canvas guides you through **6 structured phases** to produce:

| Output | Description |
|---|---|
| 📄 **Versioned Prompt Spec** | All design decisions captured in a `.md` doc with changelog |
| ⚡ **Ready-to-Use System Prompt** | Clean, copy-paste prompt with no fluff |
| 🏆 **Scorecard** | 7-dimension rubric, weighted score out of 100 |
| 🔁 **Iteration Plan** | Prioritized, actionable fixes — not "improve your prompt" but *exactly* what to change and why |

---

## 🗺️ The 6 Phases

```
Phase 1 → Intake          Understand the goal before writing a single word
Phase 2 → Canvas Build    Fill all 8 prompt components, one at a time
Phase 3 → Draft           Assemble the full system prompt from the canvas
Phase 4 → Scorecard       Score across 7 weighted dimensions (out of 100)
Phase 5 → Iteration Plan  Get a prioritized fix list for every gap
Phase 6 → Output Package  3 versioned artifacts, ready to commit
```

---

## 🧩 The 8 Canvas Components

Every prompt built with this skill covers all 8 components:

1. **🎯 Objective** — Single, imperative, measurable job for the prompt
2. **🎭 Persona / Role** — Domain-specific voice with behavioral constraints
3. **📚 Context** — Domain knowledge + user context the model can't infer
4. **🔧 Input Variables** — Dynamic slots with types and required/optional flags
5. **📐 Output Format** — Schema, length range, structure, parsing intent
6. **🛡️ Constraints & Guardrails** — Hard prohibitions, requirements, edge case rules
7. **💡 Few-Shot Examples** — Real input/output pairs, including edge cases
8. **📊 Eval Criteria** — Binary pass/fail criteria for every output

---

## 📊 The Scorecard

Every prompt gets scored across 7 dimensions with a weighted total out of 100:

| Dimension | Weight |
|---|---|
| Clarity of Objective | 20% |
| Role / Persona Precision | 10% |
| Context Sufficiency | 15% |
| Output Format Specificity | 15% |
| Constraint Completeness | 15% |
| Example Quality | 15% |
| Eval Criteria Measurability | 10% |

**Score thresholds:**
- 🟢 **85–100** — Production-ready. Ship it.
- 🟡 **70–84** — Fix 1–2 gaps before shipping.
- 🟠 **50–69** — Iterate before testing in production.
- 🔴 **< 50** — Rebuild. Return to Phase 2.

---

## 🚀 How to Use It

### Option 1: Claude Code / Orchestra (Recommended)

1. Drop `prompt-engineering-canvas.skill` into your skills directory
2. The skill auto-triggers when you say things like:
   - *"Help me write a prompt for..."*
   - *"Build a system prompt that..."*
   - *"My prompt isn't working, can you fix it?"*
   - *"Design a prompt for [feature]"*
3. Claude guides you through all 6 phases interactively

### Option 2: Claude.ai Direct

1. Start a new Claude.ai conversation
2. Say: *"I want to use the Prompt Engineering Canvas to build a prompt for [your feature]"*
3. Paste the contents of `SKILL.md` if Claude doesn't have access to the skill file
4. Answer the 5 intake questions and let Claude drive

### Option 3: Manual Workflow

Use the canvas as a checklist. For each new prompt:
1. Fill in all 8 components in a new `[feature]-prompt-spec-v0.1.md`
2. Score it against the rubric in `references/scorecard-rubric.md`
3. Generate iteration suggestions using `references/iteration-patterns.md`
4. Commit your spec alongside your code

---

## 📁 File Structure

```
prompt-engineering-canvas/
├── SKILL.md                          # Main workflow — all 6 phases
└── references/
    ├── canvas-components.md          # Deep guidance on all 8 components
    ├── scorecard-rubric.md           # Scoring criteria, rubrics, thresholds
    └── iteration-patterns.md         # Named fix library by dimension
```

---

## 💬 Example Session

```
You:    Help me write a prompt for a LinkedIn post generator

Claude: Let's run the canvas. 5 intake questions first:
        1. What is this prompt for?
        2. What model will run it?
        ...

[After intake]

Claude: Canvas complete. Here's your assembled v0.1 system prompt.
        Scorecard: 92/100 🟢
        2 iteration suggestions identified. Ready to package?

You:    Yes

Claude: [Delivers spec doc, clean system prompt, scorecard summary]
```

---

## 🔁 Iteration Loop

The canvas isn't a one-shot tool — it's designed for iteration.

After your first test run in production:
1. Bring back real outputs that didn't pass your eval criteria
2. Claude re-scores affected dimensions
3. Applies fixes from `references/iteration-patterns.md`
4. Bumps the version (v0.1 → v0.2) and updates the changelog

**Every prompt version is traceable. Every change has a reason.**

---

## 📄 License

MIT — use it, fork it, improve it.
