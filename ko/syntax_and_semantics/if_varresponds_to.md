# if var.responds_to?(...)

`if`의 조건이 `responds_to?` 테스트라면, `then` 분기에서 해당 변수의 타입은 그 메서드를 갖는 타입으로 한정됩니다.

```crystal
if a.responds_to?(:abs)
  # 이때 a의 타입은 'abs' 메서드를 갖는 타입에 한정
end
```

추가로 `else` 분기에서 해당 변수의 타입은 그 메서드를 갖지 않는 타입으로 한정됩니다.

```crystal
a = some_condition ? 1 : "hello"
# a : Int32 | String

if a.responds_to?(:abs)
  # Int#abs가 있지만 String#abs는 없으므로 이때 a는 Int32
else
  # 이때 a는 String
end
```

하지만 이는 인스턴스 변수나 클래스 변수에는 적용되지 **않습니다**. 이런 경우에는 우선 변수에 할당해야 합니다.

```crystal
if @a.responds_to?(:abs)
  # 이때 @a는 `abs`를 갖는 타입이 아닐 수도 있음
end

a = @a
if a.responds_to?(:abs)
  # 이때 a는 `abs`를 갖는 타입
end

# 더 짧게는
if (a = @a).responds_to?(:abs)
  # 이때 a는 `abs`를 갖는 타입
end
```

