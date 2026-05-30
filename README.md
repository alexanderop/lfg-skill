# lfg — Autonomous Engineering Autopilot (self-contained skill)

A single, **self-contained** agent skill that runs an entire software task end-to-end, hands-off:
recall prior learnings → plan → implement with TDD → review & autofix → verify → commit, push,
open PR → watch CI and fix failures → compound the learning.

`/lfg [feature description]` runs the whole pipeline **without stopping to ask anything**. It is an
explicit opt-in autopilot — use it only when you want hands-off execution of a described task.

## Pipeline

```mermaid
flowchart TD
    Start(["/lfg feature description"]) --> S0

    S0["Step 0 · Isolate<br/>using-git-worktrees"] --> G0{Branch / worktree<br/>exists?}
    G0 -->|no| S0
    G0 -->|yes| S1["Step 1 · Recall<br/>search docs/solutions/"]

    S1 --> S2["Step 2 · Plan<br/>writing-plans"]
    S2 --> G2{Plan file<br/>written?}
    G2 -->|no| S2
    G2 -->|yes| S3["Step 3 · Implement<br/>subagent-driven-development + TDD"]

    S3 --> G3{Code changed<br/>+ tests exist?}
    G3 -->|no| S3
    G3 -->|yes| S4["Step 4 · Review + autofix<br/>requesting / receiving-code-review"]

    S4 --> S5["Step 5 · Verify<br/>verification-before-completion"]
    S5 --> G5{Tests + app<br/>green?}
    G5 -->|red| DBG["systematic-debugging<br/>fix root cause"]
    DBG --> S5
    G5 -->|green| S6["Step 6 · Ship<br/>finishing-a-development-branch → open PR"]

    S6 --> G6{PR exists<br/>or branch pushed?}
    G6 -->|no| S6
    G6 -->|yes| S7["Step 7 · CI watch + autofix<br/>fix-pipeline · max 3 cycles"]

    S7 --> G7{CI green?}
    G7 -->|red, &lt;3 cycles| S7
    G7 -->|red, 3 exhausted| RES["Record residual<br/>on PR body"]
    G7 -->|green| S8
    RES --> S8["Step 8 · Compound<br/>document learning → docs/solutions/"]

    S8 --> Done(["Step 9 · DONE<br/>promise + summary"])

    classDef gate fill:#fff3cd,stroke:#d39e00,color:#000;
    classDef step fill:#e7f1ff,stroke:#0d6efd,color:#000;
    classDef terminal fill:#d1e7dd,stroke:#198754,color:#000;
    classDef fix fill:#f8d7da,stroke:#dc3545,color:#000;
    class G0,G2,G3,G5,G6,G7 gate;
    class S0,S1,S2,S3,S4,S5,S6,S7,S8 step;
    class Start,Done terminal;
    class DBG,RES fix;
```

## Why this exists

The pipeline is normally an orchestrator that invokes a dozen separately-installed skills. This
repo **bundles every technique it needs into one folder**, so there is **zero dependency on any
plugin or skill library**. Copy the folder anywhere and it runs.

## Layout

```
lfg/
├── SKILL.md            # the orchestrator pipeline (Steps 0–9)
└── references/         # 13 bundled technique skills, each in its own folder
    ├── using-git-worktrees/
    ├── writing-plans/
    ├── subagent-driven-development/
    ├── test-driven-development/
    ├── executing-plans/
    ├── requesting-code-review/
    ├── receiving-code-review/
    ├── verification-before-completion/
    ├── systematic-debugging/
    ├── finishing-a-development-branch/
    ├── fix-pipeline/
    ├── compound/
    └── dispatching-parallel-agents/
```

Each step in `SKILL.md` reads the relevant `references/<name>/SKILL.md` and follows it — no `Skill`
tool, no `superpowers:` namespace required.

## Install

Drop the `lfg/` folder into your agent's skills directory, e.g.:

```bash
# Claude Code
git clone https://github.com/alexanderop/lfg-skill ~/.claude/skills/lfg

# or any harness that loads skills from a folder
cp -R lfg /path/to/your/skills/
```

Then invoke `/lfg <feature description>`.

## Caveat

This is a **snapshot** of the bundled techniques at the time it was assembled. It does not
auto-sync with upstream improvements.

## Attribution & License

Most bundled techniques under `references/` are derived from
[Superpowers](https://github.com/obra/superpowers) by Jesse Vincent, used under the MIT License.
The original copyright notice is preserved in [LICENSE](./LICENSE). This bundle is also released
under the MIT License.
