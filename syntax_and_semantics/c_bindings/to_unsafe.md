# to_unsafe

어떤 타입이 `to_unsafe` 메서드를 정의한다면, C에 값을 넘길 때 이 메서드가 반환하는 값이 전달됩니다.

```crystal
lib C
  fun exit(status : Int32) : NoReturn
end

class IntWrapper
  def initialize(@value)
  end

  def to_unsafe
    @value
  end
end

wrapper = IntWrapper.new(1)
C.exit(wrapper) # wrapper는 Int32가 아니지만 to_unsafe 메서드는
                # 맞기 때문에 wrapper.to_unsafe가 대신 전달됨
```

이는 값을 명시적으로 변환하지 않으면서도 C 타입의 래퍼를 정의할 때 아주 유용합니다.

예를 들어 `String` 클래스는 `UInt8*`를 반환하는 `to_unsafe`를 구현하고 있습니다.

```crystal
lib C
  fun printf(format : UInt8*, ...) : Int32
end

a = 1
b = 2
C.printf "%d + %d = %d\n", a, b, a + b
```
