# 열거형

열거형은 정수 값의 집합입니다. 각 값은 대응되는 이름을 갖습니다.

```crystal
enum Color
  Red
  Green
  Blue
end
```

열거형은 `enum` 예약어 뒤에 이름을 써서 정의합니다. 열거형의 본체에 값이 올 수 있습니다. 값은 `0`으로 시작하여 하나씩 증가합니다. 이 기본값을 덮어쓸 수도 있습니다.

```crystal
enum Color
  Red         # 0
  Green       # 1
  Blue   = 5  # 5로 덮어씀
  Yellow      # 6 (5 + 1)
end
```

열거형의 각 상수는 그 열거형의 타입을 갖습니다.

```crystal
Color::Red #:: Color
```

`value`를 호출하여 기저에 있는 값을 얻어올 수 있습니다.

```crystal
Color::Green.value #=> 1
```

그 값은 기본적으로 `Int32`이지만, 타입을 바꿀 수도 있습니다.

```crystal
enum Color : UInt8
  Red
  Green
  Blue
end

Color::Red.value #:: UInt8
```

기저 타입으로는 정수 타입만을 사용할 수 있습니다.

모든 열거형은 [Enum](http://crystal-lang.org/api/Enum.html)을 상속합니다.

## 플래그 열거형

열거형에 `@[Flags]` 속성을 지정하여 기본값을 바꿀 수 있습니다.

```crystal
@[Flags]
enum IOMode
  Read # 1
  Write  # 2
  Async # 4
end
```

`@[Flags]` 속성은 첫 상수의 값을 `1`로 바꾸고, 뒤에 오는 값을 `2`씩 곱합니다.

암묵적인 `None`과 `All` 상수가 열거형에 자동으로 추가됩니다. `None`의 값은 `0`이며 `All`의 값은 모든 상수에 비트 OR 연산을 한 값이 됩니다.

```crystal
IOMode::None.value #=> 0
IOMode::All.value  #=> 7
```

또한 `Enum`의 몇 가지 메서드를 사용하기 위해서는 `@[Flags]` 속성이 필요합니다.

```crystal
puts(Color::Red)                    # "Red"를 출력
puts(IOMode::Write | IOMode::Async) # "Write, Async"를 출력
```

## 정수에서 열거형으로의 변환

정수로부터 열거형을 생성할 수 있습니다.

```crystal
puts Color.new(1) #=> "Green"을 출력
```

열거형의 상수 중에 없는 값은 사용할 수 없습니다. 타입은 `Color`가 되겠지만, 출력해 보면 기저에 있는 값이 그대로 사용되는 것을 알 수 있습니다.

```crystal
puts Color.new(10) #=> "10"을 출력
```

이 메서드는 C에서의 정수를 크리스탈의 열거형으로 변환하는 것이 주 목적입니다.

## 메서드

클래스나 구조체와 마찬가지로 열거형에서 메서드를 정의할 수 있습니다.

```crystal
enum Color
  Red
  Green
  Blue

  def red?
    self == Color::Red
  end
end

Color::Red.red?  #=> true
Color::Blue.red? #=> false
```

인스턴스 변수는 정의할 수 없고, 클래스 변수만을 정의할 수 있습니다.

## 사용

열거형은 [Symbol](http://crystal-lang.org/api/Symbol.html)의 타입 안정성을 확보한 대안입니다. 예를 들어, API의 메서드에서 열거형을 이용한 [타입 제약](type_restrictions.html)을 특정할 수 있습니다.

```crystal
def paint(color : Color)
  case color
  when Color::Red
    # ...
  else
    # 확률은 높지 않아도 일어날 수 있음
    raise "알 수 없는 색깔: #{color}"
  end
end

paint Color::Red
```

위의 예시를 Symbol로 구현할 수도 있습니다.

```crystal
def paint(color : Symbol)
  case color
  when :red
    # ...
  else
    raise "알 수 없는 색깔: #{color}"
  end
end

paint :red
```

하지만 프로그래머가 `:reed` 같은 오타를 낸다면 실행 시간에나 오류를 알아차릴 수 있습니다. 하지만 `Color::Reed`는 컴파일 오류이므로 컴파일 시간에 알아차릴 수 있습니다.

가능하다면 열거형은 사용하는 것이 권장됩니다. 심볼은 API의 내부 구현에서만 사용하고 공개 API에서는 심볼을 가급적이면 피해야 합니다. 그렇다고 굳이 심볼을 쓰는 것을 말릴 사람이 있는 것은 아닙니다.
