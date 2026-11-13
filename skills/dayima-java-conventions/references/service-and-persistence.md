# Service and Persistence

## Services

- Define stable public service interfaces and place business orchestration in `*ServiceImpl` implementations.
- Put transactions on actual business transaction boundaries, not indiscriminately on controllers or pure reads.
- Keep controllers thin: accept identity and input, call services, and wrap successful output.
- Use a dedicated `Client` for each external protocol. Use `Handler + Registry` or an equivalent strategy when behavior varies by channel or platform.

## Consistency and state

- Validate the current state before every state transition.
- Protect transitions with conditional updates such as `WHERE id = ? AND status = ?` and require exactly one affected row.
- Back idempotency with a database unique constraint or business key. Do not rely only on check-then-insert application logic.
- For balances, inventory, quotas, and other critical aggregates, use versioned updates, conditional updates, or a necessary row lock to prevent lost updates.
- Keep immutable ledger or flow records append-only; represent reversals with compensating records rather than destructive edits.

## Persistence

- In projects already using XBatis, use its Mapper and `QueryChain` for simple CRUD, predicates, ordering, pagination, and counts. Retain existing MyBatis DAO/XML conventions in MyBatis projects; formatting work does not authorize an ORM migration.
- Use explicit DAO methods and Mapper XML for joins, conditional updates, row locks, bulk operations, or performance-sensitive SQL.
- Name Mapper XML files in lowercase snake_case and keep Java parameters and result mappings consistent.
- Store money in a suitable `DECIMAL` column and map it to `BigDecimal`.
- Store business time in a sortable database temporal type.
- Verify table names, identifier types, money types, and critical annotations with focused metadata tests when these mappings carry business risk.

## Mapper method names

Use the operation and domain object to make a method understandable at its call site:

| Operation | Naming | Example |
| --- | --- | --- |
| Single record | `get` + object; add lookup key when useful | `getFlow`, `getOrderByRequestNo` |
| Record list | `list` + plural objects and meaningful filters | `listFlows`, `listActiveBindings` |
| Insert | `insert` + object; name idempotent semantics explicitly | `insertAccountIfAbsent` |
| Update | `update` + object or changed business state | `updateAccountBalance`, `updateAuthResult` |
| Aggregate | `count` or `sum` + the measured object/value | `sumPendingWithdrawAmount` |
| Lock or claim | Name the concurrency operation | `lockAccount`, `claimWithdrawOrder` |
| Other mutation | Name the business action | `unbindPayoutAccount`, `resolveReconcileDifference` |

- Do not use ambiguous names such as `accountStatus` for an update, `authResult` for saving a result, or `save` when insert/update semantics are known. Keep framework-required method names unchanged.
- Use meaningful `@Param` names such as `account`, `order`, and `leaseUntil`, rather than `a`, `o`, or `until`. Update placeholders and dynamic SQL expressions together.
- Rename the Java declaration and XML statement ID together; synchronize all callers, method references, reflection-based tests, and relevant documentation. Verify that Java methods still match mapped statements.

## MyBatis XML and SQL formatting

- Use four-space XML indentation and a blank line between top-level mappings, SQL fragments, and statements. Expand dynamic tags such as `<if>`, `<where>`, and `<set>` so their content is on separate indented lines.
- Put major SQL clauses on separate lines: `SELECT`, `FROM`, `WHERE`, `ORDER BY`, `LIMIT`, and `FOR UPDATE`. Put long column lists, `INSERT` columns/values, and `SET` assignments on separate lines so correspondence is easy to inspect.
- Indent continuation predicates consistently; use spaces around operators and after commas. Keep function calls such as `VALUES(uid)` readable and preserve XML escaping for comparisons.
- A formatting pass must preserve SQL semantics, parameters, result mappings, predicates, and lock order. For identifier renames, check the corresponding bindings; use focused mapper tests when names or bindings change rather than adding tests for whitespace.
