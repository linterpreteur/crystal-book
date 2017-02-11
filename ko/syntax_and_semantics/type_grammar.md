# 타입 문법

* [타입 제약](type_restrictions.html)을 특정할 때
* [타입 인자](generics.html)을 특정할 때
* [변수를 선언할 때](declare_var.html)
* [별칭](alias.html)을 선언할 때
* [typedef](c_bindings/type.html)를 선언할 때
* [is_a?](is_a.html) 유사 호출의 인자일 때
* [as](as.html) 표현식의 인자일 때
* [sizeof](sizeof.html) 표현식의 인자일 때
* [instance_sizeof](instance_sizeof.html) 표현식의 인자일 때
* 메서드의 [반환형](return_types.html)일 때

일반적인 타입에 대해 편의를 위한 문법을 제공합니다. [C 바인딩](c_bindings/index.html)을 작성할 때 특히 유용하고, 앞서 본 경우 중 하나라면 언제든 사용할 수 있습니다.

## 제너릭

일반적인 타입과 제너릭을 사용할 수 있습니다.

```crystal
Int32
My::Nested::Type
Array(String)
```

## 공용체

```crystal
alias Int32OrString = Int32 | String
```

막대기(`|`)를 사용하여 공용체 타입을 생성합니다. `Int32 | String`는 "Int32 또는 String"으로 읽습니다. 일반 코드에서 `Int32 | String`은 `Int32`의 `|` 메서드에 `String` 인자를 넘겨 호출하는 것으로 해석됩니다.

## Nil이 될 수 있는 경우

```crystal
alias Int32OrNil = Int32?
```

이는 다음과 같습니다.

```crystal
alias Int32OrNil = Int32 | ::Nil
```

일반 코드에서 `Int32?`는 문법 오류입니다.

## 포인터

```crystal
alias Int32Ptr = Int32*
```

이는 다음과 같습니다.

```crystal
alias Int32Ptr = Pointer(Int32)
```

일반 코드에서 `Int32*`는 `Int32`의 `*` 메서드를 호출하는 것으로 해석됩니다.

## StaticArray

```crystal
alias Int32_8 = Int32[8]
```

이는 다음과 같습니다.

```crystal
alias Int32_8 = StaticArray(Int32, 8)
```

일반 코드에서 `Int32[8]`는 `Int32`의 `[]` 메서드에 `8`을 인자로 넘겨 호출하는 것으로 해석됩니다.

## Tuple

```crystal
alias Int32StringTuple = {Int32, String}
```

이는 다음과 같습니다.

```crystal
alias Int32StringTuple = Tuple(Int32, String)
```

일반 코드에서 `{Int32, String}`는 `Int32`와 `String` 타입 자체를 원소로 갖는 튜플 인스턴스입니다. 이는 위의 튜플 **타입**과는 다른 것입니다.

## NamedTuple

```crystal
alias Int32StringNamedTuple = {x: Int32, y: String}
```

이는 다음과 같습니다.

```crystal
alias Int32StringNamedTuple = NamedTuple(x: Int32, y: String)
```

일반 코드에서 `{x: Int32, y: String}`는 `x`에 `Int32`를, `y`에 `String`을 갖는 이름 달린 튜플 인스턴스입니다. 이는 위의 튜플 **타입**과는 다른 것입니다.

## Proc

```crystal
alias Int32ToString = Int32 -> String
```

이는 다음과 같습니다.

```crystal
alias Int32ToString = Proc(Int32, String)
```

인자 없는 프록은 다음과 같이 씁니다.

```crystal
alias ProcThatReturnsInt32 = -> Int32
```

인자가 여러 개인 프록은 다음과 같습니다.

```crystal
alias Int32AndCharToString = Int32, Char -> String
```

중첩된 프록(또는 어떤 다른 타입)은 다음과 같이 괄호를 써서 나타낼 수 있습니다.

```crystal
alias ComplexProc = (Int32 -> Int32) -> String
```

일반 코드에서 `Int32 -> String`는 문법 오류입니다.

## self

타입 문법에서 `self`를 이용하여 `self` 타입을 표기할 수 있습니다. 자세한 것은 [타입 

제약](type_restrictions.html) 항목을 참고하세요.

## class

`class`는 인스턴스의 타입이 아닌 클래스 타입을 나타냅니다.

```crystal
def foo(x : Int32)
  "인스턴스"
end

def foo(x : Int32.class)
  "클래스"
end

foo 1     # "인스턴스"
foo Int32 # "클래스"
```

`class`는 클래스 타입의 배열 및 집합을 만들 때 유용합니다.

```crystal
class Parent
end

class Child1 < Parent
end

class Child2 < Parent
end

ary = [] of Parent.class
ary << Child1
ary << Child2
```

## 밑줄

타입 제약에서 밑줄을 쓸 수 있습니다. 밑줄은 어느 타입에든 해당됩니다.

```crystal
# 제약을 특정하지 않는 것과 동일하여 별로 의미가 없음
def foo(x : _)
end

# 두 인자를 받아 Int32를 반환하는 프록을 정의하여 조금 더 유용
def foo(x : _, _ -> Int32)
end
```

## typeof

타입 문법에서 `typeof`를 사용할 수 있습니다. `typeof`는 전달된 표현식의 공용체 타입을 반환합니다.

```crystal
alias SameAsInt32 = typeof(1 + 2)
alias Int32OrString = typeof(1, "a")
```
