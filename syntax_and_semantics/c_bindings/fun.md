# fun

`lib` 안의 `fun` 선언은 C 함수로 바인딩됩니다.

```crystal
lib C
  # C에서는 double cos(double x)
  fun cos(value : Float64) : Float64
end
```

한 번 바인딩하면, `C` 타입의 클래스 메서드인 것처럼 이용할 수 있습니다.

```crystal
C.cos(1.5) #=> 0.0707372
```

함수에 인자가 없다면 선언할 때와 호출할 때 모두 괄호를 생략할 수 있습니다.

```crystal
lib C
  fun getch : Int32
end

C.getch
```

반환형이 void라면 이 또한 생략할 수 있습니다.

```crystal
lib C
  fun srand(seed : UInt32)
end

C.srand(1_u32)
```

가변 인자 함수도 바인딩할 수 있습니다.

```crystal
lib X
  fun variadic(value : Int32, ...) : Int32
end

X.variadic(1, 2, 3, 4)
```

C 함수를 호출할 때 (나중에 설명할 `to_unsafe`를 제외하면) 묵시적 변환은 없다는 사실을 알 수 있습니다. 반드시 예상되는 타입을 넘겨야 하는 것입니다. 정수나 실수형의 경우에는 다양한 `to_...` 메서드를 사용합니다.

크리스탈의 메서드 이름은 소문자로 시작해야 하므로 `fun` 이름 또한 소문자로 시작해야 합니다. 대문자로 시작하는 C 함수를 바인딩하려면 크리스탈에서 쓰일 다른 이름을 줄 수 있습니다.

```crystal
lib LibSDL
  fun init = SDL_Init(flags : UInt32) : Int32
end
```

이름이 유효한 식별자가 아니거나 타입 이름인 경우 문자열을 이름으로 쓸 수 있습니다.

```crystal
lib LLVMIntrinsics
  fun ceil_f32 = "llvm.ceil.f32"(value : Float32) : Float32
end
```

C 함수는 대개 길고 라이브러리 이름이 접두사로 붙어 있기 때문에 이런 방법을 통해 더 짧고 더 나은 이름을 달아줄 수 있습니다.

C 바인딩에 쓸 수 있는 유효한 타입은
* 원시 타입 (`Int8`, ..., `Int64`, `UInt8`, ..., `UInt64`, `Float32`, `Float64`)
* 포인터 타입 (`Pointer(Int32)`, `Int32*`로도 쓸 수 있음)
* 정적 배열 (`StaticArray(Int32, 8)`, `Int32[8]`로도 쓸 수 있음)
* 함수 타입 (`Function(Int32, Int32)`, `Int32 -> Int32`로도 쓸 수 있음)
* 이전에 선언된 `struct`, `union`, `enum`, `type`, `alias`
* 반환형이 없음을 나타내는 `Void`
* `Void`와 비슷하지만 코드가 더 이상 진행되지 않음을 나타내는 `NoReturn`
* `@ [Extern]` 속성이 표시된 크리스탈 구조체

[타입 문법](../type_grammar.html)에서 fun 선언에 쓰인 표기에 대해 알아볼 수 있습니다.

표준 라이브러리에 정의된 [LibC](https://github.com/crystal-lang/crystal/blob/master/src/lib_c.cr) lib은 `int`, `short`, `size_t` 등 흔하게 사용되는 C 타입의 별칭을 선언합니다. 이를 바인딩에서 다음과 같이 사용할 수 있습니다.

```crystal
lib MyLib
  fun my_fun(some_size : LibC::SizeT)
end
```

**주의:** C의 `char` 타입은 크리스탈의 `UInt8`이므로, `char*`와 `const char*`은 `UInt8*`입니다. 크리스탈의 `Char` 타입은 유니코드의 코드 포인트이므로 4바이트로 나타냅니다. 이는 `UInt8`이 아니라 `Int32`와 더 유사합니다. 확실하지 않다면 `LibC::Char` 별칭을 쓸 수 있습니다.
