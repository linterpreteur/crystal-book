# responds_to?

유사 메서드 `responds_to?`는 타입에 주어진 이름의 메서드가 있는지 검사합니다.

```crystal
a = 1
a.responds_to?(:abs)    #=> true
a.responds_to?(:size) #=> false
```

이것을 유사 메서드라고 하는 이유는, [if var.responds_to?(...)](if_varresponds_to.html)에서 설명된 것과 마찬가지로 컴파일러가 이 표현식에 대해 정보를 가지고 있으며 타입 정보에 영향을 끼칠 수 있기 때문입니다.
