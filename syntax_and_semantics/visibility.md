# 가시성

메서드는 기본적으로 공개되어(public) 있습니다. 메서드가 공개되어 있다는 것은 언제든 이 메서드를 호출할 수 있다는 뜻입니다. `public` 예약어를 쓰지 않는다고 해도 그것이 기본값이기 때문입니다.

메서드에 `private` 혹은 `protected` 표시를 할 수 있습니다.

## private 메서드

`private` 메서드는 리시버 없이만 호출할 수 있습니다. 즉 점 앞에는 아무것도 올 수 없습니다.

```crystal
class Person
  private def say(message)
    puts message
  end

  def say_hello
    say "안녕" # 정상 동작, 리시버 없음
    self.say "안녕" # 오류, self가 리시버

    other = Person.new "Other"
    other.say "안녕" # 오류, other이 리시버
  end
end
```

`private` 메서드는 하위 클래스에서만 사용할 수 있습니다.

```crystal
class Employee < Person
  def say_bye
    say "잘 가" # 정상 동작
  end
end
```

## private 타입

private 타입은 그 타입이 정의된 네임스페이스에서만 참조될 수 있으며, 그 타입의 경로를 전부 적는 것은 허용되지 않습니다.

```crystal
class Foo
  private class Bar
  end

  Bar      # 정상 동작
  Foo::Bar # 오류
end

Foo::Bar # 오류
```

`private`은 `class`, `module`, `lib`, `enum`, `alias`와 상수에 사용할 수 있습니다.

```crystal
class Foo
  private ONE = 1

  ONE # => 1
end

Foo::ONE # 오류
```

## protected 메서드

`protected` 메서드는 다음 경우에만 호출할 수 있습니다.

1. 현재 타입과 같은 타입의 인스턴스에서
2. 현재 타입과 같은 (클래스, 구조체, 모듈 등의) 네임스페이스에 있는 인스턴스에서

```crystal
### 1의 예시

class Person
  protected def say(message)
    puts message
  end

  def say_hello
    say "안녕" # 정상 동작, 암묵적인 self는 Person
    self.say "안녕" # 정상 동작, self는 Person

    other = Person.new "Other"
    other.say "안녕" # OK, other은 Person
  end
end

class Animal
  def make_a_person_talk
    person = Person.new
    person.say "안녕" # 오류, person은 Person이지만
                       # 현재 타입은 Animal
  end
end

one_more = Person.new "One more"
one_more.say "안녕" # 오류, one_more은 Person이지만
                     # 현재 타입은 Program

### 2의 예시

module Namespace
  class Foo
    protected def foo
      puts "안녕"
    end
  end

  class Bar
    def bar
      # Foo와 Bar은 Namespace에 있으므로 동작
      Foo.new.foo
    end
  end
end

Namespace::Bar.new.bar
```

`protected` 클래스 메서드는 인스턴스 메서드에서 우회하여 호출할 수 있습니다.

```crystal
class Person
  protected def self.say(message)
    puts message
  end

  def say_hello
    Person.say "안녕" # 정상 동작
  end
end
```

## private 최상위 메서드

`private` 최상위 메서드는 현재 파일에서만 사용할 수 있습니다.

```crystal
# one.cr 파일
private def greet
  puts "안녕"
end

greet #=> "안녕"

# two.cr 파일
require "./one"

greet # 정의되지 않은 지역 변수 혹은 메서드 'greet'
```

이로써 한 파일에서만 사용할 헬퍼 메서드를 정의할 수 있습니다.

## private 최상위 타입

`private` 최상위 타입은 현재 파일에서만 사용할 수 있습니다.

```crystal
# one.cr 파일
private class Greeter
  def self.greet
    "안녕"
  end
end

Greeter.greet #=> "안녕"

# two.cr 파일
require "./one"

Greeter.greet # 정의되지 않은 상수 'Greeter'
```
