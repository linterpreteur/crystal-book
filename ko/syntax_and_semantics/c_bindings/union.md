# union

`lib` 안의 `union` 선언은 C 공용체를 선언합니다.

```crystal
lib U
  # C에서는
  #
  #  union IntOrFloat {
  #    int some_int;
  #    double some_float;
  #  };
  union IntOrFloat
    some_int : Int32
    some_float : Float64
  end
end
```

공용체의 인스턴스는 `new`로 생성할 수 있습니다.

```crystal
value = U::IntOrFloat.new
```

스택에 공용체가 할당될 것입니다.

C 공용체는 모든 필드를 0으로 초기화합니다. 정수와 실수는 0에서 시작하고, 포인터는 0의 주소 값을 가집니다.

`uninitialized`를 이용하여 이를 방지할 수 있습니다.

```crystal
value = uninitialized U::IntOrFloat
value.some_int #=> 임의의 쓰레기 값
```

속성을 쓰고 읽을 수 있습니다.

```crystal
value = U::IntOrFloat.new
value.some_int = 1
value.some_int #=> 1
value.some_float #=> 4.94066e-324
```

할당하는 값이 속성의 값과 다를 경우 [to_unsafe](to_unsafe.html)가 시도됩니다.

C 공용체는 함수와 메서드에 값으로 (복사되어) 전달되며, 메서드에서 반환될 때에도 값으로 전달됩니다.

```crystal
def change_it(value)
  value.some_int = 1
end

value = U::IntOrFloat.new
change_it value
value.some_int #=> 0
```

[타입 문법](../type_grammar.html)에서 공용체 속성 타입에 쓰인 표기에 대해 알아볼 수 있습니다.
