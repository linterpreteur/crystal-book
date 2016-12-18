# Array

[Array](http://crystal-lang.org/api/Array.html)는 타입 `T` 원소들을 포함하는 제너릭 타입입니다. 보통 배열 리터럴을 통해 생성합니다.

```crystal
[1, 2, 3]         # Array(Int32)
[1, "hello", 'x'] # Array(Int32 | String | Char)
```

배열은 혼합 타입을 가질 수 있습니다. 이는 `T`가 타입의 결합이라는 뜻입니다. 하지만 이는 T를 특정하거나 배열 리터럴을 사용함으로써, 배열이 생성되는 시점에 결정됩니다. 후자의 경우, T는 배열 리터럴의 원소들의 결합입니다.

빈 배열을 만들 때는 항상 T를 특정해야 합니다.

```crystal
[] of Int32 # Array(Int32).new와 동일
[]          # 문법 오류
```

## 문자열 배열

문자열의 배열은 특수한 문법으로도 만들 수 있습니다.

```crystal
%w(one two three) # ["one", "two", "three"]
```

## 기호 배열

기호의 배열은 특수한 문법으로도 만들 수 있습니다.

```crystal
%i(one two three) # [:one, :two, :three]
```

## 배열류 타입

다른 타입에서도 배열 리터럴 문법을 사용할 수 있습니다. 단, 그 타입이 인자 없는 `new` 메서드와 `<<` 메서드를 정의하는 경우에 한합니다.

```crystal
MyType{1, 2, 3}
```

`MyType`이 제너릭이 아니라면, 위 코드는 다음과 동일합니다.

```crystal
tmp = MyType.new
tmp << 1
tmp << 2
tmp << 3
tmp
```

`MyType`이 제너릭이라면, 위 코드는 다음과 동일합니다.

```crystal
tmp = MyType(typeof(1, 2, 3)).new
tmp << 1
tmp << 2
tmp << 3
tmp
```

제너릭 타입인 경우 타입 인자를 특정할 수도 있습니다.

```crystal
MyType(Int32 | String) {1, 2, "foo"}
```
