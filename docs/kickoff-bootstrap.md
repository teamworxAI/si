# Claude Code kickoff: `bootstrap` through `first-chain`

Version 3, 2026-11-22. Paste everything below the line into Claude Code, run from a
checkout of `teamworxAI/si` on a machine with Docker.

This could not be run from the cloud sessions that wrote it. Nothing in this
repository has been verified by a gate yet. What the cloud session of 2026-11-22
established, so the local run does not repeat it:

- That session had the Docker CLI but no daemon, and its network refused container
  registries. No gate was executed and no code was written.
- `humanaxiom/Knowledge-engine` is public and its `HEAD` was `7f8559a`.
  `humanaxiom/Teamworx-Torque` and `teamworxAI/campus-ai` are private: the local
  machine's git credentials must be able to read both before `bootstrap` starts.
- The section 0 questions were already asked. They are recorded with their working
  assumptions as Q24 to Q31 in `docs/open-questions.md`. The owner has not ruled on
  them yet.

---

You are working in `teamworxAI/si`. Treat this folder as the only working repo and do
not switch folders without asking. Read `README.md`, `docs/architecture.md` and
`docs/open-questions.md` first. They are the design and the owner's rulings.

You may read these as references. Record the commit you port from, and port tests with
the code.

| Repository | Commit | Take |
|---|---|---|
| `humanaxiom/Teamworx-Torque` | `5429eff` (main) | Harness: `AGENTS.md`, `CLAUDE.md`, `.claude/`, `.github/`, `scripts/verify.sh` and the gate scripts, the commit hook, `harness_telemetry`, the gates image, the walking skeleton and its tests |
| `humanaxiom/Teamworx-Torque` | `77f6e3c` (`agent/demo-industries`) | Source register and provenance gate, grounding rules, public fixtures |
| `humanaxiom/Knowledge-engine` | `7f8559a` | Per-pass model config, offline bundle and SBOM scripts (later items) |
| `teamworxAI/campus-ai` | `06136c5` | Licence guard test, data-class gate, model register |

## 0. Before any code

Q24 to Q31 in `docs/open-questions.md` are the questions a first read of this prompt
raises, each with a working assumption. Do not ask them again. Send me one
consolidated message that lists which of A10 to A20 and A24 to A31 you will build on,
plus any new question and the assumption you would otherwise make, added to the
register at the end. Then wait.

Before that message, check and report three things: the Docker daemon runs and can
pull an image, both private reference repositories are readable at the commits in the
table above, and the working branch.

## 1. Plan item `bootstrap`

Outcome metric moved: none directly. It unblocks both numbers in Q11.

1. Port Torque's harness as it stands at `5429eff`. Keep every rule and gate. Do not
   weaken any.
2. Rename, do not fork the behaviour:
   - product name `si`; Python package `si/`;
   - image and compose prefixes `si-`; environment prefix `SI_`;
   - host ports in 47300 to 47399, loopback only (Q17), so it runs beside Torque.
3. Do not copy Torque's product documents. `docs/architecture.md` and
   `docs/open-questions.md` in this repo already exist; convert the architecture into
   the harness's ADR form (`ADR-SI-<n>`) without changing a decision. Start
   `docs/requirements.md` empty of Torque criteria.
4. `LICENSE` stays MIT, copyright Teamworx Innovations.
5. One verification entry point, `scripts/verify.sh [fast|full]`, in a no-network gates
   container, the same command in CI.
6. The walking skeleton serves health and a store probe, with the egress boundary
   enforced at startup and at the network. The records store is PostgreSQL (the
   architecture's system of record), not SQLite. This is the one place the skeleton
   differs from Torque's, so write its ADR first and say what changes.
7. Add from day one, each with a test that can fail:
   - an egress allowlist that is empty by default and refuses to start otherwise;
   - a scan that fails the build on any hardcoded port, host or model name;
   - a licence table test in the style of campus-ai's `tests/test_licences.py`;
   - a test that no image, dependency or weight is fetched at run time.
8. Prove it: dispatch each role subagent once on a trivial task and keep the
   transcript as evidence. Run both modes of `verify.sh` on a clean checkout.

## 2. Plan items `block-contracts`, `core-services`, `first-chain`

The platform is generic (Q21). It ships no use case. Every capability is a block with
a typed contract, and a use case is a pack that selects and wires blocks. Read
`docs/architecture.md` section 5 before designing anything.

### `block-contracts`

Outcome metric moved: none directly; it makes the first configuration possible.

1. The block interface: typed input and output, parameter schema, kind (`model` or
   `deterministic`), a trace span and a provenance record on every run.
2. The pack loader and validator. A pack that wires incompatible blocks fails to
   load. A workflow that publishes without a grounding block and a human review block
   fails to load.
3. A toy pack under `tests/packs/` with no real domain in it. Every block's contract
   tests run against it.
4. A scan that fails the build when any pack name, variant name or pack vocabulary
   appears in platform code.
5. Packs are data only (Q22). No pack-local code.

### `core-services`

Records store with lineage and append-only revisions enforced by database triggers,
the hash-chained audit log with a verify command, tracing, and a thin model gateway
with the model register. These are shared services, not blocks, and no pack can turn
them off.

### `first-chain`

Outcome metric moved: configurations running end to end, from zero to one.

The smallest set of blocks that answers a question from one configuration, offline:

1. Blocks: admit, parse PDF text, index (keyword, graph, vector), retrieve, compose,
   ground, and the ask panel.
2. The configuration is `examples/maintenance`, on one public manual from Torque's
   source register, with its release basis and checksum recorded. It is an example
   and an acceptance test, not part of the platform.
3. Ingest accounting must add up: parsed + skipped = input.
4. Graph and vectors go through the store interface on adapter A (PostgreSQL only,
   Q10). Contract tests are written against the interface, so adapter B can pass them
   later unchanged.
5. Retrieval order is exact, keyword, graph, vector. The ground block drops any
   sentence whose numbers, units or quotes are not verbatim in a cited record.
6. Model calls go through the gateway only. Model-calling tests skip by name when no
   model host is reachable. CI never calls a model.

Do not build blocks that no configuration uses yet. Each later block arrives with the
configuration change that needs it.

## 3. Constraints on every item

- No Chinese models: no weights from organisations in the People's Republic of China
  and no fine-tune of them (Q3). The default compose model is `gpt-oss:20b`, the
  default embedding model `nomic-embed-text`. Both are configuration.
- Air-gapped. Everything runs in Docker. Only the model server is on bare metal.
- No hardcoding. No invented data, URLs or figures.
- TDD, coverage at least 80%, end-to-end smoke after every iteration.
- No customer, SFU or personal data in the repository (Q18).
- Never commit to `main`. Pushing and PRs need my explicit approval.
- Iteration cap of 5, then escalate with evidence paths.
- At the end of each item give me: a short status, the outcome metric now and before,
  the evidence paths, and the next item.
