# 속성

타입과 메서드에 속성을 표기할 수 있는 경우가 있습니다. 현재는 미리 정의된 속성만을 사용할 수 있지만, 추후에는 사용자 정의 속성 또한 사용할 수 있을 것입니다.

## Link

C 라이브러리에 어떻게 링크할지에 대한 정보를 컴파일러에 전달합니다. [lib](c_bindings/lib.html) 항목에 설명이 있습니다.

## Extern

크리스탈 구조체를 lib 선언에서 사용할 수 있도록 만듭니다.

```crystal
@ [Extern]
struct MyStruct
end

lib MyLib
  fun my_func(s : MyStruct) # 가능 (Extern 속성이 없을 경우 오류 발생)
end
```

구조체를 C 공용체처럼 동작하도록 만들 수도 있습니다. 이는 다소 안전하지 않을 수 있습니다.

```crystal
# Int32 코드 포인트와 Char를 쉽게 변환하기 위한 구조체
@ [Extern(union: true)]
struct Int32OrChar
  property int = 0
  property char = '\0'
end

s = Int32OrChar.new
s.char = 'A'
s.int # => 65

s.int = 66
s.char # => 'B'
```

## ThreadLocal

클래스 변수와 C 외부 변수에 적용된 `@ [ThreadLocal]` 속성은 그 변수가 스레드 로컬이라는 것을 의미합니다.

```crystal
class DontUseThis
  # 스레드 당 하나
  @ [ThreadLocal]
  @@values = [] of Int32
end
```

ThreadLocal은 표준 라이브러리에서 런타임을 구현하기 위한 용도로 사용되며, 그 외에는 가급적이면 사용하지 않는 것이 좋습니다.

## Packed

[C 구조체](c_bindings/struct.html)에 사용하여 속성 사이에 여백 바이트가 삽입되는 것을 방지합니다. C 라이브러리에서 명시적으로 압축 맞춤을 지정하지 않은 경우라면 보통 필요하지 않습니다.

## AlwaysInline

컴파일러에 메서드를 항상 인라인하도록 요청합니다.

```crystal
@ [AlwaysInline]
def foo
  1
end
```

## NoInline

컴파일러에 메서드를 절대 인라인하지 않도록 요청합니다. 메서드에서 yield를 사용하는 경우라면 항상 코드를 인라인하기 때문에, 이 때는 아무런 효과가 없습니다.

```crystal
@ [NoInline]
def foo
  1
end
```

## ReturnsTwice

메서드 혹은 [lib fun](c_bindings/fun.html)이 두 번 반환함을 나타냅니다. C의 `setjmp` 함수가 그 예입니다.

## Raises

메서드 혹은 [lib fun](c_bindings/fun.html)이 예외를 일으킬 수도 있음을 나타냅니다. [callbacks](c_bindings/callbacks.html) 항목에 설명이 있습니다.

## CallConvention

[lib fun](c_bindings/fun.html)의 호출 규약을 지정합니다.

```crystal
lib LibFoo
  @ [CallConvention("X86_StdCall")]
  fun foo : Int32
end
```

다음과 같은 호출 규약을 지정할 수 있습니다.

* C (기본값)
* Fast
* Cold
* WebKit_JS
* AnyReg
* X86_StdCall
* X86_FastCall

[LLVM 문서](http://llvm.org/docs/LangRef.html#calling-conventions)에서 이에 대한 설명을 읽을 수 있습니다.

## Flags

[enum](enum.html)을 "플래그 열거형"임을 나타냅니다. 이는 `to_s`와 같은 일부 메서드의 동작을 변경합니다.
