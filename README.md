# si

A sovereign, air-gapped knowledge platform from Teamworx Innovations. Many data
sources go through ETL into records, a graph and vectors. Configurable workflows
answer from those stores with citations, a configurable UI presents them, and one set
of observability, provenance and monitoring runs under all of it.

A use case is a pack: configuration, not a fork. The first two packs are `maintenance`
(manuals, parts diagrams linked to parts lists, reviewed procedure drafts) and
`research` (cited retrieval over a document corpus).

## Status

Design only. No code exists yet.

| Document | What it holds |
|---|---|
| [docs/architecture.md](docs/architecture.md) | The architecture, version 0.3 |
| [docs/open-questions.md](docs/open-questions.md) | Rulings made so far and the questions still open |
| [docs/kickoff-bootstrap.md](docs/kickoff-bootstrap.md) | The Claude Code prompt that starts the build |

## Rules that do not change

- Air-gapped: nothing leaves the deployment unless the egress allowlist names it, and
  the allowlist is empty by default.
- No Chinese models, including fine-tunes of them.
- AI is the interface, not the source of truth. Records and rules produce the answer,
  the model words it, a deterministic check judges the wording, and a human approves
  anything that publishes.
- Everything runs in Docker. Ports, hosts and model names are configuration.

## Licence

MIT, copyright Teamworx Innovations. See [LICENSE](LICENSE). The licence is to be
revisited at go-to-market.
