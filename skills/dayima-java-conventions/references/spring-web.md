# Spring Web

## Controller contract

- Annotate controllers with `@RestController`.
- Put the shared path on the class with `@RequestMapping`; start class and method paths with `/`.
- Use `@GetMapping` only for read-only, side-effect-free queries.
- Use `@PostMapping` for creates, updates, deletes, approvals, state transitions, and other commands.
- Do not use bare method-level `@RequestMapping` or redundant method-level `@ResponseBody`.
- Give new endpoints and methods a clear business meaning, such as `signingStatus`, `applyWithdraw`, or `withdrawOrderDetail`. For a direct Controller-to-RPC operation, keep the business name aligned across the route, Controller, RPC interface, and implementation. Rename existing public contracts only within the user's authorized scope and update their callers.
- Describe what the endpoint does in its expanded Javadoc; do not repeat the route as its description. Save the business result to a local variable before passing it to `ResultRsp.success`.

## Responses

- Return `ResultRsp<T>` from every endpoint and call `ResultRsp.success(data)` on success.
- Return `ResultRsp<DomainOrVO>` for one object.
- Return `ResultRsp<List<DomainOrVO>>` for a list.
- Return `ResultRsp<ProjectPager<DomainOrVO>>` for a page, using the project's actual pager type.
- Return `ResultRsp<Boolean>` for a simple delete, revoke, or toggle result.
- Do not return raw objects, `Map<String, Object>`, `MsgBean`, or an ad hoc response envelope.

Use `com.dayima.core.support.ResultRsp`. Before adding or changing an endpoint, verify that the target project already has the approved dependency. If it does not, stop and report: `项目缺少统一的 com.dayima.core.support.ResultRsp 依赖，请先引入统一依赖后再继续接口改造。` Do not add a local replacement.

## Inputs and errors

- Use query parameters for GET filters and pagination unless the project has a verified compatible binding convention.
- Use `@RequestBody` for complex POST request objects. Follow the target project's verified convention for simple scalar POST parameters.
- Use dedicated request objects for multi-field commands; do not overload persistence entities as mutable command payloads when that exposes unrelated fields.
- Let the global exception handler translate business exceptions. Do not repeat endpoint-level `try/catch` blocks.
- Keep authentication context extraction and input binding in the controller; keep business decisions in services.
