# 스플랫과 튜플

*스플랫*(`*`)을 이용하여 메서드에 가변 인자를 넘길 수 있습니다. 스플랫은 인자 중 어느 위치에든 한 번만 나타날 수 있습니다.

```crystal
def sum(*elements)
  total = 0
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3    #=> 6
sum 1, 2, 3, 4.5 #=> 10.5
```

넘겨진 인자는 메서드 본체에서 [튜플](http://crystal-lang.org/api/Tuple.html)이 됩니다.

```crystal
# elements는 Tuple(Int32, Int32, Int32)
sum 1, 2, 3

# elements는 Tuple(Int32, Int32, Int32, Float64)
sum 1, 2, 3, 4.5
```

스플랫 인자 다음에는 이름 달린 인자만을 넘길 수 있습니다.

```crystal
def sum(*elements, initial = 0)
  total = initial
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3 # => 6
sum 1, 2, 3, initial: 10 # => 16
```

스플랫 뒤의 인자는 기본값이 없다면, 이름 달린 필수 인자입니다.

```crystal
def sum(*elements, initial)
  total = initial
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3 # 오류, 인자 없음: initial
sum 1, 2, 3, initial: 10 # => 16
```

이름 달린 인자가 다른 두 메서드는 서로 다른 메서드입니다.

```crystal
def foo(*elements, x)
  1
end

def foo(*elements, y)
  2
end

foo x: "머시기" # => 1
foo y: "머시기" # => 2
```

스플랫 인자에 이름을 짓지 않을 수도 있습니다. 이때는 "이 뒤로는 모두 이름 달린 인자여야 함"을 의미합니다.

```crystal
def foo(x, y, *, z)
end

foo 1, 2, 3    # 오류, 인자 개수 틀림 (예상 2개, 실제 3개)
foo 1, 2       # 오류, 인자 없음: z
foo 1, 2, z: 3 # 정상 작동
```

## 튜플 쪼개기

`*`을 이용하여 메서드 호출에 튜플을 쪼개어 넣을 수도 있습니다.

```crystal
def foo(x, y)
  x + y
end

tuple = {1, 2}
foo *tuple # => 3
```

## 더블 스플랫과 이름 달린 튜플

더블 스플랫(`**`)은 이름 달린 인자 중 다른 인자에 해당되지 않는 것을 전부 포착합니다. 그 인자의 타입은 `NamedTuple`입니다.

```crystal
def foo(x, **other)
  # 포착한 이름 달린 인자를 NamedTuple로 반환
  other
end

foo 1, y: 2, z: 3    # => {y: 2, z: 3}
foo y: 2, x: 1, z: 3 # => {y: 2, z: 3}
```

## 이름 달린 튜플을 쪼개기

`**`을 이용해 메서드 호출에 `NamedTuple`을 쪼개어 넣을 수 있습니다.

```crystal
def foo(x, y)
  x - y
end

tuple = {y: 3, x: 10}
foo **tuple # => 7
```
