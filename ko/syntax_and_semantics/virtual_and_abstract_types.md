# 가상 타입과 추상 타입

한 변수의 타입이 같은 계층 아래의 여러 타입을 조합한 것이라면, 그 타입은 **가상 타입**이 됩니다. 이는 `Reference`, `Value`, `Int`, `Float`을 제외한 모든 클래스와 구조체에 적용됩니다.

```crystal
class Animal
end

class Dog < Animal
  def talk
    "멍!"
  end
end

class Cat < Animal
  def talk
    "냥"
  end
end

class Person
  getter pet

  def initialize(@name : String, @pet : Animal)
  end
end

cholsu = Person.new "철수", Dog.new
minsu = Person.new "민수", Cat.new
```

위의 프로그램을 `tool hierarchy` 명령어로 컴파일한다면 `Person`에 대해 다음을 볼 수 있습니다.

```
- class Object
  |
  +- class Reference
     |
     +- class Person
            @name : String
            @pet : Animal+
```

`@pet`이 `Animal+`이라는 것을 볼 수 있습니다. `+` 기호는 가상 타입을 나타냅니다. "`Animal`을 포함해 `Animal`을 상속하는 임의의 타입"을 의미합니다.

컴파일러는 같은 위계에 있는 타입이라면 언제나 타입 공용체를 가상 타입으로 해석합니다.

```
if some_condition
  pet = Dog.new
else
  pet = Cat.new
end

# pet : Animal+
```

같은 위계에 있는 구조체와 클래스에 대해서는 항상 공용체가 가상 타입이 됩니다. 컴파일러는 항상 (`Reference`, `Value`, `Int`, `Float`을 제외하고) 가장 가까운 공통 상위 클래스를 찾을 것입니다. 찾을 수 없는 경우에는 타입 공용체가 유지됩니다.

이런 과정이 일어나는 진짜 이유는, 비슷하지만 다른 온갖 종류의 공용체를 만들지 않으므로 컴파일이 더욱 빨라질 뿐 아니라 코드의 용량 또한 줄어들기 때문입니다. 굳이 그런 이유가 아니더라도 가상 타입을 만드는 쪽이 더 말이 되기도 합니다. 같은 위계 하의 클래스는 비슷하게 동작하기 때문입니다.

철수의 애완동물한테 말을 걸어볼까요.

```crystal
cholsu.pet.talk # 오류: Animal에 'talk' 메서드 정의되지 않음
```

이때 `@pet`은 `Animal`을 포함하는 `Animal+`로 취급되기 때문에 오류가 발생합니다. `Animal`에서는 `talk` 메서드를 찾을 수 없기 때문에 오류가 되는 것입니다.

컴파일러가 모르는 것은, `Animal`의 인스턴스를 만든다는 것은 말이 안 되고 절대로 일어나지 않는단 사실입니다. 컴파일러에게 이를 알려주는 방법은 클래스를 `abstract`로 표시하는 것입니다.

```crystal
abstract class Animal
end
```

이제 코드는 정상적으로 컴파일됩니다.

```crystal
cholsu.pet.talk #=> "멍!"
```

클래스를 추상 클래스로 지정하면 인스턴스를 실수로 만드는 것을 방지할 수도 있습니다.

```crystal
Animal.new # 오류: 추상 클래스 Animal의 인스턴스를 만들 수 없음
```

`Animal`이 `talk` 메서드를 정의해야 한다는 것을 명시적으로 나타내기 위해 추상 메서드를 추가할 수 있습니다.

```crystal
abstract class Animal
  # Makes this animal talk
  abstract def talk
end
```

메서드를 `abstract`로 지정하면 컴파일러는 모든 하위 클래스에 대해 이 메서드가 설령 사용되지 않는다고 해도 구현되어 있는지 점검합니다.

추상 메서드는 모듈에도 정의될 수 있으며, 이 경우 컴파일러는 그 모듈을 포함하는 타입이 해당 추상 메서드를 구현하는지 검사합니다.
