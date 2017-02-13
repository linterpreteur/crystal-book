# 정수

부호 있는 정수 타입은 네 개가 있습니다. [Int8](http://crystal-lang.org/api/Int8.html), [Int16](http://crystal-lang.org/api/Int16.html), [Int32](http://crystal-lang.org/api/Int32.html), [Int64](http://crystal-lang.org/api/Int64.html)으로 각각 8, 16, 32, 64비트의 숫자를 나타냅니다.

부호 없는 정수 타입은 네 개가 있습니다. [UInt8](http://crystal-lang.org/api/UInt8.html), [UInt16](http://crystal-lang.org/api/UInt16.html), [UInt32](http://crystal-lang.org/api/UInt32.html), [UInt64](http://crystal-lang.org/api/UInt64.html)입니다.

정수 리터럴은 맨 앞에 `+` 또는 `-` 부호가 올 수 있습니다. 숫자나 밑줄이 이어지고,
마지막에 접미사가 올 수 있습니다. 접미사가 없다면 리터럴의 타입은 `Int32`, 
`Int64`, `UInt64` 중 숫자가 들어맞는 가장 낮은 타입입니다.

```crystal
1      # Int32

1_i8   # Int8
1_i16  # Int16
1_i32  # Int32
1_i64  # Int64

1_u8   # UInt8
1_u16  # UInt16
1_u32  # UInt32
1_u64  # UInt64

+10    # Int32
-20    # Int32

2147483648          # Int64
9223372036854775808 # UInt64
```

접미사 전의 밑줄(`_`)은 반드시 필요한 것은 아닙니다.

밑줄을 써서 숫자를 읽기 쉽게 만들 수도 있습니다.

```crystal
1_000_000 # 1000000보다 나음
```

이진수는 `0b`로 시작합니다.

```crystal
0b1101 # == 13
```

8진수는 `0o`로 시작합니다.

```crystal
0o123 # == 83
```

16진수는 `0x`로 시작합니다.

```crystal
0xFE012D # == 16646445
0xfe012d # == 16646445
```
