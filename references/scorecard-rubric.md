# Scorecard Rubric — Scoring Guide

Use this rubric to score prompts in Phase 4. Each dimension is scored 1–5. Apply the weighted formula to get a final score out of 100.

---

## Weighted Score Formula

```
Score = Σ (dimension_score × dimension_weight × 20)
```

| Dimension | Weight |
|---|---|
| Clarity of Objective | 20% |
| Role / Persona Precision | 10% |
| Context Sufficiency | 15% |
| Output Format Specificity | 15% |
| Constraint Completeness | 15% |
| Example Quality | 15% |
| Eval Criteria Measurability | 10% |
| **Total** | **100%** |

---

## Dimension Rubrics

### 1. Clarity of Objective (Weight: 20%)

| Score | Description |
|---|---|
| 5 | Single, imperative, measurable objective. Success criterion is explicit. A unit test could be written for it. |
| 4 | Clear single objective. Success criterion implied but not stated. |
| 3 | Objective present but contains 2 tasks, or is written descriptively rather than imperatively. |
| 2 | Objective is vague ("help with X") or buried in multiple sentences. |
| 1 | No clear objective. Prompt reads as background info without a job. |

---

### 2. Role / Persona Precision (Weight: 10%)

| Score | Description |
|---|---|
| 5 | Domain-specific role, 2+ voice descriptors, explicit behavioral constraints. Role changes model behavior measurably. |
| 4 | Domain-specific role with at least 1 voice descriptor. Minor gaps. |
| 3 | Generic role ("helpful assistant") or role present but mismatched to task domain. |
| 2 | Role is a job title with no descriptors and no constraints. |
| 1 | No role defined, or role actively conflicts with the task. |

**Note:** For purely structural/extraction tasks, a score of 3 with a minimal role is acceptable. Don't penalize for intentionally omitting persona on extraction-only tasks.

---

### 3. Context Sufficiency (Weight: 15%)

| Score | Description |
|---|---|
| 5 | Domain context covers all non-obvious knowledge the model needs. User context calibrates tone and vocabulary appropriately. No unnecessary context padding. |
| 4 | Domain context present and mostly complete. User context present but generic. |
| 3 | Some domain context provided but with gaps that would cause errors on real inputs. No user context. |
| 2 | Minimal context. Model is expected to infer things it shouldn't have to. |
| 1 | No context provided. Prompt assumes the model has knowledge it doesn't. |

---

### 4. Output Format Specificity (Weight: 15%)

| Score | Description |
|---|---|
| 5 | Explicit format (JSON schema / template / structure). Length range specified. Required vs. optional fields defined. Parsing intent stated. |
| 4 | Format type specified. Length target present. Minor gaps in field definitions. |
| 3 | Format mentioned but not specified ("return JSON" without schema, "keep it short" without range). |
| 2 | Output format implied but not stated. Model is expected to guess appropriate format. |
| 1 | No output format defined. Model will produce whatever it defaults to. |

---

### 5. Constraint Completeness (Weight: 15%)

| Score | Description |
|---|---|
| 5 | Hard prohibitions listed. Hard requirements listed. Top 3 edge cases have explicit handling rules. Hallucination guard present if applicable. |
| 4 | Prohibitions and requirements present. 1–2 edge cases covered. Minor gaps. |
| 3 | Some constraints present but major failure modes not addressed. |
| 2 | 1–2 generic constraints ("be accurate", "be concise") with no specificity. |
| 1 | No constraints defined. |

---

### 6. Example Quality (Weight: 15%)

| Score | Description |
|---|---|
| 5 | 1–3 examples using real/realistic inputs. At least one edge case demonstrated. Outputs match required format exactly. No conflicting signals between examples. |
| 4 | Examples present with realistic inputs. Format mostly correct. No edge case examples. |
| 3 | Examples present but use idealized/unrealistic inputs, or outputs don't match the required format precisely. |
| 2 | 1 weak example. Input or output clearly doesn't reflect production reality. |
| 1 | No examples, or examples that contradict the instructions. |

**Note:** For very simple tasks (single-field extraction, yes/no classification), 0 examples with a score of 3 is acceptable. Don't penalize when examples would add noise rather than signal.

---

### 7. Eval Criteria Measurability (Weight: 10%)

| Score | Description |
|---|---|
| 5 | 3–5 passing criteria, all binary/measurable. 2–3 failing criteria, all binary/measurable. At least 1 structural criterion automatable in CI. |
| 4 | Criteria present and mostly measurable. Minor subjectivity in 1–2 criteria. |
| 3 | Criteria present but written subjectively ("should be clear", "should be helpful"). Would require judgment calls to apply. |
| 2 | 1–2 criteria, vague. |
| 1 | No eval criteria defined. |

---

## Score Interpretation

| Range | Label | Recommended Action |
|---|---|---|
| 85–100 | Production-ready | Ship it. Set up structural eval automation. |
| 70–84 | Nearly there | Fix the 1–2 lowest-scoring dimensions before shipping. |
| 50–69 | Needs work | Complete Phase 5 iteration plan. Don't ship until ≥70. |
| <50 | Rebuild | Return to Phase 2 canvas. The foundation is too weak to iterate on. |

---

## Common Score Patterns and What They Mean

**High objective, low format (e.g., 5 / 4 / 3 / 1 / 3 / 3 / 2):**
You know what you want but haven't specified how to get it. Prioritize output format and constraints.

**Low objective, high everything else (e.g., 1 / 5 / 4 / 5 / 4 / 4 / 3):**
Lots of scaffolding but no clear job. Rewrite the objective first — everything else depends on it.

**Mid across the board (e.g., 3 / 3 / 3 / 3 / 3 / 3 / 3):**
Generic prompt. Likely a first draft. Run the full iteration plan in priority sequence.

**High everywhere except examples (e.g., 5 / 4 / 4 / 5 / 4 / 1 / 4):**
Structurally solid but unanchored. Add 1–2 real examples and retest. This usually pushes a 72 to an 88.
