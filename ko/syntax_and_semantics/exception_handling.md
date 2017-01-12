# 예외 처리

크리스탈에서 오류를 다루는 방법은 예외를 일으키고 구조하는 것입니다.

## 예외 일으키기

최상위 `raise` 메서드를 호출하여 예외를 일으킵니다. 기타 예약어와는 다르게 `raise`는 두 가지 오버로드가 있는 보통의 메서드입니다. [하나는 String을 받고](http://crystal-lang.org/api/toplevel.html#raise%28message%20%3A%20String%29-class-method), [하나는 Exception 인스턴스를 받습니다](http://crystal-lang.org/api/toplevel.html#raise%28ex%20%3A%20Exception%29-class-method).

```crystal
raise "으악!"
raise Exception.new("무슨 오류")
```

String을 받는 것은 그것을 메시지로 하여 새로운 [Exception](http://crystal-lang.org/api/Exception.html) 인스턴스를 생성합니다.

`Exception`과 그 하위 클래스의 인스턴스만을 일으킬 수 있습니다.

## 사용자 정의 예외 정의

[Exception](http://crystal-lang.org/api/Exception.html)의 하위 타입을 만들어서 예외 타입을 정의할 수 있습니다.

```crystal
class MyException < Exception
end

class MyOtherException < Exception
end
```

다른 경우와 마찬가지로, 새로이 생성자를 만들거나 기본 생성자를 이용할 수 있습니다.

## 예외 구조

`begin ... rescue ... end` 표현식으로 예외를 구조합니다.

```crystal
begin
  raise "꽥!"
rescue
  puts "살았다!"
end

# 출력: 살았다!
```

`rescue` 절에서 변수를 특정하여 구조된 예외에 접근할 수 있습니다.

```crystal
begin
  raise "끄억!"
rescue ex
  puts ex.message
end

# 출력: 끄억!
```

특정 타입, 혹은 그 하위 타입의 예외만을 구조할 수도 있습니다.

```crystal
begin
  raise MyException.new("앗!")
rescue MyException
  puts "MyException이다!"
end

# 출력: MyException이다!
```

이 경우, 타입 제약과 비슷한 문법으로 그 예외에 접근할 수 있습니다.

```crystal
begin
  raise MyException.new("앗!")
rescue ex : MyException
  puts "#{ex.message} MyException이다!"
end

# 출력: 앗! MyException이다!
```

`rescue` 절을 여러 개 적을 수도 있습니다.

```crystal
begin
  # ...
rescue ex1 : MyException
  # MyException만
rescue ex2 : MyOtherException
  # MyOtherException만
rescue
  # 다른 종류의 예외들
end
```

공용체 타입을 특정하여 한 번에 여러 타입의 예외를 구조할 수도 있습니다.

```crystal
begin
  # ...
rescue ex : MyException | MyOtherException
  # MyException 혹은 MyOtherException만
rescue
  # 다른 종류의 예외들
end
```

## ensure

`ensure` 절은 예외 여부와 상관 없이 `begin ... end` 또는 `begin ... rescue ... end` 표현식의 마지막에 실행됩니다.

```crystal
begin
  something_dangerous
ensure
  puts "정리 중..."
end

# something_dangerous을 호출한 후
# 예외가 일어났는지 여부는 따지지 않고 "정리 중..."을 출력합니다.
```

또는,

```crystal
begin
  something_dangerous
rescue
  # ...
ensure
  # 이 코드를 실행
end
```

`ensure` 절은 대개 자원을 해제하거나 메모리 등을 정리하는 데 쓰입니다.

## else

`else` 절은 예외가 구조되지 않은 경우에만 실행됩니다.

```crystal
begin
  something_dangerous
rescue
  # 예외가 일어나면 실행
else
  # 예외가 일어나지 않으면 실행
end
```

`else` 절은 `rescue` 절이 하나라도 있는 경우에만 올 수 있습니다.

## 짧은 문법

짧은 형태의 문법으로 예외를 다를 수도 있습니다. 메서드 정의가 암묵적으로 `begin ... end` 표현식이라고 가정하고, `rescue`, `ensure`, `else` 절을 넣어봅시다.

```crystal
def some_method
  something_dangerous
rescue
  # 예외가 일어나면 실행
end

# 다음과 동일
def some_method
  begin
    something_dangerous
  rescue
    # 예외가 일어나면 실행
  end
end
```

`ensure`은 다음과 같이 쓸 수 있습니다.

```crystal
def some_method
  something_dangerous
ensure
  # 항상 이 코드를 실행
end

# The above is the same as:
def some_method
  begin
    something_dangerous
  ensure
    # 항상 이 코드를 실행
  end
end
```

## 타입 추론

`begin` 안에서 선언된 변수는 `rescue`와 `ensure` 안에서는 `Nil` 타입으로 간주됩니다.

```crystal
begin
  a = something_dangerous_that_returns_Int32
ensure
  puts a + 1 # 오류, Nil에 정의된 메서드 '+' 없음
end
```

위의 경우는 `something_dangerous_that_returns_Int32`가 오류를 일으키지 않아도, `a`에 값을 할당한 후 예외를 일으킬지 모르는 메서드를 실행한 후에도 해당됩니다.

```crystal
begin
  a = 1
  something_dangerous
ensure
  puts a + 1 # 오류, Nil에 정의된 메서드 '+' 없음
end
```

`a`에 값을 할당한다는 사실이 명백할지라도 컴파일러는 `a`가 초기화된 적이 없었을 것이라고 생각합니다. 이 과정이 미래에 더 매끄러워질 수 있겠지만, 지금은 예외 처리 과정을 최소화하고 코드의 의도록 명확하게 만드는 데 집중하는 것이 낫습니다.

```crystal
# `a`는 예외 처리 코드에 있을 필요가 없으므로
# 아까의 코드보다 더 나음
a = 1
begin
  something_dangerous
ensure
  puts a + 1 # works
end
```

## 오류를 다루는 다른 방법들

예외는 오류 처리의 한 방법이지만 유일한 방법은 아닙니다. 예외를 일으키면 메모리가 소모되며, 예외 처리 코드를 실행하는 것은 일반적으로 느린 과정입니다.

표준 라이브러리에는 한 가지 동작을 위해 다른 방법을 사용하는 메서드 짝이 몇 가지 있습니다. 하나는 예외를 일으키고, 하나는 `nil`을 반환합니다.

```crystal
array = [1, 2, 3]
array[4]  # IndexError를 일으킴
array[4]? # 인덱스 초과로 nil을 반환
```

물음표가 달린 메서드는 예외를 일으키는 대신 `nil`을 반환하도록 하는 것이 일반적인 관습입니다. 사용자는 이로써 예외를 처리할지 `nil`을 처리할지 선택할 수 있습니다. 하지만 오류 처리를 분리할 수 있기 때문에 예외를 발생시키는 것이 더욱 바람직하므로, 이는 모든 메서드에 적용할 수 있는 방법은 아닙니다.
