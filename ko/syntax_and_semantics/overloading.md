# 오버로딩

몇 살이나 나이를 먹을지 인자를 받는 `become_older` 메서드를 정의할 수 있습니다.

```crystal
class Person
  @age = 0

  def become_older
    @age += 1
  end

  def become_older(years)
    @age += years
  end
end

cholsu = Person.new "철수"
cholsu.age #=> 0

cholsu.become_older
cholsu.age #=> 1

cholsu.become_older 5
cholsu.age #=> 6
```

즉, 이름이 같지만 다른 인자를 받는 메서드를 정의할 수 있습니다. 이 둘은 별도의 메서드로 취급됩니다. 이런 경우를 가리켜 *메서드 오버로딩*이라고 합니다.

메서드 오버로드에는 몇 가지 기준이 있습니다.

* 인자의 개수
* 인자에 적용된 타입 제약
* 이름 달린 인자 중 필수적인 인자들의 이름
* 메서드가 [블락](blocks_and_procs.html)을 받는지 여부

예를 들어, 서로 다른 네 가지의 `become_older` 메서드를 정의할 수 있습니다.

```crystal
class Person
  @age = 0

  # 나이를 한 살 먹기
  def become_older
    @age += 1
  end

  # 나이를 주어진 만큼 먹기
  def become_older(years : Int32)
    @age += years
  end

  # 나이를 문자열로 주어진 만큼 먹기
  def become_older(years : String)
    @age += years.to_i
  end

  # 이 사람의 현재 나이를 블락에 넘겨서
  # 블락이 반환한 만큼 나이를 먹기
  def become_older
    @age += yield @age
  end
end

person = Person.new "철수"

person.become_older
person.age #=> 1

person.become_older 5
person.age #=> 6

person.become_older "12"
person.age #=> 18

person.become_older do |current_age|
  current_age < 20 ? 10 : 30
end
person.age #=> 28
```

블락을 받는 메서드의 경우는 `yield`에 의해 구분된다는 것을 볼 수 있습니다. 마지막에 의미 없는 `&block` 인자를 추가함으로써 이를 명시적으로 나타낼 수 있습니다.

```crystal
class Person
  @age = 0

  def become_older(&block)
    @age += yield @age
  end
end
```

자동으로 생성되는 문서에서는 이 `&block` 인자가 명시하지 않더라도 항상 나타날 것입니다.

인자의 개수가 같다면 컴파일러는 제약이 많은 순서로 정렬합니다.

```crystal
class Person
  @age = 0

  # 이 메서드가 우선 정의됨
  def become_older(age)
    @age += age
  end

  # 제약이 아예 없는 것보다 "String"은 제약이 더 많은 셈이므로,
  # 컴파일러는 어떤 오버로드가 사용될지 결정할 때
  # 이 메서드를 앞서 정의한 것보다 우선으로 고려합니다.
  def become_older(age : String)
    @age += age.to_i
  end
end

person = Person.new "철수"

# 첫 번째 정의를 호출
person.become_older 20

# 두 번째 정의를 호출
person.become_older "12"
```

하지만 완전한 순서가 있어서 컴파일러가 순서를 항상 알아낼 수 있는 것은 아니기 때문에, 제약이 더 많은 메서드를 앞에, 제약이 더 적는 메서드를 뒤에 두는 것이 좋은 습관입니다.
