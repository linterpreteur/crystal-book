# new, initialize, allocate

클래스에 `new` 메서드를 호출하여 그 클래스의 인스턴스를 생성합니다.

```
person = Person.new
```

이때, `person`은 `Person`의 인스턴스입니다.

지금은 `person`을 가지고 할 수 있는 일이 별로 없습니다. 몇 가지 개념을 추가해보겠습니다. `Person`에는 이름과 나이가 있습니다. "모든 것이 객체" 항목에서 보았듯, 객체는 타입을 가지며 메서드에 반응하고 이 메서드들은 객체와 상호작용하는 유일한 방법입니다. 따라서 `name`과 `age` 메서드가 필요합니다. 이 정보를 인스턴스 변수에 저장하겠습니다. 인스턴스 변수는 반드시 골뱅이(`@`) 문자로 시작해야 합니다. 또한 Person이 우리가 선택한 이름과 0세의 나이로 태어나도록 만들겠습니다. 이 "태어나는" 부분은 특수한 `initialize` 메서드로 작성합니다. 이 메서드는 보통 *생성자*라고 부릅니다.

```crystal
class Person
  def initialize(name : String)
    @name = name
    @age = 0
  end

  def name
    @name
  end

  def age
    @age
  end
end
```

이제 다음과 같이 사람을 만들 수 있습니다.

```crystal
cholsu = Person.new "철수"
minsu = Person.new "민수"

cholsu.name #=> "철수"
cholsu.age #=> 0

minsu.name #=> "민수"
```

(`name`은 `String`임을 특정했지만 `age`는 타입을 특정하지 않은 이유가 궁금하시다면 [전역 타입 추론 알고리즘](type_inference.html)에 그 설명이 있습니다.)

`Person`을 `new`로 만들었지만 초기화 부분은 `new` 메서드가 아닌 `initialize` 메서드에 정의했다는 것을 볼 수 있습니다. 이유가 무엇일까요?

`initialize` 메서드를 정의하면 크리스탈이 자동으로 다음과 같은 `new` 메서드를 생성하기 때문입니다.

```crystal
class Person
  def self.new(name : String)
    instance = Person.allocate
    instance.initialize(name)
    instance
  end
end
```

우선 `self.new` 표기에 주목합시다. 이는 이 메서드가 특정 인스턴스가 아닌 `Person` **클래스**에 속한다는 뜻입니다. 따라서 우리는 `Person.new`를 호출할 수 있습니다.

다음으로, `allocate`는 주어진 타입의 객체를 초기화하지 않은 상태로 생성하는 저수준 클래스 메서드입니다. 이 메서드는 기본적으로 객체에 필요한 메모리를 할당합니다. 그 후 이 객체에 `initialize`가 호출되고 그 인스턴스가 반환됩니다. `allocate`는  [안전하지 않은](unsafe.html) 코드이므로 직접 호출하는 일은 보통 없습니다. 하지만 여기서 `new`와 `initialize`의 연관성을 볼 수 있습니다.

