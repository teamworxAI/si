# Open questions and working assumptions

Each question names the assumption the plan uses until the owner answers it. Answer by
number. Numbering is stable: never renumber, and add new questions at the end.

- `Q<n>` is the question, `A<n>` the working assumption.
- A resolved question is marked `**Resolved <date>.**` and keeps its text.

## Resolved

- **Q1. New platform, or build on an existing repository?** **Resolved 2026-11-21.**
  Net new. One configurable platform that takes lessons and the best pieces from all
  earlier builds.
- **Q2. Ownership and licence.** **Resolved 2026-11-21.** Teamworx owns this code. MIT
  for now, until go-to-market (see Q12).
- **Q3. Model origin.** **Resolved 2026-11-21.** No Chinese models. This platform reads
  that as: weights published by organisations in the People's Republic of China, and
  any fine-tune or derivative of such weights. (A3: the reading in the second sentence
  is the architect's; confirm or narrow it.)
- **Q4. Network boundary.** **Resolved 2026-11-21.** Air-gapped.
- **Q5. Frontier models.** **Resolved 2026-11-21.** Through a secure custom deployment
  in AWS or Azure, as a later option only. Not built in the first release.
- **Q6. Meaning of dual use.** **Resolved 2026-11-21.** Civil plus defence customers.
- **Q7. Graph and vector store.** **Resolved 2026-11-21.** "Both as needed":
  PostgreSQL-only and Neo4j, behind one interface.
- **Q8. First vertical slice.** **Resolved 2026-11-21.** The `maintenance` pack. Read with
  Q21: it is the first reference configuration, kept under `examples/`, not part of the
  platform.
- **Q9. Repository.** **Resolved 2026-11-21.** `teamworxAI/si`.
- **Q21. Generic or use-case first?** **Resolved 2026-11-21.** Generic platform. The
  ingredients are building blocks, configured for each client and use case. Nothing is
  baked in.
- **Q22. Are packs data only?** **Resolved 2026-11-21.** Yes. A pack holds no code. A
  capability the catalogue lacks is added as a generic block through the gates, then
  configured. No pack-local plugins.
- **Q23. Where do client packs live?** **Resolved 2026-11-21.** Outside this repository,
  one private repository per client. This repository keeps only `examples/` and
  `tests/packs/`.

## Open

- **Q10. Which adapter does `first-chain` use?** **A10:** PostgreSQL-only (adapter A),
  because it is one service fewer and the first chain should be thin. Neo4j (adapter B)
  follows in `graph-neo4j` against the same contract tests.
- **Q11. Outcome metric.** **A11:** two numbers. Configurations running end to end on
  one platform commit (target one at `first-chain`, two at `second-config`), and blocks
  used by more than one configuration.
- **Q12. Licence at go-to-market.** **A12:** not decided. MIT stands until then.
- **Q13. Copyright holder's legal name.** `LICENSE` says "Teamworx Innovations".
  **A13:** that is the correct legal name; correct it here if not.
- **Q14. Read-aloud engine.** No engine is chosen. Torque's first choice was a
  Chinese-origin model, which Q3 excludes here. **A14:** read-aloud ships disabled
  with a named reason until an engine passes the origin and licence checks.
- **Q15. Reranker.** The earlier research pick was a Chinese-origin model. **A15:** no
  reranker in the first release; retrieval order is exact, keyword, graph, vector.
- **Q16. Tracing backend in the shipped product.** Arize Phoenix is under Elastic
  License 2.0, which is not an open-source licence. **A16:** Phoenix is used in
  development only; the shipped trace store is decided in `release`.
- **Q17. Port block.** Torque uses host ports 47200 to 47299. **A17:** si uses 47300 to
  47399, loopback only.
- **Q18. Fixtures.** **A18:** only public-domain or Distribution A US Government works,
  each with its release basis recorded, as in Torque's source register. No customer,
  SFU or personal data in the repository.
- **Q19. Controlled Goods Program and export control.** Torque left two actions open:
  naming the accountable compliance owner, and asking counsel whether registration
  under the Controlled Goods Program is needed before handling customer data.
  **A19:** both carry over to this platform and gate a defence pilot, not the build.
- **Q20. Languages.** **A20:** English and French from the first release, from one
  enabled list, as in Torque.
