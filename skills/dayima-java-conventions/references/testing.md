# Testing

- Keep the framework version used by the target project; do not upgrade JUnit or Spring test infrastructure incidentally.
- Name tests `*Test.java` and mirror the production package.
- Test real production implementations and observable results. Do not copy business logic into tests and verify that copy, or add tests that merely mirror implementation details.
- Add focused unit tests for changed decision logic and integration tests only when Spring wiring, transactions, SQL, or external configuration is part of the behavior.
- When Web behavior or contracts change, verify observable routing, HTTP methods, input binding, and the `ResultRsp` response shape as relevant. Do not add annotation-by-annotation or reflection-only tests that merely restate the controller implementation.
- Match test scope to business risk. For money, idempotency, and state-machine behavior, retain coverage of the relevant duplicate requests, invalid transitions, conditional-update conflicts, rollback, and boundary amounts.
- For persistence mappings that carry business risk, verify critical table names, ID types, money types, enum codes, and annotations.
- Prefer real objects. Use mocks or stubs only for external or nondeterministic dependencies needed by the test, with sanitized fixtures. Never call production endpoints or use real credentials.
- Run the smallest affected test first. Expand to module or multi-module tests when the change crosses boundaries.
- Report the exact command, pass/fail result, and any external prerequisite that prevented execution.
