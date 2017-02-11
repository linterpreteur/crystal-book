# 연산자

`+`와 `-` 같은 연산자는 다른 메서드와 다를 바 없는, 정규 메서드입니다.

```crystal
a + b
```

이 코드는 다음과 동일합니다.

```crystal
a.+(b)
```

새로운 타입에 연산자를 정의할 수도 있습니다.

```crystal
struct Vector2
  getter x, y

  def initialize(@x : Int32, @y : Int32)
  end

  def +(other)
    Vector2.new(x + other.x, y + other.y)
  end
end

v1 = Vector2.new(1, 2)
v2 = Vector2.new(3, 4)
v1 + v2               #=> Vector2(@x=4, @y=6)
```

다음으로 모든 연산자의 일반적인 의미를 살펴보겠습니다.

## 단항 연산자

```crystal
+   # 단항 더하기
-   # 산술 부정
!   # 논리 부정
~   # 비트 보수
```

이 연산자들을 정의할 때에는 인자를 쓰지 않습니다.

```crystal
struct Vector2
  def -
    Vector2.new(-x, -y)
  end
end

v1 = Vector2.new(1, 2)
-v1                    #=> Vector2(@x=-1, @y=-2)
```

**주의:** `!` (논리 부정) 연산자는 메서드로 정의할 수 없습니다. 의미를 바꿀 수 없다는 것입니다.

## 이항 연산자

```crystal
+   # 덧셈
-   # 뺄셈
*   # 곱셈
/   # 나눗셈
%   # 나머지
&   # 비트 AND
|   # 비트 OR
^   # 비트 XOR
**  # 지수
<<  # 비트 왼쪽 시프트, 또는 추가의 의미
>>  # 비트 오른쪽 시프트
==  # 같다
!=  # 같지 않다
<   # 작다
<=  # 작거나 같다
>   # 크다
>=  # 크거나 같다
<=> # 비교
=== # (숫자의 경우, 값뿐 아니라 타입도) 같다
```

## 인덱싱

```crystal
[]  # 배열 인덱스 (범위를 벗어나면 예외 일어남)
[]? # 배열 인덱스 (범위를 벗어나면 nil 반환)
[]= # 배열 인덱스 할당
```

다음과 같이 사용합니다.

```crystal
class MyArray
  def [](index)
    # ...
  end

  def [](index1, index2, index3)
    # ...
  end

  def []=(index, value)
    # ...
  end
end

array = MyArray.new

array[1]       # 첫 번째 메서드 호출
array[1, 2, 3] # 두 번째 메서드 호출
array[1] = 2   # 세 번째 메서드 호출

array.[](1)       # 첫 번째 메서드 호출
array.[](1, 2, 3) # 두 번째 메서드 호출
array.[]=(1, 2)   # 세 번째 메서드 호출
```

## 의미

연산자에 어떤 의미든 부여할 수 있으나, 암호 같이 난해한 코드나 예측할 수 없는 방식으로 작동하는 코드를 방지하기 위해 위에서 본 관습적인 의미를 따르는 것이 좋습니다.

