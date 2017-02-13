# 블락과 프록

메서드는 코드 블락을 받아서 `yield` 에약어로 그 블락을 실행할 수 있습니다.

```crystal
def twice
  yield
  yield
end

twice do
  puts "안녕!"
end
```

이 프로그램은 각 `yield`마다 한 번, 총 두 번 "안녕!"을 출력합니다.

내부에서 `yield`를 사용하면 컴파일러는 메서드가 블락을 받는다는 것을 알 수 있습니다. 더 명백하게 나타내려면 마지막 인자 이름 앞에 `&` 기호를 붙여서 더미 인자를 선언합니다.

```crystal
def twice(&block)
  yield
  yield
end
```

메서드를 호출할 때 블락을 넘기려면 `do ... end` 혹은 `{ ... }`를 사용합니다. 둘은 같은 의미입니다.

```crystal
twice() do
  puts "안녕!"
end

twice do
  puts "안녕!"
end

twice { puts "안녕!" }
```

`do ... end`와 `{ ... }`의 차이점은 `do ... end`는 왼쪽부터 호출을 구분하고 `{ ... }`는 오른쪽부터 호출을 구분한다는 것입니다.

```crystal
foo bar do
  something
end

# 다음과 같은 의미
foo(bar) do
  something
end

foo bar { something }

# 다음과 같은 의미

foo(bar { something })
```

`do ... end`를 이용함으로써 평범한 영어 같은 도메인 특정 언어(DSL)를 만들 수 있습니다.

```crystal
open file "foo.cr" do
  something
end

# 다음과 동일
open(file("foo.cr")) do
end
```

동작이 다음과 같았다면 보기 좋은 DSL을 만드는 데 한층 애로사항이 꽃피었을 것입니다.

```crystal
open(file("foo.cr") do
end)
```

## 오버로드

[오버로딩](overloading.html) 항목에서 보았던 것처럼, 블락을 받는 메서드와 블락을 받지 않는 메서드는 서로 다른 오버로드로 취급됩니다.

## 블락에 인자를 넘기기

`yield` 표현식은 메서드 호출과 같이 인자를 받을 수 있습니다.

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  puts "#{i} 받았다"
end
```

이 코드는 "1 받았다"와 "2 받았다"를 출력합니다.

중괄호 표기 또한 쓸 수 있습니다.

```crystal
twice { |i| puts "#{i} 받았다" }
```

여러 값을 `yield`에 넘길 수도 있습니다.

```crystal
def many
  yield 1, 2, 3
end

many do |x, y, z|
  puts x + y + z
end

# 출력: 6
```

블락에서 정의한 인자보다 많은 인자를 넘길 수 있습니다.

```crystal
def many
  yield 1, 2, 3
end

many do |x, y|
  puts x + y
end

# 출력: 3
```

넘겨진 인자보다 블락의 인자가 더 많다면 오류가 발생합니다.

```crystal
def twice
  yield
  yield
end

twice do |i| # 오류: 블락 인자 너무 많음
end
```

각 블락 인자는 해당 위치에 넘겨진 인자의 타입을 갖습니다.

```crystal
def some
  yield 1, 'a'
  yield true, "안녕"
  yield 2, nil
end

some do |first, second|
  # first는 Int32 | Bool
  # second는 Char | String | Nil
end
```

마지막 `yield` 표현식에는 두 번째 인자가 없었기 때문에 블락 인자 `second`의 타입은 `Nil` 또한 포함합니다.

## 인자가 하나인 경우의 짧은 문법

블락이 인자 하나를 받아 그 인자의 메서드를 호출하는 경우 짧은 문법으로 블락을 정의할 수 있습니다.

```crystal
method do |argument|
  argument.some_method
end
```

이를 다음과 같이 쓸 수 있습니다.

```crystal
method &.some_method
```

다음도 동일합니다.

```crystal
method(&.some_method)
```

이는 단순히 문법적인 편의 사항으로 성능에는 영향을 미치지 않습니다.

`some_method`에 인자를 넘길 수 도 있습니다.

```crystal
method &.some_method(arg1, arg2)
```

연산자도 마찬가지입니다.

```crystal
method &.+(2)
method &.[index]
```

## yield 표현식의 값

`yield` 표현식도 값을 가집니다. 블락의 마지막 값이 바로 `yield`의 값입니다.

```crystal
def twice
  v1 = yield 1
  puts v1

  v2 = yield 2
  puts v2
end

twice do |i|
  i + 1
end
```

"2"와 "3"이 출력됩니다.

`yield` 표현식의 값은 값을 변환하거나 여과할 때 유용합니다. [Enumerable#map](http://crystal-lang.org/api/Enumerable.html#map%28%26block%20%3A%20T%20-%3E%20U%29-instance-method)과 [Enumerable#select](http://crystal-lang.org/api/Enumerable.html#select%28%26block%20%3A%20T%20-%3E%20%29-instance-method)이 좋은 예입니다.

```crystal
ary = [1, 2, 3]
ary.map { |x| x + 1 }         #=> [2, 3, 4]
ary.select { |x| x % 2 == 1 } #=> [1, 3]
```

변환 메서드를 다음과 같이 정의할 수 있습니다.

```crystal
def transform(value)
  yield value
end

transform(1) { |x| x + 1 } #=> 2
```

`transform` 메서드의 마지막 값은 `yield`이며, 그 값은 블락의 마지막 값이 되므로 이 표현식의 값은 `2`가 됩니다.

## break

블락에서 `break` 표현식을 사용하면 메서드의 실행을 종료합니다.

```crystal
def thrice
  puts "1 이전"
  yield 1
  puts "2 이전"
  yield 2
  puts "3 이전"
  yield 3
  puts "3 이후"
end

thrice do |i|
  if i == 2
    break
  end
end
```

"1 이전"과 "2 이전"이 출력됩니다. `thrice` 메서드에 넘겨진 블락에서 `break`을 실행하기 때문에 `puts "3 이전"` 표현식은 실행되지 않습니다.

`break`도 인자를 받을 수 있습니다. 이 값이 메서드의 반환 값이 됩니다.

```crystal
def twice
  yield 1
  yield 2
end

twice { |i| i + 1 } #=> 3
twice { |i| break "안녕" } #=> "안녕"
```

`twice` 메서드의 마지막 값은 `yield`이며, 그 값은 블락의 값이므로 첫 번째 호출의 값은 3입니다. `break`가 실행되므로 두 번째 호출의 값은 "안녕"입니다.

조건부로 break를 실행한다면 호출의 반환 타입은 블락의 값과 `break`에 넘겨진 값들의 타입 공용체입니다.

```crystal
value = twice do |i|
  if i == 1
    break "안녕"
  end
  i + 1
end
value #:: Int32 | String
```

`break`가 인자를 여러 개 받으면 그 인자들은 자동으로 [Tuple](http://crystal-lang.org/api/Tuple.html)로 변환됩니다.

```crystal
values = twice { break 1, 2 }
values #=> {1, 2}
```

`break`가 인자를 받지 않는 것은 `nil` 인자를 받는 것과 동일합니다.

```crystal
value = twice { break }
value #=> nil
```

## next

블락에서의 `next` 표현식은 (메서드가 아니라) 블락의 실행을 종료합니다.

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  if i == 1
    puts "1 생략"
    next
  end

  puts "#{i}"
end

# 출력:
# 1 생략
# 2
```

`next` 표현식이 인자를 받으면 그 값이 블락을 호출한 `yield` 표현식의 값이 됩니다.

```crystal
def twice
  v1 = yield 1
  puts v1

  v2 = yield 2
  puts v2
end

twice do |i|
  if i == 1
    next 10
  end

  i + 1
end

# 출력
# 10
# 3
```

`next`가 인자를 여러 개 받으면, 그 인자들은 자동으로 [Tuple](http://crystal-lang.org/api/Tuple.html)로 변환됩니다. 인자를 받지 않는 것은 `nil` 인자를 받는 것과 동일합니다.

## with ... yield

`yield` 표현식에 `with` 예약어를 이용하여 블락에서 리시버로 어떤 객체를 사용할지를 변경할 수 있습니다.

```crystal
class Foo
  def one
    1
  end

  def yield_with_self
    with self yield
  end

  def yield_normally
    yield
  end
end

def one
  "하나"
end

Foo.new.yield_with_self { one } # => 1
Foo.new.yield_normally { one }  # => "하나"
```

## 블락 인자 쪼개기

블락 인자는 괄호 안에서 받은 인자를 쪼갤 수 있습니다.

```crystal
array = [{1, "하나"}, {2, "둘"}]
array.each do |(number, word)|
  puts "#{number}: #{word}"
end
```

이는 다음과 동일한 코드를 작성하는 문법적 편의 사항입니다.

```crystal
array = [{1, "하나"}, {2, "둘"}]
array.each do |arg|
  number = arg[0]
  word = arg[1]
  puts "#{number}: #{word}"
end
```

이는 정수를 받는 `[]` 메서드가 있는 타입이라면 블락 인자에서 쪼갤 수 있다는 뜻입니다.

## 성능

`yield`를 사용할 때 블락은 **항상** 인라인됩니다. 클로저도, 호출도, 함수 포인터도 없습니다.

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  puts "#{i} 받았다"
end
```

다음을 쓰는 것과 완전히 동일합니다.

```crystal
i = 1
puts "#{i} 받았다"
i = 2
puts "#{i} 받았다"
```

한 예로, 표준 라이브러리에 선언된 정수 타입의 메서드 `times`가 있습니다.

```crystal
3.times do |i|
  puts i
end
```

꽤 멋있어 보이는데 반복이 과연 C만큼 빠를까요? 네, 그렇습니다.

`Int#times`의 정의는 다음과 같습니다.

```crystal
struct Int
  def times
    i = 0
    while i < self
      yield i
      i += 1
    end
  end
end
```

포획되지 않은 블락은 항상 인라인되기 때문에 위의 메서드 호출은 다음과 **완전히 동일**합니다.

```crystal
i = 0
while i < 3
  puts i
  i += 1
end
```

재사용성이나 가독성을 위해 블락을 사용하는 것을 겁낼 필요가 없습니다. 실행되는 코드의 성능에는 아무 영향도 없습니다.
