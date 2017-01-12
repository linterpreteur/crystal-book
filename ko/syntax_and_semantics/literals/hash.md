# Hash

[Hash](http://crystal-lang.org/api/Hash.html)는 `K` 타입의 키에서 `V` 타입의 값으로의 연결을 나타냅니다. 보통 해시 리터럴을 통해 만들 수 있습니다.

```crystal
{1 => 2, 3 => 4}     # Hash(Int32, Int32)
{1 => 2, 'a' => 3}   # Hash(Int32 | Char, Int32)
```

해시는 키와 값 모두 혼합 타입을 가질 수 있습니다. 이는 `K` 또는 `V`가 공용체 타입임을 의미합니다. 이 타입은 `K`와 `V`를 특정하거나 해시 리터럴을 이용함으로써, 해시가 생성되는 시점에 결정됩니다. 후자의 경우 `K`는 해시 리터럴 키의 공용체로 결정되며 `V`는 값들의 공용체로 결정됩니다.

빈 해시를 만들 때는 항상 `K`와 `V`를 특정해야 합니다.

```crystal
{} of Int32 => Int32 # Hash(Int32, Int32).new와 동일
{}                   # 문법 오류
```

## 해시류 타입

다른 타입에서도 배열 리터럴 문법을 사용할 수 있습니다. 단, 그 타입이 인자 없는 `new` 메서드와 `[]=` 메서드를 정의하는 경우에 한합니다.

```crystal
MyType{"foo" => "bar"}
```

`MyType`이 제너릭이 아니라면, 위 코드는 다음과 동일합니다.

```crystal
tmp = MyType.new
tmp["foo"] = "bar"
tmp
```

`MyType`이 제너릭이라면, 위 코드는 다음과 동일합니다.

```crystal
tmp = MyType(typeof("foo"), typeof("bar")).new
tmp["foo"] = "bar"
tmp
```

제너릭 타입인 경우 타입 인자를 특정할 수도 있습니다.

```crystal
MyType(String, String) {"foo" => "bar"}
```
