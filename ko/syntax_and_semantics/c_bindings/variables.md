# 변수

C 라이브러리에서 외부로 노출된 변수들은 전역 변수 선언과 비슷한 방법으로 `lib` 안에 선언할 수 있습니다.

```crystal
lib C
  $errno : Int32
end
```

이를 읽고 쓸 수 있습니다.

```crystal
C.errno #=> some value
C.errno = 0
C.errno #=> 0
```

속성을 통해 변수를 스레드 로컬로 표시할 수 있습니다.

```crystal
lib C
  @ [ThreadLocal]
  $errno : Int32
end
```

[타입 문법](../type_grammar.html)에서 외부 변수 타입에 쓰인 표기에 대해 알아볼 수 있습니다.
