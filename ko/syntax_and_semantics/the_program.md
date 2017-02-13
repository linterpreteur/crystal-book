# 프로그램

프로그램은 타입, 메서드, 파일 단위의 지역 변수가 선언되는 전역 객체입니다.

```crystal
# 프로그램에 메서드 선언
def add(x, y)
  x + y
end

# 프로그램의 add 메서드 호출
add(1, 2) #=> 3
```

메서드의 값은 마지막 표현식의 값과 같습니다. 따라서 `return`은 필요하지 않지만, 명시할 수는 있습니다.

```crystal
def even?(num)
  if num % 2 == 0
    return true
  end

  return false
end
```

`add(1, 2)`처럼 리시버 없이 메서드를 호출했을 때, 현재 타입이나 상위 타입에서 그 메서드를 찾지 못했다면 프로그램에서 해당 메서드를 찾습니다.

```crystal
def add(x, y)
  x + y
end

class Foo
  def bar
    # 프로그램의 add 메서드 호출
    add(1, 2)

    # Foo의 baz 메서드 호출
    baz(1, 2)
  end

  def baz(x, y)
    x * y
  end
end
```

현재 타입에서 같은 이름을 갖는 메서드를 정의하고 있더라도 프로그램의 메서드를 호출하고 싶은 경우라면 호출 앞에 `::`를 붙입니다.

```crystal
def baz(x, y)
  x + y
end

class Foo
  def bar
    baz(4, 2) #=> 2
    ::baz(4, 2) #=> 6
  end

  def baz(x, y)
    x - y
  end
end
```

프로그램에 선언된 변수는 메서드 안에서는 사용할 수 없습니다.

```crystal
x = 1

def add(y)
  x + y # 오류: 지역 변수 또는 메서드 'x'는 정의되지 않음
end

add(2)
```

메서드 호출에서 괄호를 생략할 수도 있습니다.

```crystal
add 1, 2 # add(1, 2)와 동일
```

## 메인 코드

프로그램을 컴파일하고 실행할 때 실행되는 메인 코드는, 별도의 "메인" 메서드 없이 소스 파일에 바로 쓸 수 있습니다.

```crystal
# "안녕 크리스탈!"을 출력하는 프로그램
puts "안녕 크리스탈!"
```

메인 코드는 타입 선언 안에도 올 수 있습니다.

```crystal
# "Hello"를 출력하는 프로그램
class Hello
  # 이 때 'self'는 Hello 클래스
  puts self
end
```
