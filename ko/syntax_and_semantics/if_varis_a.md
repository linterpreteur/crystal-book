# if var.is_a?(...)

`if`의 조건이 `is_a?` 테스트라면, `then` 분기에서 해당 변수의 타입은 그 타입으로 한정됩니다.

```crystal
if a.is_a?(String)
  # a는 String
end

if b.is_a?(Number)
  # b는 Number
end
```

추가로 `else` 분기에서 해당 변수의 타입은 그 타입이 아니게 됩니다.

```crystal
a = some_condition ? 1 : "hello"
# a : Int32 | String

if a.is_a?(Number)
  # a : Int32
else
  # a : String
end
```

`is_a?` 테스트는 추상 클래스와 모듈 등 어떤 타입에든 적용할 수 있습니다.

조건에 `&&`가 있을 때에도 똑같이 적용됩니다.

```crystal
if a.is_a?(String) && b.is_a?(Number)
  # a는 String, b는 Number
end
```

하지만 이는 인스턴스 변수나 클래스 변수에는 적용되지 **않습니다**. 이런 경우에는 우선 변수에 할당해야 합니다.

```crystal
if @a.is_a?(String)
  # 이때 @a는 String이 아닐 수도 있음
end

a = @a
if a.is_a?(String)
  # 이때 a는 String임이 보장됨
end

# 더 짧게는
if (a = @a).is_a?(String)
  # 이때 a는 String임이 보장됨
end
```
