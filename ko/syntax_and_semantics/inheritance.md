# 상속

상속 계층의 최상위에 있는 `Object`를 제외한 모든 클래스는, 다른 상위 클래스를 상속합니다. 상위 클래스를 지정하지 않으면 클래스의 경우는 기본으로 `Reference`를, 구조체의 경우 `Struct`를 상속합니다.

클래스는 상위 클래스의 생성자(`new`와 `initialize`)를 포함하여 모든 인스턴스 변수, 모든 인스턴스 메서드와 클래스 메서드를 상속합니다.

```crystal
class Person
  def initialize(@name : String)
  end

  def greet
    puts "안녕, 내 이름은 #{@name}!"
  end
end

class Employee < Person
end

employee = Employee.new "철수"
employee.greet # "안녕, 내 이름은 철수!"
```

클래스가 `new` 혹은 `initialize`를 정의한다면 상위 클래스의 생성자를 상속하지 않습니다.

```crystal
class Person
  def initialize(@name : String)
  end
end

class Employee < Person
  def initialize(@name : String, @company_name : String)
  end
end

Employee.new "철수", "방방주식회사" # OK
Employee.new "민수" # 오류: 'Employe:Class#new'의 인자 개수
                     # 틀림 (2개 자리에 1개)
```

파생 클래스에서 메서드를 덮어쓸 수도 있습니다. 이를 메서드 오버라이딩이라고 합니다.

```crystal
class Person
  def greet(msg)
    puts "안녕, #{msg}"
  end
end

class Employee < Person
  def greet(msg)
    puts "안뇽, #{msg}"
  end
end

p = Person.new
p.greet "얘들아" # "안녕, 얘들아"

e = Employee.new
e.greet "얘들아" # "안뇽, 얘들아"
```

오버라이딩 대신 타입 제약을 이용해 특수화된 메서드를 정의할 수도 있습니다.

```crystal
class Person
  def greet(msg)
    puts "안녕, #{msg}"
  end
end

class Employee < Person
  def greet(msg : Int32)
    puts "안녕, 숫자 #{msg}"
  end
end

e = Employee.new
e.greet "얘들아" # "안녕, 얘들아"

e.greet 1 # "안녕, 숫자 1"
```

## super

`super`로 상위 클래스의 메서드를 호출할 수 있습니다.

```crystal
class Person
  def greet(msg)
    puts "안녕, #{msg}"
  end
end

class Employee < Person
  def greet(msg)
    super # super(msg)와 동일
    super("다른 메시지")
  end
end
```

인자나 괄호 없이도 `super`는 메서드가 받은 것과 동일한 메서드를 받습니다. 인자를 넘길 경우, 그 인자를 대신 받습니다.
