# 초기화하지 않은 변수 선언

크리스탈에서는 초기화하지 않은 변수를 선언할 수 있습니다.

```crystal
x = uninitialized Int32
x #=> 신뢰할 수 없는 쓰레기 값
```

이는 [안전하지 않은](unsafe.html) 코드이며, 저수준 코드에서 성능 저하를 일으키지 않고서 초기화하지 않은 [StaticArray](http://crystal-lang.org/api/StaticArray.html)를 선언하는 데 사용되곤 합니다.

```crystal
buffer = uninitialized UInt8[256]
```

buffer는 힙이 아니라 스택에 할당됩니다.

`uninitialized` 예약어 뒤에 오는 타입은 [타입 문법](type_grammar.html)을 따릅니다.

