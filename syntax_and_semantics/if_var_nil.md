# if var.nil?

`if`의 조건이 `var.nil?`이라면 컴파일러는 `then` 분기에서 `var`이 `Nil`이며 `else` 분기에서는 `Nil`이 아님을 추론합니다.

```crystal
a = some_condition ? nil : 3
if a.nil?
  # 이때 a는 Nil
else
  # 이때 a는 Int32
end
```
