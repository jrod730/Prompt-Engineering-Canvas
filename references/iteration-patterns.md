# Iteration Patterns — Fix Library

Use this reference when generating iteration suggestions in Phase 5. Match the dimension with the pattern that fits the diagnosed problem.

---

## Dimension: Clarity of Objective

### Pattern: Objective Sharpening
**When:** Objective is vague, multi-part, or written as a description rather than a command.
**Fix:** Rewrite as a single imperative sentence. Add a success criterion sentence.
**Template:** "Extract/Classify/Generate [specific thing] from [specific input] and return [specific output format]."
**Test:** Can you write a pass/fail unit test for this objective? If not, it's still too vague.

### Pattern: Responsibility Splitting
**When:** One prompt is doing two distinct jobs.
**Fix:** Split into two prompts. Define the handoff format between them.
**Test:** Each prompt's objective now fits in one sentence.

---

## Dimension: Role / Persona Precision

### Pattern: Domain Anchoring
**When:** Role is too generic ("helpful assistant") or mismatched to the task domain.
**Fix:** Replace with a domain-specific expert role. Add 2–3 voice descriptors. Add 1–2 behavioral constraints.
**Template:** "You are a [domain] expert with [experience marker]. You [voice trait 1] and [voice trait 2]. You never [behavioral constraint]."
**Test:** Does the role change model behavior noticeably? Run the same prompt with and without the role block.

### Pattern: Persona Removal
**When:** Task is purely structural (format conversion, extraction) and the persona is adding noise.
**Fix:** Remove the role block entirely. Start with the task block.
**Test:** Output variance decreases. Structural compliance improves.

### Pattern: Anti-Persona Addition
**When:** Model keeps drifting into an unwanted tone (too casual, too apologetic, too verbose).
**Fix:** Add explicit "You are NOT" statements to the persona block.
**Example:** "You are not a customer service agent. Do not apologize, soften, or hedge."
**Test:** Run 5 outputs and check for the unwanted pattern.

---

## Dimension: Context Sufficiency

### Pattern: Domain Knowledge Injection
**When:** Model is making domain errors — wrong terminology, missing industry logic, incorrect assumptions.
**Fix:** Add a domain context paragraph with the 3–5 facts the model needs most.
**Test:** Domain errors drop to zero on your eval set.

### Pattern: Context Pruning
**When:** Prompt is very long but performance isn't improving, or model is ignoring parts of the context.
**Fix:** Remove any context that doesn't change the output. Test each paragraph by deleting it and checking if output quality drops. Keep only what matters.
**Test:** Output quality maintained at 70% of original context length.

### Pattern: User Context Calibration
**When:** Output tone or vocabulary is wrong for the end user (too technical, too simple, wrong assumed knowledge level).
**Fix:** Add a "User context" block that describes who the end user is and what they need.
**Template:** "The user is a [role] with [knowledge level] experience in [domain]. They need [output style], not [what to avoid]."
**Test:** Show output to a representative end user. Does it land?

---

## Dimension: Output Format Specificity

### Pattern: Schema Hardening
**When:** Model is returning inconsistent JSON structures, missing fields, or wrong data types.
**Fix:** Add an explicit JSON schema to the output format block. Include field names, types, required/optional status, and null handling.
**Template:**
```
Return a JSON object with this exact structure:
{
  "field_name": string,          // required
  "other_field": number | null,  // optional
  "status": "success" | "error"  // enum
}
```
**Test:** 100% of outputs parse without error.

### Pattern: Length Anchoring
**When:** Outputs are inconsistently long — sometimes truncated, sometimes padded.
**Fix:** Add explicit length targets: word count range, sentence count, or bullet point count.
**Template:** "Respond in 150–250 words" or "Provide exactly 3 bullet points, each 1–2 sentences."
**Test:** Measure output length across 10 runs. Standard deviation should drop significantly.

### Pattern: Structure Templating
**When:** Multi-section outputs are inconsistently structured.
**Fix:** Provide an explicit output template with section headers as part of the prompt.
**Template:**
```
Use this exact structure:
## Summary
[1–2 sentences]

## Key Findings
- [Finding 1]
- [Finding 2]

## Recommended Action
[1 sentence]
```
**Test:** Every output matches the template structure.

---

## Dimension: Constraint Completeness

### Pattern: Failure Mode Coverage
**When:** Model behaves unpredictably on edge cases — bad inputs, missing data, ambiguous situations.
**Fix:** List the top 3 edge cases and add explicit handling rules for each.
**Template:** "If {{variable}} is empty or malformed, return: [exact fallback behavior]."
**Test:** Feed the model each edge case. Does it follow the rule?

### Pattern: Hallucination Guard
**When:** Model is fabricating information not present in the input.
**Fix:** Add explicit prohibition: "Never include information not present in {{input_variable}}. If a field cannot be determined from the source, return null for that field."
**Test:** Feed the model an input with known missing fields. Check that those fields return null, not fabricated values.

### Pattern: Scope Limiting
**When:** Model is going beyond its assigned task — offering opinions, adding unrequested sections, editorializing.
**Fix:** Add scope constraint: "Your only job is [specific task]. Do not [list of out-of-scope behaviors]."
**Test:** Run 10 outputs and flag any that go beyond the assigned scope.

---

## Dimension: Example Quality

### Pattern: Real Input Injection
**When:** Examples use clean, idealized inputs that don't reflect production reality.
**Fix:** Replace with actual production inputs (anonymized if needed). Include typos, formatting issues, OCR artifacts — whatever the model will see in the real world.
**Test:** Examples should be indistinguishable from real inputs.

### Pattern: Edge Case Exemplification
**When:** All examples are happy-path. Model fails on edge cases.
**Fix:** Add 1–2 examples that show correct behavior on edge cases (missing data, ambiguous input, error responses).
**Test:** Edge case handling improves on eval set.

### Pattern: Negative Example Addition
**When:** Model keeps producing outputs in a wrong style/format despite instructions.
**Fix:** Add a "Bad output" example with annotation explaining why it's wrong.
**Template:**
```
BAD output (do not do this):
[Wrong output]
Why it's wrong: [Explanation]

GOOD output:
[Correct output]
```
**Test:** Incidence of the bad pattern drops to near zero.

### Pattern: Example Reduction
**When:** Many low-quality examples are creating conflicting signals.
**Fix:** Cut to 1–2 highest-quality examples. Better to have 1 excellent example than 5 mediocre ones.
**Test:** Output consistency improves after reduction.

---

## Dimension: Eval Criteria Measurability

### Pattern: Criteria Operationalization
**When:** Criteria are subjective or unmeasurable ("output should be helpful", "tone should be professional").
**Fix:** Rewrite each criterion as a binary check or a measurable property.
**Before:** "Response should be professional"
**After:** "Response contains no first-person apologies, no filler phrases ('certainly!', 'of course!'), and no hedging language ('I think', 'maybe')"
**Test:** Two different reviewers should agree on pass/fail for every criterion.

### Pattern: Structural Eval Automation
**When:** Evals are currently manual and slowing down iteration.
**Fix:** Identify which criteria are automatable (JSON validation, field presence, length check, enum value check) and write scripts for them.
**Test:** Structural evals run in CI on every prompt version bump, zero human involvement needed.

### Pattern: LLM Grader Design
**When:** Semantic evals need to scale beyond what a human reviewer can handle.
**Fix:** Write a grader prompt that takes (input, output, criteria) and returns a pass/fail JSON with reasoning.
**Note:** Grader prompts need their own canvas pass. Don't skip this.
**Test:** Grader agrees with human reviewer on 90%+ of cases in your calibration set.

---

## Recommended Sequencing Logic

When generating the iteration plan sequence, order suggestions by:

1. **Objective clarity first** — everything else compounds on top of it
2. **Output format next** — unblocks structural eval automation
3. **Constraints** — reduces variance before adding examples
4. **Examples** — higher leverage once format is locked
5. **Context** — tune after structural issues are resolved
6. **Persona** — fine-tune last (usually lowest impact)
7. **Eval criteria** — run in parallel with all of the above

**Exception:** If the scorecard shows a dimension at 1/5, fix it before anything else regardless of order.
