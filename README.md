# Common Context Structure (CCS)

**The standard folder structure of a knowledge vault that stores the derivation chain from a concept (Identity) to an executable skill (Skill) as a file-link graph.**

Every concept is completed into an executable skill through a five-stage derivation chain, and each stage is recorded as exactly one markdown file with explicit links. A skill is never a file written from someone's head — it is the **terminus of a chain**.

```
Identity ──definesGoal──▶ Goal ──requiresTask──▶ Task ──requiresKnowledge──▶ Knowledge ──appliedThrough──▶ Method ──developsSkill──▶ Skill
(concept)                 (goal)                 (task)                      (knowledge)                   (method)                   (skill)
```

---

## Folder Structure

```
Common Context Structure\
│
│  ◆ Derivation chain (one file per concept in each folder)
├── _identity\        Concept definitions — the root of everything.   <UPPERCASE_CONCEPT>.md
├── _goal\            Chain stage 1: goal.                            <concept>_goal.md
├── _task\            Chain stage 2: task.                            <concept>_task.md
├── _knowledge\       Chain stage 3: knowledge (criteria).            <concept>_knowledge.md
├── _method\          Chain stage 4: method (procedure).              <concept>_method.md
├── _skill\           Chain terminus: canonical skills.               <name>_skill\SKILL.md
│                     └─ composites also carry edges\<edge>_invocation_skill\SKILL.md
│
│  ◆ Composite authoring records (9 stages of skill bundling, one file per stage)
├── _entity\
│   ├── _suite\          1. member grouping                <name>_suite.md
│   ├── _edge\           2. dependency-edge identification <name>_edge.md
│   ├── _sequence\       3. execution-order composition    <name>_sequence.md
│   ├── _mode\           4. sync/parallel classification   <name>_mode.md
│   ├── _sync\           5a. synchronous guarantees        <name>_sync.md
│   ├── _async\          5b. parallel fan-out/join         <name>_async.md (fork-shaped composites only)
│   ├── _invocation\     6. invocation plan                <name>_invocation.md
│   ├── _agent\          7. context assignment             <name>_agent.md
│   ├── _workflow\       8. workflow assembly              <name>_workflow.md
│   ├── _composite\      9. sealed interface               <name>_composite.md
│   └── _record\         authoring-run state records       <name>_authoring_run.md
│
│  ◆ Deployment staging (canonical → runtime registry, 6 stages)
└── _deploy\
    ├── _closure\        1. closure confirmation           <stamp>_<skill>_closure.md
    ├── _baseline\       2. baseline snapshot (container)  …_baseline\ = runtime package + _chain\ + manifest.md
    ├── _transform\      3. transform (container)          …_transform\<skill>\ = lean registry-bound package
    ├── _verification\   4. three-gate verification        <stamp>_<skill>_account.md (PROVEN/UNPROVEN)
    ├── _landing\        5. landing fact                   <stamp>_<skill>_landing.md
    └── _record\         6. deployment record (vault-side) <stamp>_<skill>_record.md
```

---

## Role of Each Folder

### 1. Derivation chain — `_identity` → `_goal` → `_task` → `_knowledge` → `_method`

| Folder | Role | File contents | Chain links |
|---|---|---|---|
| `_identity/` | Declares every concept in the workspace as **one concept = one file**. Every folder, chain, and skill traces back here | Meaning (what it IS / is NOT) · Boundary (fences against neighboring concepts) · Entity Home Pattern (where instances live) · Skill Derivation | `→ definesGoal` |
| `_goal/` | What the concept aims at | What this concept is trying to achieve | `← definesGoal` / `→ requiresTask` |
| `_task/` | The action the goal requires | The concrete action needed to reach the goal (stated in steps) | `← requiresTask` / `→ requiresKnowledge` |
| `_knowledge/` | The judgment criteria the task presupposes | The validity-criteria list — what counts as PASS/FAIL | `← requiresKnowledge` / `→ appliedThrough` |
| `_method/` | The procedure that applies the knowledge | Numbered procedure steps (including STOP conditions) | `← appliedThrough` / `→ developsSkill` |

### 2. Canonical skills — `_skill/`

| Kind | Location | Contents |
|---|---|---|
| Canonical skill | `_skill/<name>_skill/SKILL.md` (folder name == frontmatter `name:`) | An **atomic** skill states its own Procedure; a **composite** skill states its member/edge roster and the sealed interface (INTERFACE / HIDDEN WORKFLOW) |
| Edge callers | `_skill/<composite>_skill/edges/<edge>_invocation_skill/SKILL.md` | Own the calling behavior between composite members (atomic members stay unchanged): producer→consumer order · finalize-then-read guarantee · value-level bindings · PASS-only gates |

### 3. Composite authoring records — `_entity/`

The stage-by-stage outputs of the skill-bundling route **SUITE→EDGE→SEQUENCE→MODE→SYNC/ASYNC→INVOCATION→AGENT→WORKFLOW→COMPOSITE**, kept in per-type subfolders. Each authored composite leaves one `<name>_<type>.md` file in each subfolder — its complete audit trail. Every entity file carries the `## Links` contract (identity / producedBy / usesSkill / reads / writes / nextSkill / nextInput).

### 4. Deployment staging — `_deploy/`

The six stages that export a canonical skill to the runtime registry. **Landing (stage 5) happens only when verification (stage 4) reads PROVEN.**

| Stage | Folder | What it does |
|---|---|---|
| 1 | `_closure/` | Enumerate and confirm the skill's runtime dependency set (SKILL.md→SKILL.md) to its fixed point |
| 2 | `_baseline/` | Self-contained baseline snapshot — runtime package + derivation chain (`_chain/`) + `manifest.md`, links re-anchored |
| 3 | `_transform/` | The lean registry-bound package — runtime links kept, provenance links de-linked to plain text (zero dangling) |
| 4 | `_verification/` | Three-gate AND verdict: masked-byte-identity / package-resolution / registry-callability → PROVEN/UNPROVEN |
| 5 | `_landing/` | The minimal fact of what was copied to the registry, only on PROVEN (status CALLABLE) |
| 6 | `_record/` | The vault-side standing record of the deployment event — never placed inside the registry (vault-independent) |

---

## Design Principles

1. **No folder exists without a concept** — every folder name matches a backing `_identity/<UPPERCASE>.md` exactly. To create a folder, define the concept first.
2. **A skill is the terminus of a chain** — a skill is generated only after Identity→Goal→Task→Knowledge→Method has been built forward.
3. **Canonical and copies are separated** — this structure holds canonicals only. Executable copies leave through the six `_deploy` stages into the runtime registry, and domain run outputs land under a separate runRoot, never written back into the canonical vault.
4. **Every link is verified** — chain, member, and edge links are kept at zero dangling.

## Naming Laws

- Leading `_` = **folder marker** — never used in a file name (no exceptions)
- Folder name ↔ backing `_identity/<UPPERCASE>.md` must match exactly
- Skill folders are `<name>_skill`, and the folder name == the frontmatter `name:` field
- Direct `_deploy` stage artifacts are named `<stamp>_<source_skill>_<stage>.md` (baseline/transform are container folders)

---

> This repository is the **structural skeleton** of CCS. For the full specification of the living reference implementation (102 concepts · 98 completed chains · 112 canonical skills · 15 composites · 4 deployed runs, as of 2026-07-07), see `common_context_folder_structure_0.0.2.md`.
