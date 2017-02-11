# 메서드 변수와 인스턴스 변수

메서드의 인자를 인스턴스 변수에 할당하기 위한 짧은 문법을 사용해 생성자를 더욱 간단하게 쓸 수 있습니다.

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end
end
```

지금은 Person에 기능이 많지 않습니다. 이름을 주거나 이름과 나이를 얻는 것이 전부이며, 그 나이는 항상 0일 것입니다. 나이를 먹는 메서드를 추가해보겠습니다.

```crystal
class Person
  def become_older
    @age += 1
  end
end

john = Person.new "철수"
peter = Person.new "민수"

john.age #=> 0

john.become_older
john.age #=> 1

peter.age #=> 0
```

메서드 이름은 관습에 따라 소문자로 시작하며, 소문자와 숫자, 밑줄만을 사용합니다.

부연하자면 `become_older` 메서드를 원래의 `Person` 정의에 쓸 수도 있고 별도의 정의에 둘 수도 있습니다. 크리스탈은 모든 정의를 클래스 하나로 조합하기 때문입니다. 다음 코드도 문제 없이 작동할 것입니다.

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end
end

class Person
  def become_older
    @age += 1
  end
end
```

## 메서드 재정의와 previous_def

메서드를 재정의할 경우 마지막에 나오는 정의가 우선시됩니다.

```crystal
class Person
  def become_older
    @age += 1
  end
end

class Person
  def become_older
    @age += 2
  end
end

person = Person.new "철수"
person.become_older
person.age #=> 2
```

`previous_def`로 이전에 정의한 메서드를 호출할 수도 있습니다.

```crystal
class Person
  def become_older
    @age += 1
  end
end

class Person
  def become_older
    previous_def
    @age += 2
  end
end

person = Person.new "철수"
person.become_older
person.age #=> 3
```

인자나 괄호 없이도 `previous_def`는 메서드 인자와 동일한 인자를 받습니다. 인자를 넘겨준다면, 그 인자를 받습니다.

## 상시 적용 초기화

인스턴스 변수는 `initialize` 메서드 밖에서도 초기화할 수 있습니다.

```crystal
class Person
  @age = 0

  def initialize(@name : String)
  end
end
```

이로써 `@age`를 항상 0으로 초기화합니다. 이는 중복을 피할 때 유용하며, 추후 클래스에 인스턴스 변수를 추가할 때 `Nil` 타입을 피하는 데에도 유용합니다.

