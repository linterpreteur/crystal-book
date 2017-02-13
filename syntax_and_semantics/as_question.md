# as?

유사 메서드 `as?`는 `as`와 비슷하지만, 주어진 타입이 들어맞지 않을 경우 예외를 일으키지 않고 `nil`을 반환합니다. 또한 포인터 타입과 다른 타입 사이의 변환에 사용될 수 없다는 것이 차이입니다.

Example:

```crystal
value = rand < 0.5 ? -3 : nil
result = value.as?(Int32) || 10

value.as?(Int32).try &.abs
```
