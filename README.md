# skills

Agent skills, installable with the [`skills`](https://github.com/vercel-labs/skills) CLI.

```bash
npx skills add natewalton/skills
```

| skill | what it does |
|---|---|
| `writing-style` | Write and edit clear, accurate, concise prose with enough detail for the reader to act. Includes linked benchmarks for reviewing business writing. |
| `narrative-writing` | Compose and revise posts, personal nonfiction, profiles, and founder stories. Develops perspective, narrative flow, and voice through literary examples and independent editorial review. Complements `writing-style`. |
| `spec-design` | Write, slice, and review evidence-backed specs that deliver standalone customer value. Keeps scope minimal, names the real user outcome, and rejects work that depends on future features to be useful. |
| `general-systems` | A design-review protocol that interrupts implementation to check whether you are solving the right problem at the right level of abstraction: name the system, count its concepts from the code, count exceptions, read the friction signals, then decide whether to bend the model or redesign it. |
| `delivery-coordination` | Ship deployments, persisted-data changes, collectors, and multi-agent work with a process smaller than the work: one owner by default, focused tests, dry-run and rollback for persisted writes, verification on the real user-facing surface, one tracker update at the end. No delivery JSON, review ledgers, or acknowledgement chains. |
| `falsification-first-skill-design` | Create, revise, slim, or review AI coding skills using falsification-first Yes/No gates and an explicit escape-hatch audit. Pairs with your agent's default skill-authoring skill (`skill-creator` is one). |
