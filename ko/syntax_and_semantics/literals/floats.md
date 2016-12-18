# 실수

[Float32](http://crystal-lang.org/api/Float32.html)와 [Float64](http://crystal-lang.org/api/Float64.html)의 실수형 두 개가 있습니다. 이는 각각 IEEE에서 정의한 [binary32](http://en.wikipedia.org/wiki/Single_precision_floating-point_format)와 [binary64](http://en.wikipedia.org/wiki/Double_precision_floating-point_format) 타입에 상응합니다.

부동소수점 리터럴은 `+` 또는 `-` 부호로 시작할 수 있습니다. 숫자나
밑줄이 이어지고, 점이 나온 후, 숫자나 밑줄이 이어지고, 지수 접미사와
타입 접미사가 올 수 있습니다. 접미사가 없을 경우 리터럴의 타입은 `Float64`입니다.

```crystal
1.0      # Float64
1.0_f32  # Float32
1_f32    # Float32

1e10     # Float64
1.5e10   # Float64
1.5e-7   # Float64

+1.3     # Float64
-0.5     # Float64
```

접미사 전의 밑줄(`_`)은 필수가 아닙니다.

밑줄을 써서 숫자를 읽기 쉽게 만들 수도 있습니다.

```crystal
1_000_000.111_111 # 1000000.111111보다 훨씬 읽기 쉽지만 기능적으로 동일
```
