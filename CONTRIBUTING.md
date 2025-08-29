# Contributing to ZamAI

Thanks for your interest in contributing! ZamAI is a modular AI platform focused on:
- Pluggable, composable pipelines
- Configuration‑driven experimentation
- Reproducible evaluation + observability
- Extensible tooling (plugins, adapters, runners)

This guide explains how to propose changes, add plugins, file issues, and maintain quality.

---

## 1. Quick Start (TL;DR)

1. Fork + clone the repo.
2. Create a branch: `git switch -c feat/pipeline-registry` (use a Conventional Commit–compatible prefix).
3. Install dev environment (see Setup below).
4. Run: `make install precommit test lint typecheck`
5. Make changes (add/update docs + tests).
6. Ensure pipeline examples still run: `make examples`
7. Open a Pull Request (PR) following the template; link related issue / design doc.
8. Address review feedback; squash or rebase as requested.
9. Maintainer merges when checks pass + standards met.

---

## 2. Code of Conduct

All contributors must follow the project’s Code of Conduct (see `CODE_OF_CONDUCT.md`). Report unacceptable behavior privately via: `security@EXAMPLE.com` (replace when set).

---

## 3. Governance & Decision Flow

| Change Type | Needs Issue? | Needs Design Proposal? | Reviewers | Notes |
|-------------|--------------|------------------------|-----------|-------|
| Typo / Docs | Optional | No | 1 | Straightforward |
| Bug Fix | Yes (if non-trivial) | Usually No | 1–2 | Include failing test first |
| Feature (minor) | Yes | Maybe | 2 | Show config + example |
| Feature (core architecture) | Yes | Yes (RFC / ADR) | 2–3 | Requires extensibility review |
| Plugin (new module) | Yes | Maybe | 2 | Must follow plugin guidelines |
| Breaking Change | Yes | Yes | 3 | Include migration notes |
| Security Patch | Yes (private first) | Maybe | Security Maintainer | Coordinated disclosure |

---

## 4. Issue Types & Filing Guidelines

Use clear labels (examples):

- `type:bug`
- `type:feature`
- `type:refactor`
- `type:docs`
- `type:perf`
- `priority:high|med|low`
- `area:pipeline|config|eval|observability|plugin|infra`

Template checklist for feature request:

```
### Summary
(1–2 sentence description)

### Problem / Motivation
(What user problem does this solve?)

### Proposed Solution
(Interfaces, config keys, workflow)

### Alternatives Considered
(Why not X?)

### Impact / Risks
(Perf? Complexity? Compatibility?)

### Testing Strategy
(Which layers / sample inputs?)

### Open Questions
(Any unresolved details)
```

---

## 5. Design Proposals (RFC / ADR)

For non-trivial features create `docs/rfcs/NNNN-short-title.md`:

Minimal structure:
```
# RFC: Short Title
Status: (Draft|Accepted|Rejected|Superseded)
Date:
Authors:
Discussion: (link to issue/PR/discussion)

## Context & Problem
## Goals / Non-Goals
## Proposed Design
- Diagrams (Mermaid)
- Public Interfaces
- Config Additions
## Data / Model Versioning Impact
## Security / Privacy Considerations
## Migration Plan
## Risks / Tradeoffs
## Alternatives
## Open Questions
```

Accepted decisions distilled into ADRs under `docs/adrs/`.

---

## 6. Architecture (High-Level)

Core layers (expected—adjust once repo structure is stable):

```
/core
  pipeline/         # Orchestration primitives
  config/           # Schema + loaders (YAML/TOML)
  registry/         # Component discovery
  evaluation/       # Metrics, scoring, regression harness
  observability/    # Logging, tracing, timing, structured events
/plugins            # First-party pluggable extensions
/examples           # End-to-end runnable samples
/docs               # User + developer docs
/tests              # Tests (mirroring src layout)
```

Principles:
- Pure functions for pipeline steps where feasible
- Explicit configuration schemas (pydantic / dataclasses)
- Dependency injection over hard-coded globals
- No side effects at import time (enables lazy loading)
- Deterministic evaluation (seed control)

---

## 7. Plugin System

A “plugin” extends core without modifying it.

Plugin requirements:
1. Directory: `plugins/<plugin_name>/`
2. Manifest file: `plugin.yaml` containing:
   ```yaml
   name: my-plugin
   version: 0.1.0
   entrypoints:
     pipelines:
       - my_plugin.pipelines:MyPipeline
     components:
       - my_plugin.vectorstores:FAISSStore
   compatibility:
     zamai_core: ">=0.1.0,<0.2.0"
   ```
3. Expose a `register(registry)` function OR rely on automatic entrypoint discovery.
4. Tests under `plugins/<plugin_name>/tests/`
5. Documentation page: `docs/plugins/<plugin_name>.md`
6. Avoid vendoring large models or datasets (reference instructions instead).

Versioning:
- Increment patch for bug fixes
- Minor for new components
- Breaking changes require alignment with core release

---

## 8. Configuration Conventions

- Central config object validated early
- Environment overrides namespaced: `ZAMAI__PIPELINE__BATCH_SIZE=32`
- Reserved config keys: `pipeline`, `components`, `resources`, `evaluation`
- Provide JSON Schema (export tool forthcoming) for editor integration

Example snippet:
```yaml
pipeline:
  name: text-classification
  steps:
    - load_data
    - tokenize
    - infer
    - evaluate
components:
  tokenizer:
    type: hf_tokenizer
    model: bert-base-uncased
  model:
    type: hf_sequence_classifier
    model: bert-base-uncased
evaluation:
  metrics:
    - accuracy
    - f1
```

---

## 9. Development Environment

Prerequisites:
- Python >= 3.x (define exact once fixed)
- Node.js (if UI / dashboard components)
- Make (optional convenience)

Setup:

```bash
git clone https://github.com/tasal9/zamai.git
cd zamai
python -m venv .venv
source .venv/bin/activate
pip install -U pip
make install      # or: pip install -e ".[dev]"
pre-commit install
```

Common tasks:
```
make lint
make format
make typecheck
make test
make coverage
make examples
make docs-serve
```

---

## 10. Coding Standards

Python:
- Use type hints everywhere (mypy strict mode)
- Limit function complexity; prefer composition
- Prefer dataclasses / pydantic models for structured data
- Logging: structured (JSON) via centralized logger; no bare print
- Avoid implicit state; pass context objects explicitly

JavaScript / TypeScript (if applicable):
- Prefer TypeScript for new code
- ESM modules
- ESLint + Prettier enforced

Style / Tools (suggested):
- Ruff / Flake8
- Black
- isort
- mypy (strict)
- pytest
- hypothesis (property-based tests where valuable)

---

## 11. Testing Strategy

Test taxonomy:
| Layer | Purpose | Example |
|-------|---------|---------|
| Unit | Pure logic correctness | tokenization, metric calc |
| Integration | Component interaction | pipeline assembly |
| Regression | Prevent quality drift | evaluation harness snapshots |
| Performance (optional gating) | Latency / throughput | model inference timing |
| Property-based | Edge invariants | idempotent normalization |

Guidelines:
- Every public component must have a test.
- Add failing test first for bug fixes.
- Avoid network calls in tests; mock or add fixtures.
- Keep test runtime reasonable (CI target < 8 min).

Coverage:
- Minimum (initial target): 80% statements / 70% branches; raise over time.

---

## 12. Evaluation & Metrics Contributions

When adding a metric:
1. Pure function: `def compute(preds, targets, **kwargs) -> MetricResult`
2. Include metadata: definition, direction (higher_is_better bool)
3. Provide unit tests with edge cases (empty, single-class)
4. Document in `docs/evaluation/metrics.md`

Regression harness:
- Add baseline JSON under `tests/regression/baselines/<scenario>.json`
- Include script to regenerate + diff.

---

## 13. Reproducibility & Experiment Logging

Best practices:
- Use deterministic seeds: pass `seed` via config root
- Track dataset versions (checksum / commit / URL)
- Log hyperparameters + git commit hash to run artifact
- Avoid non-deterministic device ordering unless documented

---

## 14. Performance Guidelines

Before optimizing:
- Add benchmark scenario under `tests/perf/`
- Collect baseline numbers
- Avoid micro-optimizations that reduce clarity
- Provide perf delta in PR description

---

## 15. Security & Responsible AI

Security:
- Report vulnerabilities privately (security@EXAMPLE.com placeholder)
- Avoid shipping secrets / tokens in examples
- Validate untrusted input at boundaries

Responsible Use:
- Document limitations (bias, domain constraints)
- Respect dataset licenses; do not commit proprietary data

---

## 16. Commit Messages (Conventional Commits)

Format:
```
<type>(optional scope): short description

Optional longer body

Refs: #ISSUE
```

Types:
`feat, fix, docs, style, refactor, perf, test, build, ci, chore, revert`

Examples:
- `feat(pipeline): add parallel execution graph`
- `fix(evaluation): correct macro F1 calculation (#42)`

---

## 17. Branching Model

- `main`: stable, releasable
- `dev` (optional): staging integration
- Feature branches: `feat/...`
- Fix branches: `fix/...`
- Docs-only: `docs/...`

Rebasing is preferred over merge commits for small PRs; keep history clean.

---

## 18. Pull Requests

Checklist (PR template will mirror):
- [ ] Linked issue / RFC
- [ ] Tests added / updated
- [ ] Docs updated (user + developer)
- [ ] No new flake8 / mypy / lint errors
- [ ] CI green
- [ ] Added example (if feature)
- [ ] Performance considerations documented (if relevant)
- [ ] No large binary blobs

Review Guidelines:
- Focus on correctness, clarity, cohesion, and interface stability
- Request changes if public API unclear or undocumented
- Encourage incremental iterations (avoid 2k+ line PRs)

---

## 19. Documentation

Locations:
- User guides: `docs/user/`
- Developer internals: `docs/dev/`
- Plugins: `docs/plugins/`
- RFCs / ADRs: `docs/rfcs/`, `docs/adrs/`

Rules:
- New public feature must include at least one example & doc page
- Keep README succinct; deep dives go into docs/
- Prefer Mermaid diagrams for flows:
  ```mermaid
  flowchart LR
    A[Config Loader] --> B[Pipeline Builder]
    B --> C[Executor]
    C --> D[Evaluation Harness]
  ```

---

## 20. Release Process (Draft)

1. Ensure milestone issues closed.
2. Bump version (`__version__` + changelog).
3. Changelog sections: Added / Changed / Deprecated / Removed / Fixed / Security
4. Tag: `vX.Y.Z`
5. GitHub Release + artifacts (if any)
6. Publish docs (CI)

Semantic versioning (SemVer):
- Patch: bug fix
- Minor: backward-compatible feature
- Major: breaking change (document migration)

---

## 21. License & Attribution

Project license: (Add chosen license, e.g., Apache-2.0)
Contributors agree that contributions fall under the project license.
Third-party code must retain attribution and compatible licenses.

---

## 22. Tooling & Automation (Planned / Suggested)

- pre-commit: formatting, lint, type, security (bandit)
- GitHub Actions:
  - CI (tests, lint)
  - Docs build
  - Release tagging
  - Dependency vulnerability scan

---

## 23. Local Make Targets (Example)

(Adjust once Makefile stabilizes)
```
make help         # List targets
make install
make update-deps
make lint
make format
make typecheck
make test
make coverage
make examples
make docs-build
make docs-serve
```

---

## 24. FAQ (Growing Section)

Q: My plugin isn't discovered?
A: Ensure `plugin.yaml` present and entrypoints map to importable symbols; run `python -m zamai.tools.list_plugins`.

Q: How do I add a new metric?
A: Implement function + tests + docs + baseline (see Sections 11 & 12).

Q: Can I embed large pretrained weights?
A: No, provide a download script / instruction.

---

## 25. Contact & Community

(Placeholder—fill in when channels exist)

- Discussions: GitHub Discussions (proposed)
- Security: security@EXAMPLE.com
- Real-time chat: (e.g., Discord / Slack TBD)

---

## 26. Glossary

| Term | Definition |
|------|------------|
| Pipeline Step | Discrete, composable function in execution graph |
| Component Registry | Maps symbolic names -> implementations |
| Evaluation Harness | Framework to run metrics & regression checks |
| Plugin | External package extending core behaviors |
| Config Schema | Validated structure defining pipeline + components |

---

## 27. Contributor Journey Map (Guidance)

1. First-time: pick `good first issue`
2. Intermediate: add metric / plugin
3. Advanced: refactor internal interface with RFC
4. Core: maintain registry / release pipeline

---

## 28. Future Enhancements (Meta-TODO)

(Track in issues, not here—examples)
- Config schema export tooling
- Structured event spec
- Realtime performance dashboards
- Plugin compatibility test matrix

---

Thank you for contributing to ZamAI! Your improvements help build a robust, extensible AI platform. If anything in this document is unclear, open a `type:docs` issue to improve it.