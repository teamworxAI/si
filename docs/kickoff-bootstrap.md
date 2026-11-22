# Claude Code kickoff: `bootstrap`, then `slice-one`

Version 1, 2026-11-21. Paste everything below the line into Claude Code, run from a
checkout of `teamworxAI/si` on a machine with Docker.

This could not be run from the cloud session that wrote it: that session's network
refuses container registries, so no gate could be executed there. Nothing in this
repository has been verified by a gate yet.

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

Send me one consolidated message with your open questions and the assumptions you
would otherwise make. Then wait.

## 1. Plan item `bootstrap`

Outcome metric moved: none directly. It unblocks "packs running end to end on one
platform commit" (Q11).

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

## 2. Plan item `slice-one`

Outcome metric moved: one pack running end to end, from zero to one.

One thin path through every layer, offline:

1. Admit one public manual from Torque's source register, with its release basis and
   checksum recorded. A source with no release basis is refused.
2. Parse it to typed records with lineage in PostgreSQL. Ingest accounting must add
   up: parsed + skipped = input.
3. Build the graph and the vectors through the store interface on adapter A
   (PostgreSQL only, Q10). Write the contract tests against the interface, not the
   adapter, so adapter B can pass them later unchanged.
4. One workflow from the step library: retrieve, compose, ground. Retrieval order is
   exact, keyword, graph, vector. The ground step drops any sentence whose numbers,
   units or quotes are not verbatim in a cited record.
5. One UI panel: ask, with the answer and its cited records side by side.
6. A trace span around every step, retrieval and model call, with a test asserting it.
   Inputs and outputs hidden by default.
7. Model calls go through the gateway only. Model-calling tests skip by name when no
   model host is reachable. CI never calls a model.

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
