# Canvas Components — Deep Reference

## Component 1: Objective

The objective is the contract the prompt makes with the model. It must be:
- **Verb-first**: "Extract", "Classify", "Generate", "Summarize" — not "This prompt is for..."
- **Single-responsibility**: One job per prompt. If you need two things, use two prompts.
- **Measurable**: You should be able to write a pass/fail test for it.

**Common mistakes:**
- Vague: "Help the user with estate paperwork" → Fix: "Extract all required government form fields from a death certificate and return them as a structured JSON object"
- Multi-task: "Summarize the claim and suggest a resolution" → Split into two prompts or be explicit about output structure

**For agentic prompts (Orchestra):** The objective must also specify what the agent should do when it hits an ambiguous state — stop and ask, make a best guess, or escalate.

---

## Component 2: Persona / Role

Role-setting works because it activates latent behavior clusters in the model. "You are a senior paralegal" pulls in different defaults than "You are a friendly assistant."

**Effective role anatomy:**
1. Title/archetype (activates knowledge domain)
2. Voice descriptors (activates tone)
3. Behavioral constraints (prevents drift)

**Power patterns:**
- Domain expert: "You are a freight audit specialist with 10 years of experience identifying overbilling patterns"
- Functional role: "You are a triage agent — your only job is classification, never resolution"
- Anti-persona (what NOT to be): "Never adopt a customer service tone. You are a technical tool, not an assistant."

**When to skip:** For purely structural/extraction tasks (parse this JSON, reformat this text), persona adds noise. Keep it minimal.

---

## Component 3: Context

Context is the information the model can't infer from the conversation. Two types:

**Domain context**: Industry knowledge, company-specific logic, terminology.
- Example (FuneralFlow): "In most US states, a death certificate must be filed within 72 hours. Funeral homes are the legally responsible filing party in 43 states."
- Example (FDRE): "NMFC codes are used by carriers to classify freight. Misclassification is the #1 source of invoice disputes."

**User context**: Who is the end user, what do they know, what do they need.
- Example: "The user is a funeral director, not a lawyer. Avoid legal jargon. They need action steps, not analysis."

**Anti-pattern:** Dumping everything you know into context. More context ≠ better outputs. Include only what changes the output meaningfully.

**Context window budget:** For Claude Sonnet, you have ~180k tokens. Use aggressively for complex extraction tasks. Be lean for simple tasks.

---

## Component 4: Input Variables

Variables are the dynamic slots that make a prompt reusable across inputs.

**Best practices:**
- Use `{{double_braces}}` for runtime injection
- Name variables semantically: `{{death_certificate_text}}` not `{{input}}`
- Document format explicitly: "ISO 8601 date string", "Raw OCR text, may contain artifacts", "JSON array of line items"
- Mark required vs. optional — optional vars need fallback handling in the prompt

**Variable validation:** For production prompts, include explicit handling:
```
If {{carrier_invoice}} is missing or malformed, respond with:
{"error": "invoice_missing", "message": "No invoice provided"}
```

---

## Component 5: Output Format

The most under-specified component. Vague output format = unpredictable parsing = production bugs.

**Decision tree:**
- Will code parse this? → Use JSON with a strict schema
- Will a human read this? → Use markdown with clear sections
- Will it feed into another prompt? → Match the input format of the next prompt
- Is it a yes/no classification? → Constrain to enum values

**For JSON outputs**, always specify:
- Top-level keys
- Data types
- Required vs. optional fields
- Null handling

**Length calibration:**
- Too short a target → model truncates important info
- Too long a target → model pads with filler
- Specify ranges: "150–250 words" or "3–5 bullet points"

---

## Component 6: Constraints & Guardrails

Constraints do two things: prevent failure modes and reduce output variance.

**Hard prohibitions (NEVER):**
- "Never include legal advice or liability assessments"
- "Never fabricate form field values if not present in the source document"
- "Never output partial JSON — if you can't complete the schema, return an error object"

**Hard requirements (ALWAYS):**
- "Always include a confidence score on extracted fields"
- "Always return ISO 8601 dates, never natural language dates"

**Edge case handling:**
- What to do when input is missing
- What to do when input is ambiguous
- What to do when the task is impossible given the input

**For agentic prompts:** Add a halt condition — "If you cannot complete the task with >70% confidence, stop and return a structured escalation object rather than guessing."

---

## Component 7: Few-Shot Examples

Examples are the highest-leverage component for complex tasks. They demonstrate format, tone, reasoning style, and edge case handling simultaneously.

**When examples are essential:**
- Output format is complex (nested JSON, custom markdown)
- Task requires judgment calls
- Tone must be very precise

**When to skip:**
- Simple extraction with a clear schema
- Model already handles the task well zero-shot

**Example quality checklist:**
- [ ] Input looks like real production input (not idealized)
- [ ] Output demonstrates the exact format required
- [ ] At least one example shows an edge case
- [ ] Examples don't contradict each other

**Negative examples** (showing what NOT to do) are underused but powerful — especially for tone constraints.

---

## Component 8: Evaluation Criteria

Evals are how you know if your prompt is working at scale. They also make iteration scientific instead of vibes-based.

**Three types of eval criteria:**

1. **Structural** — Does the output match the required format? (automatable)
   - JSON parses without error
   - Required fields present
   - Length within range

2. **Semantic** — Does the output say the right things? (requires LLM grader or human)
   - Extracted values match source document
   - Tone matches persona
   - No hallucinated information

3. **Behavioral** — Does the model behave correctly on edge cases? (requires specific test inputs)
   - Returns error object on missing input
   - Doesn't fabricate when uncertain
   - Halts when confidence is low

**For the Invincibles:** Store eval test cases alongside the prompt spec. Run them on every prompt version bump. Structural evals can be automated in CI; semantic evals should be reviewed by a team member before shipping.
