---
name: spec-design
description: >
  Write, slice, review, or refactor implementation specs that deliver standalone
  customer value. Use before building from a spec, when a spec feels like an
  enabler, when a solution feels heavier than the problem, when a spec asserts
  current code behavior, when user-visible behavior changes, or when checking
  whether shipped work actually reaches a real consumer. Applies to specs,
  design documents, ADRs, RFCs, proposals, and plans.
---

# Spec design

Treat the spec as the unit of work. Kill two failure modes: work that cannot
deliver value by itself and machinery designed for a future that may not arrive.
Run these rules before writing, before building, and before accepting the work.

## 1. Ship discrete, standalone customer value

- Make each spec deliver customer value end to end, by itself.
- Never make one spec wait on another to become valuable. Reslice coupled specs
  until each delivers something independently.
- Slice vertically from trigger to customer outcome, not horizontally by pipeline
  layer.
- Name the outcome chain to a real consumer: build -> run -> decide -> human acts
  -> customer outcome. End at something the customer notices, never at “the table
  is populated.”
- If no consumer exists on the day the spec ships, treat it as an enabler. Fold it
  into the value slice it enables or cut it.
- Frame value by the outcome, not the mechanism or separation of parts.
- Split different trust tiers or responsibilities into different specs. Keep
  recommend-only, assisted apply, system write-back, and autonomy separate so the
  safe capability does not wait for approval of the dangerous one.

## 2. Maximize impact for minimum effort

- Specify the crudest mechanism that delivers the value. Add sophistication only
  when the simple version demonstrably fails.
- Do not plan for an uncommitted future: no speculative abstractions, settings
  with one caller, or phase two nobody agreed to.
- Cut work that only repackages data already on the record.
- Cut work that reaches no real consumer when it ships.
- Do not collapse axes a consumer needs separately, such as value and feasibility,
  into one score. Keep the raw axes.
- Re-test abstractions inherited from an ADR or older spec. Ask what breaks if the
  abstraction is omitted.
- Sequence by impact divided by effort, not by completeness.

## 3. Validate before claiming

Support both kinds of spec claim with evidence in the spec.

**What the code does today.** Treat every current-behavior statement as a
load-bearing fix-plan claim. Cite `file.py:line`, the query and its result, or the
command and its output. Another document, ADR, plan, or delegated summary is not
source verification; read the source yourself. Give numbers a denominator and an
as-of date.

**That the work delivered.** Verify in source and tests that the mechanism runs
and reaches a consumer. Shipped is not delivered. Look explicitly for dormant
merged code: no production caller, an unseeded attribute, a never-run harness, or
a branch not merged to main.

Cite the locator instead of copying code. Quote only when the exact text is the
thing being fixed.

## 4. Keep code-complexity review separate

Use this skill to size and shape the work unit. For non-trivial code complexity
inside a spec—a new module, new architectural concept, or third failed fix in one
area—use the project’s systems-design workflow or skill when one exists. Do not
duplicate that interrogation inside the spec-design check.

## 5. Use plain, outcome-first titles

- Make the title understandable to someone who does not know the code.
- Name what the work delivers, not a field, function, mechanism, or internal name.
- Keep titles succinct. Put mechanisms and acceptance criteria in the body.
- Apply the same rule to section headings so the headings alone tell the argument.
- Use plain language and no em dashes.

Prefer “The webhook honors every required opt-out keyword” over
“Complete `STOP_KEYWORDS` frozenset.”

## 6. Make the body readable at a glance

Use this fixed backbone and remove bespoke headers:

**Why** -> **the proposed work** -> **files touched** -> **out of scope** ->
**tests** -> **sequencing or rollout** -> **what was rejected** -> **sources**.

- Lead with the problem in the reader’s terms. When the honest impact is “less
  than expected,” state that and the evidence up front.
- For every problem, state what happens with evidence, what breaks for the user,
  how often, and one verdict: fix it, no action, or fix only while editing that
  code. “Consider” is not a verdict.
- Record rejected alternatives and the reason each lost.
- Keep the body as the current plan, not a journal. Put status in the status line
  and dated revisions at the end.
- Compress mechanism detail. Put file paths and failure modes where needed.
- Do not restate what the code already documents.
- Specify desired behavior, not implementation. Name a code symbol only when
  creating or deleting that symbol is the deliverable, or mark it as a non-binding
  locator for orientation.
- Name the test command and the passing result. “Works correctly” is not testable.
- List sources for external claims or omit those claims.

## 7. Count the files and make exclusions concrete

- List every file the work touches and the section it supports.
- Count the files and compare the count with the project’s scope threshold.
- Require a non-empty **Out of scope** section. Name adjacent work a reader might
  assume is included and explain why it is excluded.

## 8. Specify user-visible behavior and UI together

Put the UI change in the same spec whenever backend work creates, removes, or
changes user-visible behavior. Cover new operational limits users need at design
time, controls for removed capabilities, and copy for changed failure modes.

Ask: what does the user see and rely on after this ships? If the answer is “the
same screen, now meaning something different,” the spec is incomplete.

## How to use

Answer two groups of questions when writing or reviewing a spec.

**Must be answerable** — give a concrete answer or the spec is not ready:

1. How does someone get this outcome today, and what is wrong with that? If
   nothing is wrong, close the spec.
2. What is the crude version of the proposed work, and why will it not do? If
   there is no reason, specify the crude version.
3. Who sees a difference the day this ships, and on which screen? If nobody does,
   fold or cut the enabler.

**Must come back empty** — every non-empty answer is a defect to fix:

4. Which parts deliver value only after another spec ships?
5. Which sections change nothing for anyone or only repackage existing data?
6. Which parts can only their author tell worked?
7. Which current-behavior claims lack `file:line`, a query result, or command output?
8. What is already documented in the code?
9. What specifies a future that has not arrived?
10. Which sections omit what breaks or lack a firm verdict?
11. Which rejected option and reason are missing?
12. What changes user-visible behavior without a matching UI change?
13. Which headings use a field, function, mechanism name, or outsider jargon?

Require a status line, date, counted file list, and non-empty **Out of scope**
section before building. Proceed only when questions 1-3 have concrete answers
and questions 4-13 are empty.
