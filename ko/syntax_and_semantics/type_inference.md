# 타입 추론

크리스탈의 철학은 타입 표기를 최소한 줄이는 것입니다. 그럼에도 표기가 필요한 경우가 있습니다.

다음과 같은 클래스 정의를 생각해봅시다.

```crystal
class Person
  def initialize(@name)
    @age = 0
  end
end
```

`@age`가 정수라는 것은 빠르게 알아챌 수 있지만 `@name`의 타입은 알 수가 없습니다. 컴파일러가 `Person` 클래스의 쓰임새로부터 그 타입을 추론해낼 수도 있을 것입니다. 하지만 문제가 있습니다.

* 코드를 읽는 인간에게 타입이 분명하지 않습니다. 컴파일러뿐 아니라 사람도 `Person`의 모든 쓰임새를 찾아봐야 할 것입니다.
* 메서드를 한 번만 분석하는 것이나, 증분 컴파일과 같은 일부 컴파일러 최적화 기법이 거의 불가능해집니다.

코드가 방대해짐에 따라 이 문제의 연관성은 더욱 커집니다. 프로젝트를 이해하는 것은 힘들어지고, 컴파일 시간은 견디기 힘들 만큼 길어지는 것입니다.

이런 이유로 크리스탈은 인스턴스 변수와 [클래스](class_variables.html) 변수의 타입을 (인간에게 명확한 만큼) 명확하게 알고 있어야 합니다.

이를 달성하는 방법은 여러 가지가 있습니다.

## 명시적 타입 표기 사용

가장 쉽지만 가장 재미없는 방법은 타입을 명시적으로 표기하는 것입니다.

```crystal
class Person
  @name : String
  @age : Int32

  def initialize(@name)
    @age = 0
  end
end
```

## 명시적 타입 표기 미사용

명시적으로 타입을 적어주지 않는다면 컴파일러는 인스턴스 변수와 클래스 변수의 타입을 문법 규칙으로부터 추론하려 할 것입니다.

규칙을 통해 주어진 인스턴스 또는 클래스 변수로부터 타입을 추론할 수 있다면, 해당 타입이 집합에 추가됩니다. 모든 규칙을 적용한 후에 추론되는 타입은 집합에 있는 타입의 [공용체](union_types.html)가 됩니다. 추가로 인스턴스 변수가 항상 초기화되는 것은 아니라고 추론하는 경우에는 [Nil](literals/nil.html) 타입도 포함됩니다.

많은 규칙이 있지만 가장 많이 쓰이는 것은 세 개가 있습니다. 이를 모두 기억할 필요는 없습니다. 컴파일러가 인스턴스 변수의 타입을 추론할 수 없다는 오류를 띄운다면 언제든 명시적으로 타입을 적어줌으로써 해결할 수 있습니다.

지금부터 볼 규칙들은, 인스턴스 변수의 경우만 언급하지만 클래스 변수에도 동일하게 적용됩니다.

### 1. 리터럴 값을 할당

인스턴스 변수에 리터럴이 할당된다면 그 리터럴의 타입이 집합에 추가됩니다. 모든 [리터럴](literals.html)에는 주어지는 타입이 있습니다.

다음 예시에서 `@name`은 `String`으로, `@age`는 `Int32`로 추론됩니다.

```crystal
class Person
  def initialize
    @name = "John Doe"
    @age = 0
  end
end
```

이 규칙을 비롯해 모든 규칙은 `intialize`가 아닌 메서드에도 적용됩니다.

```crystal
class SomeObject
  def lucky_number
    @lucky_number = 42
  end
end
```

이 경우, `@lucky_number`는 `Int32 | Nil`로 추론됩니다. 42가 할당되었으므로 `Int32`가, 항상 할당되는 것은 아니므로 `Nil`이 추론되는 것입니다.

### 2. 클래스 메서드 `new` 호출의 결과를 할당

`Type.new(...)` 식의 표현식이 인스턴스 변수에 할당된다면 `Type` 타입이 집합에 추가됩니다.

다음 예시에서 `@address`는 `Address`로 추론됩니다.

```crystal
class Person
  def initialize
    @address = Address.new("somewhere")
  end
end
```

제너릭 타입의 경우도 마찬가지입니다. 다음과 같은 경우 `@values`는 `Array(Int32)`로 추론됩니다.

```crystal
class Something
  def initialize
    @values = Array(Int32).new
  end
end
```

**주의**: `new` 메서드를 재정의하는 타입이 있을 수 있습니다. 이 경우 다음 규칙을 이용하여 추론할 수 있다면, 추론되는 타입은 `new`가 반환하는 타입입니다.

### 3. 타입 제약이 있는 메서드 인자를 할당

다음 예시에서 메서드 인자 `name`이 `String`의 타입 제약이 있기 때문에 `@name`은 `String`으로 추론되며, 그 인자가 `@name`에 할당됩니다.

```crystal
class Person
  def initialize(name : String)
    @name = name
  end
end
```

그 인자의 이름은 전혀 중요하지 않습니다. 다음과 같은 경우도 동일하게 동작합니다.

```crystal
class Person
  def initialize(obj : String)
    @name = obj
  end
end
```

메서드 인자를 인스턴스 변수에 할당하는 짧은 문법을 쓸 때에도 같은 효과가 있습니다.

```crystal
class Person
  def initialize(@name : String)
  end
end
```

또한 컴파일러는 인자에 다른 값이 재할당되는지 검사하지 않는다는 사실에 유의해야 합니다.

```crystal
class Person
  def initialize(name : String)
    name = 1
    @name = name
  end
end
```

이 경우에도 `@name`은 `String`으로 추론되어, 나중에서야 `Int32`는 `String` 타입의 변수에 할당될 수 없다며 컴파일 오류를 내놓을 것입니다. `@name`이 `String`이 아닌 경우라면 명시적으로 타입을 표기해주어야 합니다.

### 4. 반환 타입 표기가 있는 클래스 메서드의 결과를 할당

다음 예시에서, 클래스 메서드 `Address.unknown`에 `Address` 반환 표기가 있기 때문에 `@address`는 `Address`로 추론됩니다.

```crystal
class Person
  def initialize
    @address = Address.unknown
  end
end

class Address
  def self.unknown : Address
    new("unknown")
  end

  def initialize(@name : String)
  end
end
```

사실 이런 경우에는 `self.unknown`에 반환 타입 표기가 필요 없습니다. 컴파일러가 메서드 본체를 보고서 앞서 본 규칙 중 하나를 적용할 수 있다면 그로부터 타입을 추론할 것이기 때문입니다. 따라서 단순하게 다음과 같이 쓸 수도 있습니다.

```crystal
class Person
  def initialize
    @address = Address.unknown
  end
end

class Address
  # 반환 타입 표기 필요 없음
  def self.unknown
    new("unknown")
  end

  def initialize(@name : String)
  end
end
```

이 규칙은 `new`에 더해 "생성자와 유사한" 클래스 메서드가 여럿 있는 경우가 매우 흔하기 때문에 편리하게 이용됩니다.

### 5. 기본값이 있는 메서드 인자를 할당

다음 예시에서 `name`의 기본값이 문자열 리터럴이며 이 인자가 `@name`에 할당되기 때문에, 추론 타입의 집합에 `String`이 추가됩니다.

```crystal
class Person
  def initialize(name = "John Doe")
    @name = name
  end
end
```

물론 짧은 문법을 써도 같습니다.

```crystal
class Person
  def initialize(@name = "John Doe")
  end
end
```

`Type.new(...)` 메서드 혹은 반환 타입 표기가 있는 클래스 메서드 또한 기본값으로 넘길 수 있습니다.

### 6. `lib` 함수 호출의 결과를 할당

[lib 함수](c_bindings/fun.html)에는 명시적으로 타입이 있어야 하기 때문에 이를 인스턴스 변수에 할당할 때 컴파일러가 그 반환 타입을 이용할 수 있습니다.

다음 예시에서 `@age`는 `Int32`로 추론됩니다.

```crystal
class Person
  def initialize
    @age = LibPerson.compute_default_age
  end
end

lib LibPerson
  fun compute_default_age : Int32
end
```

### 7. lib의 `out` 표현식 사용

[lib 함수](c_bindings/fun.html)에는 명시적으로 타입이 있어야 하기 때문에 컴파일러가 `out` 인자의 타입을 이용할 수 있습니다. 이 인자는 포인터 타입이어야 하며 참조를 해제한 타입이 추론됩니다.

다음 예시에서 `@age`는 `Int32`로 추론됩니다.

```crystal
class Person
  def initialize
    LibPerson.compute_default_age(out @age)
  end
end

lib LibPerson
  fun compute_default_age(age_ptr : Int32*)
end
```

### 기타 규칙

컴파일러는 타입 표기를 줄이기 위해 최대한 노력합니다. 예를 들어 `if` 표현식을 할당할 때는, `then`과 `else` 분기로부터 타입이 추론될 것입니다.

```crystal
class Person
  def initialize
    @age = some_condition ? 1 : 2
  end
end
```

위의 `if`(음, 엄밀히는 삼항 연산자지만 `if`에 비슷하잖아요)는 정수 리터럴을 가지므로 `@age`는 불필요한 타입 표기 없이도 `Int32`로 성공적으로 추론됩니다.

또 다른 예는 `||`와 `||=`입니다.

```crystal
class SomeObject
  def lucky_number
    @lucky_number ||= 42
  end
end
```

위의 예에서 `@lucky_number`는 `Int32 | Nil`로 추론됩니다. 늦은 시점에 초기화되는 변수의 경우 아주 유용합니다.

상수도 이용할 수 있으며, 컴파일러에게 (그리고 인간에게) 이는 아주 간단합니다.

```crystal
class SomeObject
  DEFAULT_LUCKY_NUMBER = 42

  def initialize(@lucky_number = DEFAULT_LUCKY_NUMBER)
  end
end
```

여기서는 5번째 규칙(인자의 기본값)이 사용되며, 상수가 정수 리터럴로 해석되기 때문에 `@lucky_number`가 `Int32`로 추론될 수 있습니다.
