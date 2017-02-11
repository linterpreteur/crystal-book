# is_a?

유사 메서드 `is_a?`는 표현식의 실행 시간 타입이 주어진 타입을 상속하거나 포함하는지 검사합니다.

```crystal
a = 1
a.is_a?(Int32)          #=> true
a.is_a?(String)         #=> false
a.is_a?(Number)         #=> true
a.is_a?(Int32 | String) #=> true
```

이것을 유사 메서드라고 하는 이유는, [if var.is_a?(...)](if_varis_a.html)에서 설명된 것과 마찬가지로 컴파일러가 이 표현식에 대해 정보를 가지고 있으며 타입 정보에 영향을 끼칠 수 있기 때문입니다. 또한 인자는 컴파일 시간에 알려져 있는 [타입](type_grammar.html)이어야 합니다.
