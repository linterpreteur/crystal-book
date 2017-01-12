# alias

`lib` 안의 `enum` 선언은 C `typedef`를 선언합니다.

```crystal
lib X
  alias MyInt = Int32
end
```

이제 `Int32`와 `MyInt`는 서로 변환할 수 있습니다.

```crystal
lib X
  alias MyInt = Int32

  fun some_fun(value : MyInt)
end

X.some_fun 1 # OK
```

`alias`는 이름이 긴 타입을 계속 쓰지 않고 싶을 때 가장 유용하지만, 컴파일 시간 플래그에 따라 타입을 선언하고 싶을 때에도 유용합니다.

```crystal
lib C
  {% if flag?:(x86_64) %}
    alias SizeT = Int64
  {% else %}
    alias SizeT = Int32
  {% end %}

  fun memcmp(p1 : Void*, p2 : Void*, size : C::SizeT) : Int32
end
```

[타입 문법](../type_grammar.html)에서 alias 타입에 쓰인 표기에 대해 알아볼 수 있습니다.
