# type

`lib` 안의 `type` 선언은 C `typedef`와 비슷하지만 더 강력한 방식으로 타입을 선언합니다.

```crystal
lib X
  type MyInt = Int32
end
```

C와 달리, `Int32`와 `MyInt`는 서로 변환되지 않습니다.

```crystal
lib X
  type MyInt = Int32

  fun some_fun(value : MyInt)
end

X.some_fun 1 # 오류: 'X#some_fun'의 인자 'value'는
             # Int32가 아니라 X::MyInt여야 함
```

따라서, `type` 선언은 C 라이브러리에서 생성된 불투명 타입을 나타낼 때 유용합니다. `fopen`에서 얻을 수 있는 C의 `FILE` 타입이 그 예입니다.

[타입 문법](../type_grammar.html)에서 typedef 타입에 쓰인 표기에 대해 알아볼 수 있습니다.
