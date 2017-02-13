# 클래스 변수

클래스 변수는 인스턴스 대신에 클래스에 붙는 변수입니다. 클래스 변수는 "골뱅이" 두 개(`@@`)로 시작합니다.

```crystal
class Counter
  @@instances = 0

  def initialize
    @@instances += 1
  end

  def self.instances
    @@instances
  end
end

Counter.instances #=> 0
Counter.new
Counter.new
Counter.new
Counter.instances #=> 3
```

클래스 변수는 클래스 메서드 혹은 인스턴스 변수에서 읽거나 쓸 수 있습니다.

그 타입은 [전역 타입 추론 알고리즘](type_inference.html)을 이용해 추론됩니다.

클래스 변수는 하위 클래스에 의해 상속됩니다. 하지만 서로 다른 클래스는 클래스 변수를 공유하지 않습니다. 예시를 보겠습니다.

```crystal
class Parent
  @@numbers = [] of Int32

  def self.numbers
    @@numbers
  end
end

class Child < Parent
end

Parent.numbers # => []
Child.numbers # => []

Parent.numbers << 1
Parent.numbers # => [1]
Child.numbers # => []
```

클래스 변수는 모듈과 구조체에도 붙을 수 있습니다. 앞서 본 것과 마찬가지로, 모듈과 구조체의 클래스 변수는 포함하거나 상속하는 타입에 상속됩니다.
