# Prompt Engineering Technique Catalog

Reference for the `prompt-builder` skill. These are the techniques Anthropic's prompt
generator and prompt improver apply, roughly in order of impact. Apply the ones that
fit the task; don't bolt on every technique by default.

> For the expert-panel layer (makers/checkers, degree of separation, debate protocol,
> 80/20 stop rule, cost model), see `makers-checkers.md`.

## 1. Be clear and direct

- State the task explicitly. Spell out what the output should contain.
- Give context: what the result is for, who the audience is, what "good" looks like.
- Prefer positive instructions ("respond in plain English") over negative ones
  ("don't use jargon").
- Use numbered or bulleted steps for multi-part tasks.

## 2. Define success criteria

Before writing the prompt, decide how output quality is judged:
- **Correctness / accuracy** — factual, runs, passes tests.
- **Format** — JSON, markdown table, specific schema.
- **Tone & style** — formal, terse, friendly.
- **Length / scope** — word count, number of items, boundaries.
Bake these into the prompt as explicit requirements.

## 3. Use XML tags to structure

Wrap distinct parts in tags so the model can tell them apart:
`<context>`, `<instructions>`, `<example>`, `<data>`, `<output_format>`.
Tags reduce the chance the model confuses instructions with data, and let you
reference sections by name ("using the document in <data>...").

## 4. Assign a role (system prompt)

Set a role when domain framing improves results: "You are a senior security engineer
reviewing a diff." Put durable role/behavior in the system prompt; put the specific
task in the user turn.

## 5. Provide examples (multishot)

- 1-3 examples dramatically improve format and style adherence.
- Make examples representative of real inputs, including edge cases.
- Wrap them in `<example>` tags; show input → desired output.
- For reusable templates, leave `{{PLACEHOLDERS}}` where per-run content goes.

## 6. Let the model think (chain of thought)

For analysis, math, multi-step reasoning, or judgment calls:
- Ask it to reason step by step before answering.
- Give it a `<thinking>` space, then a separate `<answer>` section, so reasoning
  doesn't pollute the final output.
- For extended/agentic tasks, structure the thinking as explicit sub-steps.

## 7. Prefill the response

Steer format or skip preamble by prefilling the start of the assistant turn
(e.g. prefill `{` to force JSON, or `Here is the table:` to skip chit-chat).
Useful when calling the API directly.

## 8. Chain prompts for complex work

Break a big task into sequenced prompts where each step's output feeds the next
(e.g. outline → draft → critique → revise). Keeps each step focused and checkable.

## 9. Control output format

- Specify the exact shape: schema, headings, delimiters.
- For machine-readable output, demand strict JSON and provide the schema.
- State length limits explicitly.

## 10. Handle long context

- Put long documents/data near the top, the question/instructions near the bottom.
- Reference data by XML tag.
- Ask the model to quote relevant passages before answering to ground its response.

---

## Default prompt skeleton

```
<role>            Optional. Who the model should be.
<context>         Background, purpose, audience, inputs.
<instructions>    Numbered, unambiguous, positive phrasing.
<constraints>     Hard rules, scope limits, what to avoid.
<data>            {{INPUT}} — the per-run material, tagged.
<thinking>        "Think step by step before answering." (non-trivial tasks)
<output_format>   Exact shape of the deliverable.
<examples>        <example>input → output</example> (when format/style matters)
```

## Quick checklist before finalizing a prompt

- [ ] Is the task unambiguous to someone with no extra context?
- [ ] Are success criteria explicit?
- [ ] Is the output format pinned down?
- [ ] Are instructions positive and ordered?
- [ ] Are examples included where format/style matters?
- [ ] Is reasoning requested for non-trivial tasks?
- [ ] Are per-run parts marked as `{{VARIABLES}}`?
- [ ] Is anything padded that could be cut?
