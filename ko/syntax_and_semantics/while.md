# while

`while`은 그 조건이 *참인* 값인 동안 몸체를 실행합니다.

```crystal
while some_condition
  do_this
end
```

조건을 일단 확인하여 *참인* 값일 경우 몸체가 실행됩니다. 따라서 조건이 거짓인 경우라면 몸체가 실행되지 않을 것입니다.

`while`의 타입은 항상 `Nil`입니다.

`if`와 비슷하게, `while`의 조건이 변수라면 몸체에서 해당 변수는 `nil`이 아니도록 한정됩니다. 조건이 `var.is_a?(Type)` 테스트일 경우, 몸체에서 `var`은 해당 타입이게 됩니다. 조건이 `var.responds_to?(:method)`일 경우, `var`은 해당 메서드를 갖는 타입이게 됩니다.

`while` 이후 어떤 변수의 값은 `while` 전에 가졌던 값과 `while`의 몸체를 벗어날 때 가졌던 값에 의해 결정됩니다.

```crystal
a = 1
while some_condition
  # a : Int32 | String
  a = "hello"
  # a : String
  a.size
end
# a : Int32 | String
```

## 반복문 마지막에 조건 확인

몸체를 최소한 한 번 실행하고 중지 조건을 확인할 필요가 있다면, 이렇게 할 수 있습니다.

```crystal
while true
  do_something
  break if some_condition
end
```

또는 표준 라이브러리의 `loop`을 이용합니다.

```crystal
loop do
  do_something
  break if some_condition
end
```
