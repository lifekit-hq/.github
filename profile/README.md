<p align="center">
  <img src="assets/lifekit-logo.png" alt="lifekit logo" width="140">
</p>

<h1 align="center">lifekit</h1>

<p align="center"><b>A self-hosted personal-AI stack - and the tools that run it.</b></p>

---

lifekit is built as a **federation, not a monolith**: one system, many repositories,
each cloneable, buildable, and shippable on its own. The hierarchy lives in *declaration
and wiring*, never in nested code - so any part can be replaced without disturbing the rest.

## The shape

```
lifekit-stack                  ← trunk: declares + composes the stack (compose + env schema)
   ├── operator agents          the human-facing layer
   │        │  consumes (MCP)
   ├── devclaw  ◄───────────────┘  a self-driving software-development loop, behind MCP
   └── surfaces & agents        dashboards, health, domain workers
```

`devclaw` is a **peer** service, not a child of any one agent - it sits behind MCP, so any
client can reach it. Operators *consume* it; they don't *own* it. That single boundary is
what keeps the whole thing a federation instead of a tangle.

## Open repositories

| Repo | What it is |
|---|---|
| [**lifekit-stack**](https://github.com/lifekit-hq/lifekit-stack) | Composition root - infrastructure-as-code that deploys the whole stack to a fresh VPS |
| [**lifekit**](https://github.com/lifekit-hq/lifekit) | The file-based personal-AI framework (the engine) |
| [**devclaw**](https://github.com/lifekit-hq/devclaw) | Durable-goal software-development loop: plan → sandboxed execution → verify gate → iterate |
| [**finance-sentry**](https://github.com/lifekit-hq/finance-sentry) | Personal finance platform: bank, crypto, and brokerage sync with budgets and alerts |
| [**lifekit-health**](https://github.com/lifekit-hq/lifekit-health) | Health module: workout tracking + daily-state capture behind one MCP server |
| [**life-state**](https://github.com/lifekit-hq/life-state) | Daily mood/energy/soreness/sleep capture primitive |

*(Instance-specific configuration and operator surfaces are kept private.)*

## The three rules that keep it a federation

1. **A change belongs to exactly one repository.** If a change must touch two at once, an edge
   got too thick - push the coupling back out to a declaration.
2. **Edges are declared and thin.** A component knows another only through a declared interface
   (an MCP tool, a URL, an env value) - never a code import.
3. **Surfaces federate, they don't merge.** One thin top shell links down the tree; the
   individual consoles stay independently shippable.

## Contributing

Issues and PRs are welcome on any public repository - see the
[contributing guide](https://github.com/lifekit-hq/.github/blob/main/CONTRIBUTING.md).
Security reports go through
[private advisories](https://github.com/lifekit-hq/.github/blob/main/SECURITY.md), never public issues.
