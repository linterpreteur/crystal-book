# typeof

`typeof`는 표현식의 타입을 반환합니다.

```crystal
a = 1
b = typeof(a) #=> Int32
```

인자를 여러 개 넘길 경우 결과는 모든 타입의 공용체입니다.

```crystal
typeof(1, "a", 'a') #=> (Int32 | String | Char)
```

컴파일러의 타입 추론 능력을 이용하기 위한 목적으로 제너릭 코드에서 사용됩니다.

```crystal
hash = {} of Int32 => String
another_hash = typeof(hash).new #:: Hash(Int32, String)
```

`typeof`가 실제로 표현식을 평가하는 것은 아니므로 컴파일 시간의 메서드에도
사용할 수 있습니다. 다음 예에서는 중첩된 타입 인자로부터 재귀적으로
공용체 타입을 생성해 냅니다.

```crystal
class Array
  def self.elem_type(typ)
    if typ.is_a?(Array)
      elem_type(typ.first)
    else
      typ
    end
  end
end

nest = [1, ["b", [:c, ['d']]]]
flat = Array(typeof(Array.elem_type(nest))).new
typeof(nest) #=> Array(Int32 | Array(String | Array(Symbol | Array(Char))))
typeof(flat) #=> Array(String | Int32 | Symbol | Char)
```

[타입](type_grammar.html) 문법에서도 이 표현식을 사용할 수 있습니다.
