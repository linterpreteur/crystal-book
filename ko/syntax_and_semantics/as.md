# as

유사 메서드 `as`는 표현식의 타입을 제한합니다.

```crystal
if some_condition
  a = 1
else
  a = "hello"
end

# a : Int32 | String
```

위 코드에서, `a`는 `Int32 | String`의 공용체입니다. `if` 이후에 `a`가 `Int32`라는 증거가 있다면, 컴파일러게 그렇게 취급하도록 강제할 수 있습니다.

```crystal
a_as_int = a.as(Int32)
a_as_int.abs          # 작동, 컴파일러는 a_as_int가 Int32임을 앎
```

유사 메서드 `as`는 실행 시간 검사를 실시합니다. `a`가 `Int32`가 아니었다면, [예외](exception_handling.html)가 일어납니다.

표현식의 인자는 [타입](type_grammar.html)이어야 합니다.

타입이 다른 타입으로 제한될 수 없는 경우에는 컴파일 시간 오류가 발생합니다.

```crystal
1.as(String) # 컴파일 시간 오류
```

**주의:** `as`로 한 타입에서 관계 없는 타입으로 변환할 수는 없습니다. `as`는 다른 언어의 `cast`와는 다릅니다. 이런 경우에 쓸 수 있는 정수, 실수, 문자의 메서드가 있습니다. 또는 이후 설명할 포인터 변환을 사용합니다.

## 포인터 타입의 변환

`as`로 포인터 타입끼리 서로 변환할 수도 있습니다.

```crystal
ptr = Pointer(Int32).malloc(1)
ptr.as(Int8*)                    #:: Pointer(Int8)
```

이 경우, 실행 시간 검사는 일어나지 않습니다. 포인터는 안전하지 않으며 이런 종류의 변환은 보통 C 바인딩이나 저수준 코드에서나 필요합니다.

## 포인터 타입과 다른 타입 사이의 변환

포인터 타입과 Reference 타입의 변환 또한 가능합니다.

```crystal
array = [1, 2, 3]

# object_id는 객체의 메모리 주소를 반환하므로
# 그 주소로 포인터를 생성
ptr = Pointer(Void).new(array.object_id)

# 그 포인터를 같은 타입으로 변환하여
# 같은 값을 획득할 수 있음
array2 = ptr.as(Array(Int32))
array2.same?(array) #=> true
```

포인터가 있기 때문에 이 경우에도 실행 시간 검사는 일어나지 않습니다. 이런 종류의 변환은 앞서 본 것보다 필요한 경우가 더욱 드물지만, 크리스탈에서 String과 같은 핵심 타입을 구현하는 데 쓰이며, 이를 사용하여 Reference 타입을 void 포인터로 변환하여 C 함수에 넘길 수도 있습니다.

## 상위 공용체 타입으로의 변환

`as`를 이용하여 "더 큰" 타입으로 표현식을 변환할 수도 있습니다.

```crystal
a = 1
b = a.as(Int32 | Float64)
b #:: Int32 | Float64
```

별로 유용해 보이지 않을 수도 있지만, 예를 들어 배열의 원소에 사용하는 경우라면 다를 수 있습니다.

```crystal
ary = [1, 2, 3]

# Int32 | Float64의 배열 1, 2, 3을 생성
ary2 = ary.map { |x| x.as(Int32 | Float64) }

ary2 #:: Array(Int32 | Float64)
ary2 << 1.5 # 정상 작동
```

`Array#map` 메서드는 블락의 타입을 배열의 제너릭 타입으로 사용합니다. 유사 메서드 `as`가 없었다면 `Int32` 타입이 추런되어 `Float64`를 배열에 추가할 수 없었을 것입니다.

## 컴파일러가 블락의 타입을 추론할 수 없을 때의 사용

컴파일러가 블락의 타입을 추론할 수 없는 경우도 간혹 있습니다. 서로에게 의존하는 재귀 호출이 한 예입니다. 그런 경우에는 `as`로 컴파일러에게 타입을 알려줄 수 있습니다.

```crystal
some_call { |v| v.method.as(ExpectedType) }
```
