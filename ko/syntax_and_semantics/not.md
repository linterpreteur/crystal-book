# if !

`!` 연산자는 값이 [참인지 거짓인지의 여부](truthy_and_falsey_values.html)를 반전시킨 결과의 `Bool` 값을 반환합니다.

`if`의 조건으로 `is_a?`, `responds_to`, `nil?`과 변수를 사용하는 경우, 컴파일러는 그에 따라 타입을 한정합니다.

```crystal
a = some_condition ? nil : 3
if !a
  # 이 분기에서 a는 거짓인 값이므로 Nil
else
  # 이 분기에서 a는 참인 값이므로 Int32
end
```

```crystal
b = some_condition ? 1 : "x"
if !b.is_a?(Int32)
  # 이때 b는 Int32가 아니므로 String
end
```
