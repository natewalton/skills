---
name: general-systems
description: "Invoke this skill when a task touches 3+ files, introduces a new architectural concept, or when you've attempted 2+ fixes for the same problem without resolution. Use it to step back from implementation and evaluate the design before continuing."
---

# Systems Check — Design Review Protocol

Interrupts implementation momentum to evaluate whether you're solving the right problem at the right level of abstraction. The most expensive bugs are design bugs — wrong abstractions that spawn cascading fixes.

---

## Hard rules: evidence before design conclusions

Left to itself, a model reasons where it should measure — and reasoning produces confident-wrong answers, hallucinated columns, fabricated counts, and decisions from theory instead of code. The base rule: never state a load-bearing claim (one that makes the next decision wrong if it is wrong) without running the tool call that proves it, this session. Systems-work additions:

- **Every load-bearing claim in this protocol MUST come from a tool call this session.** Concept counts, caller counts, exception counts, column semantics, phase timing — all require a tool call, not recall.
- **Parallelize with subagents.** When the protocol needs counts across modules, spawn an agent to gather them in parallel instead of serializing into reasoning.
- **Stop mid-sentence when theorizing.** "The real problem is X" / "this is almost certainly Y" → delete, measure, write what the measurement showed.

---

## When this fires

**Automatic triggers:**

- Task touches **3+ files** across different layers.
- You've attempted **2+ fixes** without the root cause moving.
- You're about to introduce a new **type, state variable, event, prop, flag, or special-case branch**.
- You're about to add a **guard, cap, retry, or "unless" clause** to prevent a failure mode.

**Manual trigger:** user says "step back," "rethink," "this is getting complicated," or questions the approach.

State the trigger out loud when it fires ("systems check triggered: 4 files touched") — the named moment is the intervention.

---

## The Protocol

### Step 1: Name the system

State in one sentence what the system does and what its core concepts are. No caveats. If you can't, jump to Step 5.

### Step 2: Count concepts (from the code, this session)

List every distinct concept: types, states, events, props, flags, special cases. Derive the list by reading code in this session and quoting function signatures, field names, or schema lines. Memory does not count.

- **Healthy:** 3–5 core concepts compose to handle all cases.
- **Unhealthy:** 5+ concepts with conditional interactions.

> "Simple is the opposite of complex. A thing is simple if it has no interleaving, if it has one purpose, one concept, one dimension." — Rich Hickey, *Simple Made Easy*

### Step 3: Check exception accumulation (counted, not estimated)

Count every special case, conditional branch, or "except when" by grepping — not estimating.

| Count | Meaning | Action |
|-------|---------|--------|
| 0–1 | Healthy edge case | Proceed |
| 2–3 of the same kind | Concept defined too narrowly | Widen it |
| 4+ or 2+ kinds | Model doesn't match reality | Redesign core abstractions |

Exceptions are data points, not defects. A cluster reveals a pattern your model hasn't captured — update the model rather than enumerate more exceptions.

### Step 4: Identify friction signals

| Friction | Meaning | Action |
|----------|---------|--------|
| Simple feature needs 5+ files | Component boundaries wrong | Redesign |
| State tracking state (flags, retries, generation counters) | State model too complex | Simplify |
| Workaround requires understanding 3 other workarounds | Accidental complexity compounded | Remove, fix root |
| Framework fights you | Missing a built-in | Research before workarounds |
| Adding a guard/cap to prevent an infinite loop | Loop shouldn't exist | Question the loop |

### Step 5: Diagnostic questions

Answer with code-grounded evidence:

1. Am I fixing a symptom or the model? Symptom → redesign.
2. Can existing concepts absorb this with a small generalization? Yes → bend. No → new concepts or redesign.
3. Would I add this concept to a system designed from scratch? No → you're patching.
4. Can I explain the full system in one sentence? Paragraph of caveats → simplify.

---

## Design Principles

### Same behavior = same thing

Two operations that behave identically (change what's visible, change what elements are available) should be modeled identically. Don't create distinctions the user doesn't experience.

### Consistency serves familiarity, not itself

A user carries patterns learned in one feature into the next, so reusing an established in-product pattern makes that transfer free. Default to a sibling feature's pattern; the burden of proof is on divergence.

> "Users spend most of their time on other sites. This means that users prefer your site to work the same way as all the other sites they already know." — Jakob's Law (Jakob Nielsen)

But consistency is a *means* to usability, never an end in itself. Match presentation to behavior:

> "It is just important to be visually inconsistent when things act differently as it is to be visually consistent when things act the same." — Bruce Tognazzini, *First Principles of Interaction Design*

- **Same behavior → same pattern.** Surface drift between two features doing the same thing (different labels, icons, placeholders, layout for an identical action) is a defect — it taxes the user to relearn for no reason.
- **Different behavior → deliberately different pattern.** Forcing a shared pattern onto a feature whose task genuinely differs hides that difference; Tognazzini ranks misrepresenting behavior as "one of the worst things you can do to a user."
- **When a shared pattern and a specific feature's usability conflict, the feature wins** — but only when the divergence is "absolutely necessary to the task or will improve efficiency" (NN/g Heuristic 4, *Consistency and Standards*), i.e. justified by a behavioral difference the user actually experiences, not by local preference or cleverness. When the divergent pattern is simply *better*, raise both features to it rather than leaving them split.
- **Consistency with user expectations outranks visual uniformity.** "The most important consistency is consistency with user expectations" (Bill Buxton, via Tognazzini). The test is "does the learned model transfer," not "do the pixels match."

Distinguish internal consistency (within our own product/suite) from external consistency (platform and industry conventions per Jakob's Law). Both count; when they conflict, user expectation wins. Applies to data-model design and cross-surface UI alike — it is the user-facing twin of "same behavior = same thing."

### Prefer elimination over addition

"What can I remove?" beats "What can I add?" Removing a hack, flag, or special case is almost always better than adding one to compensate.

### Separate validation from execution

The system that checks validity should be independent from the system that executes. Problems in one layer shouldn't leak.

### Understand the framework before fighting it

Unexpected platform behavior (scroll resets, component remounts, state not updating) usually has a built-in solution that eliminates entire categories of hacks. `setTimeout` bandaids are a tell this step was skipped.

### Concept count is king

> "Simplicity is a prerequisite for reliability." — Edsger Dijkstra

Complexity grows quadratically with concept count. Before adding a new concept, exhaust ways to express the requirement with existing ones.

### Design for today, not hypothetical futures

> "Three uses before you build an abstraction." — Sandi Metz

Don't add configurability for scenarios that haven't happened. When the second and third use cases appear, then generalize — with real data about what varies.

---

## Red flags

Any one is a reason to pause:

- Boolean flag to handle "this one case"
- Comment starting with "workaround for…" or "hack:"
- Function that needs to know about 3+ other components' internal state
- State variable names containing "retry," "generation," "pending," "temp"
- `if/else` chain where each branch handles a "type" of the same operation
- Fix that requires understanding why 2+ previous fixes were necessary
- `setTimeout` / delays for rendering or state timing
- Writing a design conclusion without a tool call from this session backing it — the single most common way design reviews fail

---

## After the check

If the check reveals design issues, do not proceed:

1. State the design problem, with quoted code as evidence.
2. Propose the simplest model that handles all known cases.
3. Identify which existing code can be removed, not just what to add.
4. Get alignment before writing code.

If the check passes, proceed — you've verified the design absorbs the change naturally.
