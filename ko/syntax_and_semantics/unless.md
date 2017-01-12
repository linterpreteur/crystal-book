# unless

`unless`는 조건이 *거짓인* 값이라면 `then` 분기를 평가하며, *참인* 값이며 `else` 분기가 존재한다면 `else` 분기를 평가합니다. 즉 `if`와 정반대로 동작합니다.

```crystal
unless some_condition
  then_expression
else
  else_expression
end

# 다음과 동일
if some_condition
  else_expression
else
  then_expression
end

# 접미사로도 사용 가능
close_door unless door_closed?
```
