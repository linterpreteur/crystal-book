# Range

[Range](http://crystal-lang.org/api/Range.html)는 보통 범위 리터럴으로 생성합니다.

```crystal
x..y  # 포함 범위, 수학적으로는 [x, y]
x...y # 배제 범위, 수학적으로는 [x, y)
```

이를 기억하는 쉬운 방법은 마지막 점 하나를 *y*를 더 밀어내므로 범위 밖으로 보낸다고 생각하는 것입니다.
