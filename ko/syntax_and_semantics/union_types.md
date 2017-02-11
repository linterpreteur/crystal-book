# 공용체 타입

한 변수나 표현식은 다수의 타입을 가질 수 있습니다. 이런 경우를 공용체 타입을 갖는다고 표현합니다. 한 변수를 [if](if.html) 분기문 안에서 다른 값을 할당할 때를 예로 들 수 있습니다.

```crystal
if 1 + 2 == 3
a = 1
else
a = "hello"
end

a # : Int32 | String
```

분기문 끝에서 `a`는 `Int32 | String` 타입을 가지며, 이는 "Int32와 String의 공용체"입니다. 이 공용체 타입은 컴파일러에 의해 자동으로 생성됩니다. 실행 시간에 `a`는 물론 그 중 한 타입만을 갖습니다. 이는 `class` 메서드를 호출하여 볼 수 있습니다.

```crystal
# 실행 시간 타입
a.class # => Int32
```

컴파일 시간 타입은 [typeof](typeof.html)으로 볼 수 있습니다.

```crystal
# 컴파일 시간 타입
typeof(a) # => Int32 | String
```

공용체 타입은 아주 많은 수의 타입으로 이루어질 수 있습니다. 공용체 타입을 갖는 변수에 메서드를 호출할 때, 공용체 타입의 모든 타입이 해당 메서드를 갖지 않는다면 컴파일 시간 오류가 발생합니다. 반환 값의 타입은 그 메서드들의 반환 타입의 공용체 타입입니다.

```crystal
# to_s는 Int32와 String에 정의되어 있으며 String을 반환
a.to_s # => String

a + 1 # String#+(Int32)는 정의되어 있지 않기 때문에 오류 발생
```

필요한 경우라면 컴파일 시간에 변수를 공용체 타입으로 정의할 수도 있습니다.

```
# 컴파일 시간 타입 지정
a = 0.as(Int32|Nil|String)
typeof(a) # => Int32 | Nil | String
```

## 공용체 타입 규칙

`T1`과 `T2`의 두 타입이 공용체가 될 때 결과는 보통 `T1 | T2`가 됩니다. 하지만 결과 타입이 다른 경우도 있습니다.

### 같은 위계를 갖는 클래스와 구조체의 공용체

`T1`과 `T2`가 같은 위계 구조를 가지며 가장 가까운 공통 조상이 `Reference`, `Struct`, `Int`, `Float`, `Value`가 아닌 `Parent`일 경우 결과 타입은 `Parent+`가 됩니다. 이를 가상 타입이라고 합니다. 컴파일러는 이 타입을 `Parent` 혹은 그를 상속하는 타입으로 취급할 것입니다.

```crystal
class Foo
end

class Bar < Foo
end

class Baz < Foo
end

bar = Bar.new
baz = Baz.new

# 이때 foo의 타입은 Bar | Baz이지만,
# Bar과 Baz가 Foo를 상속하기 때문에
# 결과 타입은 Foo+
foo = rand < 0.5 ? bar : baz
typeof(foo) # => Foo+
```

### 같은 크기를 갖는 튜플의 공용체

같은 크기를 갖는 두 튜플의 공용체는 각 위치에서의 공용체 타입을 갖는 튜플 타입이 됩니다.

예를 들자면, 다음과 같습니다.

```crystal
t1 = {1, "hi"} # Tuple(Int32, String)
t2 = {true, nil} # Tuple(Bool, Nil)

t3 = rand < 0.5 ? t1 : t2
typeof(t3) # Tuple(Int32 | Bool, String | Nil)
```

### 같은 키를 갖는 이름 달린 튜플의 공용체

두 개 이상의 이름 달린 튜플이 (순서와 상관 없이) 같은 키를 갖는다면 각 키의 공용체 타입을 갖는 이름 달린 튜플이 됩니다. 이 키의 순서는 좌측에 있는 것의 순서를 따릅니다.

```crystal
t1 = {x: 1, y: "hi"} # Tuple(x: Int32, y: String)
t2 = {y: true, x: nil} # Tuple(y: Bool, x: Nil)

t3 = rand < 0.5 ? t1 : t2
typeof(t3) # NamedTuple(x: Int32 | Nil, y: String | Bool)
```
