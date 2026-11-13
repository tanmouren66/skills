---
name: dayima-java-conventions
description: Apply Dayima-wide Java engineering conventions when creating, modifying, refactoring, reviewing, or designing Java code, tests, Maven or Gradle configuration, Spring MVC controllers, Dubbo services, XBatis or MyBatis persistence, handlers, clients, entities, DTOs, or VOs. Use for Java tasks across all projects; inspect the target project's AGENTS.md and build files before applying stack-specific rules.
---

# Dayima Java Conventions

## Workflow

1. Read every applicable `AGENTS.md` in the target project.
2. Inspect `pom.xml` or Gradle files to identify Java and framework versions.
3. Read the target files and nearby representative code. For an authorized cleanup, review the whole requested scope for the same naming and readability issues instead of fixing only the user's examples.
4. Load only the references required by the task.
5. Implement without upgrading the target stack unless explicitly requested.
6. Run the smallest relevant compile or test command and report the result.

## Implementation complexity and scope

- Do not add complexity for speculative, unsupported, low-probability edge cases or races merely because it seems theoretically safer. Before adding such defenses, explain the concrete failure scenario, likelihood and supporting evidence, impact, and estimated code/change cost; obtain user confirmation. State uncertainty instead of inventing probabilities. Do not seek confirmation again for measures the user already explicitly authorized.
- Validate external input once at its boundary, then rely on established types, contracts, and validated data internally. Do not repeat null checks, range checks, or fallbacks at every layer. Keep business invariants in their owning layer; see `references/java-core.md`.
- Inline simple one-off logic near its use. Extract a method or type only for actual reuse, isolation of complex logic, or a clearly named business concept. Do not create meaningless helpers merely to shorten methods or manufacture layers.
- Prefer straightforward normal control flow. Keep a task in one method when it remains clear; do not require a main method to delegate every validation or detail to helpers. Avoid unnecessary `try/catch`; use the exception-handling criteria in `references/java-core.md`.
- Change generated files through their source definitions, templates, or generation configuration, then regenerate them using the project's existing process.
- Use the repository's existing stack, tools, and workflow. Do not introduce dependencies, replace frameworks, or broaden refactoring without a task requirement or user authorization. Added complexity must serve the current task; do not remove existing necessary business constraints in the name of simplification.
- Apply shared naming and formatting preferences across the requested scope. Keep project-specific HTTP binding, authentication, scheduling, serialization, package roots, and business policies in the project's own instructions; do not turn wallet requirements into defaults for every Dayima service.

## Reference routing

- Read `references/java-core.md` for every Java source or review task, including method names, Javadoc, package layout, readable call chains, and MapStruct conversion.
- Read `references/spring-web.md` for controllers, HTTP APIs, endpoint naming, response wrappers, or exception mapping.
- Read `references/service-and-persistence.md` for services, transactions, DAOs, entities, SQL, XBatis, or MyBatis, including Mapper method names and XML formatting.
- Read `references/testing.md` when creating or changing behavior or tests.
- Read `references/examples.md` only when an implementation pattern is unclear.
- Copy and tailor `assets/AGENTS.md.template` only when asked to create or slim a project `AGENTS.md`.

## Hard rules

- Respect the Java and framework versions declared by the target project.
- Give methods clear operation and business meaning; use names such as `getFlow`, `updateOrderState`, and `normalizePageSize`. Synchronize authorized renames through callers, RPC contracts, Mapper XML, tests, and documentation.
- Add meaningful, expanded multi-line Javadoc to business methods, including private or protected helpers, interfaces, entities, and business fields. Put `/**`, the body, and `*/` on separate lines; use `@see` to link status fields to their constant group.
- Use ordinary comments only to explain non-obvious reasons, constraints, or counterintuitive behavior. Do not narrate obvious queries, delegation, or branches, or label them as `第一步` or `第二步`.
- Use ordered step comments such as `第一步` and `第二步` only for workflows with multiple consecutive business stages when the execution order materially improves readability. Do not restate obvious code.
- Use 140 characters as the default Java line width. Keep simple calls compact; split stream stages and complex conditions when it improves readability, even below this limit. Never compress Javadoc into a single line.
- When a method call has independent business meaning or its result may need inspection during debugging, assign it to a clearly named local variable before passing it to another method. Keep simple getters and trivial value conversions inline.
- Prefer MapStruct over `BeanUtils.copyProperties` for entity/DTO/VO conversion; reuse the project's mapper pattern and compatible annotation-processing setup. Use imports for Java types rather than fully qualified names in fields or method signatures.
- Use `@GetMapping` for read-only, side-effect-free Web queries.
- Use `@PostMapping` for creates, updates, deletes, approvals, state transitions, and other commands.
- Return `ResultRsp<T>` from every Java Web endpoint and use `ResultRsp.success(data)` for success.
- Use `com.dayima.core.support.ResultRsp` unless the target project exposes it through an approved shared module.
- If the target project lacks the shared `ResultRsp` dependency, stop the related Web change and tell the user to add it. Do not create a local `ResultRsp`, copy an implementation, return a raw object, or substitute another wrapper.
- Do not expose secrets, production endpoints, tokens, or real account data.
- Do not generalize rebate-specific channels, tables, statuses, or names into other domains.

## Conflicts

Follow the user's explicit request first. Use project `AGENTS.md` for verified project facts and local additions. If project instructions conflict with the required HTTP mapping or `ResultRsp<T>` rules, report the conflict and stop the affected change instead of silently selecting one convention.
