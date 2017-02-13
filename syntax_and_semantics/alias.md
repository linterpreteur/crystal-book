# alias

`alias`로 타입에 별칭을 지어줄 수 있습니다.

```crystal
alias PInt32 = Pointer(Int32)

ptr = PInt32.malloc(1) # : Pointer(Int32)
```

별칭이 사용되면, 컴파일러는 가리키는 타입으로 별칭을 치환합니다.

별칭은 긴 타입 이름을 쓰고 싶지 않을 때, 그리고 재귀적 타입의 경우에도 유용합니다.

```crystal
alias RecArray = Array(Int32) | Array(RecArray)

ary = [] of RecArray
ary.push [1, 2, 3]
ary.push ary
ary #=> [[1, 2, 3], [...]]
```

재귀적 타입의 현실적인 한 예는 JSON입니다.

```crystal
module Json
  alias Type = Nil |
               Bool |
               Int64 |
               Float64 |
               String |
               Array(Type) |
               Hash(String, Type)
end
```
