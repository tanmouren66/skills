# Examples

Apply these fragments using the target project's types. The endpoint examples illustrate naming and call layout under the skill's general GET-query and JSON-POST defaults; explicit user protocol requirements take precedence.

## Query endpoint

```java
/**
 * 查询订单详情。
 */
@GetMapping("/orderDetail")
public ResultRsp<OrderVo> orderDetail(@RequestParam Long id) {
    OrderVo order = orderService.orderDetail(id);
    return ResultRsp.success(order);
}
```

The example does not authorize changing an existing route or HTTP method.

## Command endpoint

```java
/**
 * 审核通过订单。
 */
@PostMapping("/approveOrder")
public ResultRsp<OrderVo> approveOrder(@RequestBody OrderApproveRequest request) {
    OrderVo order = orderService.approveOrder(request);
    return ResultRsp.success(order);
}
```

Do not use GET for a state change and do not catch a shared business exception in the controller.

## Conditional state transition

```java
int affected = orderDao.updateOrderState(id, currentStatus, nextStatus);
if (affected != 1) {
    throw new BusinessException("订单状态已发生变化");
}
```

The DAO update must include both `id` and `currentStatus` in its predicate.

## Query and list conversion

```java
List<AccountFlow> flows = accountMapper.listFlows(uid, beforeId, pageSize);
return flows.stream()
        .map(AccountBeanMapper.INSTANCE::toVo)
        .collect(Collectors.toList());
```

Keep the query result available for debugging. Do not inline it into the stream or nest a state-changing service call inside a VO conversion.

## MapStruct conversion

```java
import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.factory.Mappers;

/**
 * 订单展示对象转换器。
 */
@Mapper
public interface OrderBeanMapper {
    OrderBeanMapper INSTANCE = Mappers.getMapper(OrderBeanMapper.class);

    /**
     * 转换订单信息，展示提示由业务查询另行填充。
     */
    @Mapping(target = "displayMessage", ignore = true)
    OrderVo toVo(Order source);
}
```

Use `@AfterMapping` for custom display processing when needed; keep normal field copying in generated code.

## Status documentation

```java
/**
 * 订单支付状态。
 *
 * @see OrderStatus.Payment
 */
private Integer paymentStatus;
```

Link to the status group that actually defines the codes. Do not replace the link with prose such as "编码维护于 OrderStatus" or compress this Javadoc onto one line.

## Money

```java
BigDecimal amount = commission.multiply(rate).setScale(2, RoundingMode.DOWN);
```

Do not calculate monetary values with `double` or leave division rounding implicit.

## Strategy registry

```java
public PaymentHandler getHandler(Channel channel) {
    return handlers.stream()
            .filter(handler -> handler.supports(channel))
            .findFirst()
            .orElseThrow(() -> new BusinessException("不支持的支付渠道"));
}
```

Add a handler for a new channel instead of extending a central chain of channel-specific `if` statements.
