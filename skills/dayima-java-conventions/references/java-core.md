# Java Core

## Compatibility and formatting

- Use only syntax and APIs supported by the Java version declared by the project.
- Preserve UTF-8, four-space indentation, declaration-line braces, and nearby import grouping.
- Use explicit imports and simple Java type names in declarations and signatures; qualify a name only when an import would be ambiguous. Avoid wildcard imports in new or edited imports.
- Put each meaningful stream stage on its own line. Wrap long parameter lists and boolean expressions at semantic boundaries; use named local predicates when a condition combines distinct business decisions.
- Avoid unrelated formatting and broad refactors.

## Naming and boundaries

- Use role-revealing names and established suffixes: `*Service`, `*ServiceImpl`, `*Dao`, `*DaoImpl`, `*Request`, `*Handler`, and `*Client`. Prefer `Vo` for new response models when the project has no established suffix; retain an existing `VO` convention. Avoid introducing `View` for Java response models.
- Name methods for their action and business object. Distinguish retrieval, validation, normalization, parsing, generation, and mutation: `getFlow`, `validateBusinessNo`, `normalizePageSize`, `parseMoney`, `generateOrderNo`, `recordAccountChange`. Avoid bare nouns such as `flow` or `authResult`, and vague verbs such as `process` or `change` when the business action can be named.
- Use intent-revealing parameters and locals such as `account`, `order`, `oldState`, and `leaseUntil`; reserve short names such as `i` for obvious loop counters. Boolean methods and predicates should read as conditions, such as `isSuccessful` or `matchesPaymentSnapshot`.
- Keep contracts, application logic, persistence, external integrations, and Web adapters separate. Express the separation with modules when the project is multi-module and packages when it is not.
- Group shared entities and public service interfaces by domain under the project's existing API package roots. For projects using `api.model` and `api.interfaces`, use `api.model.<domain>` and `api.interfaces.<domain>`; retain established `entity` layouts elsewhere. Keep DAOs, business implementations, and mapping implementations in their owning implementation module.
- Give each class one primary responsibility. Isolate external protocols in clients and variable channel behavior behind handlers or strategies.

## Readable calls and object conversion

- Save DAO/RPC/service results to local variables before VO conversion, response wrapping, or another business call. Fetch a list before iterating or streaming over it; do not embed the query in a `for` header or a long query-to-VO chain.
- Separate business queries and state-changing calls from conditions, using named results or affected-row counts. Preserve short-circuit evaluation and transaction/lock order when splitting an expression. Do not create locals for every getter or trivial conversion.
- Prefer MapStruct for entity/DTO/VO mappings instead of reflection-based `BeanUtils.copyProperties`. Reuse existing mapper configuration; the mall-style default is `@Mapper` with `INSTANCE = Mappers.getMapper(...)`. If the project already injects generated mappers, follow that component model.
- Let generated code map matching fields. Use explicit mappings for renamed fields, explicitly ignore fields populated by business orchestration, and use `@AfterMapping` for display processing such as masking. Conversion must not overwrite source entities with display values. Do not add a wrapper that only delegates to the generated mapper.
- Reuse the project's compatible MapStruct version and annotation-processing configuration, including Lombok integration when needed. Keep generated implementations in build output and verify them by compilation; do not hand-edit generated Java or force a version copied from another project's stack.

## Types and documentation

- Prefer `Long` for persistent identifiers.
- Use `BigDecimal` for money and rates; define scale and rounding where division or normalization occurs. Never use `double` or `float` for money.
- Prefer `LocalDateTime` for new local business timestamps where compatible with the project's contracts; retain instant/offset types when those semantics are required. Do not store or pass formatted strings when a temporal type is available, or change an existing time contract as a formatting cleanup.
- Document public contracts, entities, and business fields with meaningful Javadoc. Do not repeat identical interface Javadoc in implementations.
- Keep every Javadoc block expanded, including short descriptions and `@author`/`@since` tags where used. Describe the operation or field directly; avoid comments that merely repeat a URL or say which file maintains a code.
- Prefer numeric codes for new status fields when the project does not require another representation. Group constants by business meaning; for interface-based constants, use a domain status interface with nested interfaces for distinct status groups. Link a field to its actual group with `@see`, such as `@see OrderStatus.Payment`.
- Preserve existing status codes and serialization contracts during cleanup. Persist enums with stable explicit codes, never ordinal values; keep the project's handling of unknown codes.

## Errors and security

- Use the project's shared business exception and centralized exception mapping.
- Use `try/catch` only for meaningful recovery, compensation, exception translation, or necessary boundary handling. Prefer `try-with-resources` for resource cleanup. Do not swallow exceptions, wrap them with the same meaning at every layer, or repeatedly log and rethrow them.
- Validate required external input once at its actual system boundary. For internal values already validated and guaranteed by types or contracts, do not repeat null checks, range checks, or defensive fallbacks merely because another method or layer receives them.
- Enforce business state, authorization, balance, and other domain invariants in the service or transaction that owns them. These checks concern business facts that can change; they do not justify repeating unchanged input validation across layers.
- Never add credentials, tokens, private keys, production endpoints, or real account data to source, SQL, logs, examples, or tests.
