# 제너릭

제너릭을 통해 다른 타입을 매개변수로 받는 타입을 정의할 수 있습니다. Box 타입을 생각해봅시다.

```crystal
class MyBox(T)
  def initialize(@value : T)
  end

  def value
    @value
  end
end

int_box = MyBox(Int32).new(1)
int_box.value # => 1 (Int32)

string_box = MyBox(String).new("안녕")
string_box.value # => "안녕" (String)

another_box = MyBox(String).new(1) # 오류, Int32는 String에 대응되지 않음
```

제너릭은 컬렉션 타입을 구현할 때 매우 유용합니다. `Array`, `Hash`, `Set`이 제너릭 타입입니다. `Pointer` 또한 제너릭 타입입니다.

타입 인자를 여러 개 쓸 수도 있습니다.

```crystal
class MyDictionary(K, V)
end
```

타입 인자로는 어떤 이름이든 사용할 수 있습니다.

```crystal
class MyDictionary(KeyType, ValueType)
end
```

## 타입 변수 추론

타입 인자가 따로 특정되지 않았다면 제너릭 타입 생성자에서의 타입 제약은 자유 변수이며, 타입 인자를 추론하기 위해 타입 제약이 사용됩니다.

```crystal
MyBox.new(1)       # : MyBox(Int32)
MyBox.new("안녕")  # : MyBox(String)
```

위 코드에서는 `MyBox`의 타입을 특정하지 않았지만, 컴파일러가 다음과 같은 과정을 통해 타입을 추론해냅니다.

* `MyBox.new(value)`가 `initialize(@value : T)`에 역할을 넘김
* `T`는 아직 어떤 타입도 아니므로, 주어진 인자의 타입으로 추론

이로써 제너릭 타입을 다루는 것이 한결 단순해집니다.

## 구조체와 모듈에서의 제너릭

구조체와 모듈 또한 제너릭 타입일 수 있습니다. 모듈이 제너릭이라면 다음과 같이 포함할 수 있습니다.

```crystal
module Moo(T)
  def t
    T
  end
end

class Foo(U)
  include Moo(U)

  def initialize(@value : U)
  end
end

foo = Foo.new(1)
foo.t # Int32
```

위의 예시에서, `Foo.new(1)`에서 `U`가 `Int32`이 되므로 제너릭 모듈이 포함되면서 `T` 또한 `Int32`가 되는 것을 볼 수 있습니다.

## 제너릭 타입의 상속

제너릭 클래스와 구조체를 상속할 수도 있습니다. 이때는 제너릭 타입을 특정할 수도, 혹은 타입 변수를 상위 타입에 위임할 수도 있습니다.

```crystal
class Parent(T)
end

class Int32Child < Parent(Int32)
end

class GenericChild(T) < Parent(T)
end
```
