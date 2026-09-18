---
name: falsification-first-skill-design
description: Create, revise, slim, review, or forward-test AI coding skills using falsification-first Yes/No gates and explicit escape-hatch audits. Use together with the agent's default skill-authoring skill (skill-creator is one) for every skill-authoring task, including drafts that contain optional scope, evidence substitutions, applicability clauses, exceptions, or completion verdicts.
---

# Design falsifiable skills without escape hatches

This skill decides how a rule is worded and what counts as proof that it
works. State why each rule exists. A user instruction overrides this skill.

**Does your agent ship a default skill for writing skills? (`skill-creator` is
one.)**
   - **Yes:** Load it and use both. It decides packaging, metadata, resources,
     validation, and the mechanics of running tests and showing their results.
   - **No:** Follow your agent's documented skill format for packaging and
     validation, and run the step 5 tests yourself.

**The draft** is the text you intend to ship. In a revision it starts as the
current skill. In a new skill it starts as the first draft written after
step 1.

Work through the six steps in order. Do not begin with a narrative assessment
of the draft.

## 1. Inventory the sources

Before editing any instruction, copy these sources into an inventory:

- every explicit user requirement and prohibition;
- every supplied incident, example, counterexample, and desired result;
- the current skill and every resource its instructions tell the session to
  load;
- every protection claimed by a slimming change; and
- every repository or environment instruction whose directory scope contains
  an authorized target.

Keep every source item in the inventory. Split each source into its atomic
requirements, prohibitions, examples, and results, and give each one a stable
ID. A document or request containing several obligations cannot be represented
by one catch-all item. An empty inventory fails the draft.

**Do two source items conflict?**
   - **Yes:** Ask the user to rule. Park every row that depends on the
     conflict and finish the others. The verdict stays `REVISE` until the user
     rules.
   - **No:** Continue.

Record the target paths the request authorizes. The existing skill directory
is authorized for an in-place revision. Step 6 compares this list with the
final changed-path list.

**Does the revision require a change to a path outside that list?**
   - **Yes:** Leave that path unchanged and report the required change to the
     user.
   - **No:** Continue.

## 2. Audit the draft

Identify how a capable session could obey the draft's words while defeating
its intended outcome. Run this audit at two points. Here, on the starting
draft, every finding names something to fix. In step 6, on the final draft,
every finding blocks acceptance. Record the answers both times.

Try to misuse the draft. Answer each question independently with `Yes` or `No`
and cite the exact wording and a concrete exploit:

1. Can a supplied source be absent from the inventory?
2. Can the author decide applicability without an observable trigger?
3. Can a plan replace an executed result?
4. Can static inspection replace an executed result?
5. Can absence count as success?
6. Can an unexercised path count as success?
7. Can the draft or implementation define its own expected result?
8. Can an intermediate state satisfy the completion verdict?
9. Can a one-item placeholder inventory hide another supplied source?

A `Yes` identifies the next revision; it is not a caveat to average away.

Search the draft for `may`, `should`, `when`, `if`, `applicable`, `needed`,
`reasonable`, `relevant`, `faithful`, `authoritative`, `unsafe`, and
`load-bearing`. For each hit ask: does a coverage row name the evidence that
decides the trigger, and its one response? `Yes` keeps the word. `No` means
write that row in step 4 or rewrite the sentence.

### No double negatives

Never put two negations in one sentence. The reader has to cancel them to find
the rule, and a reader who cancels wrong follows the opposite rule. Say what
must exist or what must happen.

- Bad: "Flag behaviour that no test fails without."
- Good: "Flag code that has no test."

Find every sentence holding two of `no`, `not`, `never`, `none`, `nothing`,
`cannot`, `without`, `fail`, `absent`, `lack`, or an `un-` word. For each hit
ask: do both negations act on the same claim? `Yes` means rewrite it as a
positive statement. `No` keeps the sentence; a quoted answer label such as
`No`, or a list of search terms, is one example.

## 3. Choose the correction

Use a structural correction first: an observable trigger, an atomic gate, a
stable contract term, a decision table, a directly loaded reference, or a
deterministic script.

**Is the mechanical check more than two commands?**
   - **Yes:** Move it into a script. A script is new code, so give it its own
     tests and its own review.
   - **No:** Give the commands inline.

**Has the same rule kept a `Yes` in the audit, or drawn a reviewer finding,
after two sentence-level revisions?**
   - **Yes:** Stop sentence editing and reframe the outcome or boundary: split
     different jobs into separate skills, narrow the claim, or remove the
     design that needs the rule.
   - **No:** Revise the sentence and rerun its row.

A reframe made after step 4 sends you back to step 4. Rewrite every row it
touches before editing further.

## 4. Write the coverage rows

Create one row for each atomic behavior:

```text
Source ID:
Required behavior (one trigger, one response, one verdict):
Observable trigger:
Required response:
Evidence oracle and source locator:
Positive case:
Falsifying counterexample:
Weakening or bypass that must make the proof fail:
```

A row cites one or more sources and requires exactly one response. Every
atomic source ID maps to at least one row, and every originating incident or
example becomes a row. An empty table fails the draft. Keep the table in the
task plan or working notes. It plans the tests; it is never itself evidence
that they ran.

For each row, ask one Yes/No question: did the named trigger produce the
required response according to the named oracle?

The positive case must reach the trigger. The counterexample must produce the
dangerous wrong result that the rule is intended to reject. The weakening test
must remove, weaken, or bypass that rule and turn its proof red for that reason.

In answering a row's question, each of these is `No`:

- the condition did not occur;
- the collection the rule reads was empty;
- only a helper, source scan, plan, log, or proxy was inspected;
- the execution stopped before the protected boundary; or
- the expected result was defined from the implementation under test.

The oracle must be one of: an explicit user ruling, an instruction whose path
scope contains the target, a retained pre-change result, or an external
authority named by the request. Record its locator. The draft and the
implementation under test cannot be their own oracle.

**Do the permitted sources give this row one expected result?**
   - **Yes:** Use it as the oracle.
   - **No:** Ask the user to rule. Park this row and finish the others. The
     verdict stays `REVISE` until the user rules.

## 5. Revise the draft and run the rows

Edit the draft, then run every row. After any later revision or slimming, run
every row again. Accept shorter wording only after each counterexample is
still rejected and each weakening still turns its proof red.

Choose the verification route from this table:

| Changed content | Required verification |
|---|---|
| Any instruction, trigger, required action, tool/reference loading rule, external-effect rule, completion verdict, or claimed protection | Fresh-agent forward test plus package validation |
| Packaging or metadata grammar only; no instruction or referenced content changed | Package validation |

A row about an instruction that a session follows is run as a fresh-agent
test. One run serves every row whose trigger it reaches. A run on the prior
text is the weakening test for every rule the revision added, and it is also
the old-skill baseline that a default skill-authoring skill asks for.

For a fresh-agent test, use raw task artifacts and do not disclose the expected
answer or suspected defect. Use non-writing doubles or archived artifacts for
external effects unless live effects are already authorized. A test that
succeeds only with leaked conversation context is `No`.

**Did the proof stay green with the rule removed?**
   - **Yes:** That case cannot tell the rule from its absence. Write one
     harder case, where the dangerous result is the easy path, and run it with
     the rule and with the rule removed.
   - **No:** The rule is doing work. Keep it.

**Did the harder case also stay green with the rule removed?**
   - **Yes:** Show the user both green results and ask whether to keep the
     rule. Delete it on a no. On a yes, mark it `kept by user ruling` and
     record that answer as its oracle. A requirement the user gave earlier is
     the rule's expected result; it is never this ruling.
   - **No:** Keep the rule.

## 6. Decide

Run the step 2 audit on the final draft. Then answer every gate independently
with `Yes` or `No` and cite a coverage row, receipt, diff, or test. Every
answer must be `Yes`:

Inventory

1. Does every supplied source appear in the inventory?
2. Is the source inventory nonempty?
3. Does every source map to a coverage row?
4. Does the final changed-path list contain only the recorded authorized paths?

Rows

5. Does each coverage row require exactly one response?
6. Does each row name an observable trigger?
7. Does each row name a permitted oracle locator?

Runs

8. Did each positive case reach its trigger and pass?
9. Did each counterexample reach its trigger and fail?
10. Is every rule either proven by a weakening that turned its proof red, or
    marked `kept by user ruling` after its harder case stayed green?
11. Were gates 8 to 10 answered from runs on the final draft?
12. Did the required fresh-agent or packaging verification run and pass?

Final audit

13. Is every escape-audit answer `No`?
14. Is every conditional phrase bound to an observable trigger?
15. Does every conditional phrase require exactly one response?
16. Is the draft free of double negatives?

Combine mandatory rows and final gates with `ALL`: every answer must be `Yes`.
Use `ANY` only among evidence routes declared interchangeable by the governing
contract before testing, where each route independently exercises the same
trigger and can falsify the same response. Convenience does not make routes
interchangeable.

Any `No` requires revision: return to the step that owns the gate. Do not
average results, accept a majority, replace the binary verdict with narrative
confidence, or call packaging validation behavioral proof.

**Has the user told you to ship a draft whose verdict is `REVISE`?**
   - **Yes:** Ship it, and report the verdict and every `No` gate with it.
   - **No:** Keep revising.

End with exactly one verdict:

- `ACCEPT — ALL gates pass.`
- `REVISE — one or more gates fail.`
