# case

`case`는 패턴 매칭과 비슷한 방식으로 기능하는 제어 표현입니다. 표현을 조금만 바꾸면 if-else-if 연쇄를 더욱 강력한 구조로 작성할 수 있습니다.

가장 기본적인 형태는, 값을 다른 값에 맞추어 보는 것입니다.

```crystal
case exp
when value1, value2
  do_something
when value3
  do_something_else
else
  do_another_thing
end

# 다음과 동일
tmp = exp
if value1 === tmp || value2 === tmp
  do_something
elsif value3 === tmp
  do_something_else
else
  do_another_thing
end
```

`case`의 값과 표현식을 비교할 때 `===`가 쓰인다는 사실을 기억해 둘 필요가 있습니다.

`when`의 표현식이 타입이라면 `is_a?`가 사용됩니다. 추가로 case 표현식이 변수이거나 변수 할당이라면 해당 변수의 타입은 그에 따라 한정됩니다.

```crystal
case var
when String
  # var : String
  do_something
when Int32
  # var : Int32
  do_something_else
else
  # 이때 var은 String도 Int32도 아님
  do_another_thing
end

# 다음과 동일
if var.is_a?(String)
  do_something
elsif var.is_a?(Int32)
  do_something_else
else
  do_another_thing
end
```

묵시적 객체 문법을 통해 `when` 표현식에서 `case`의 표현식의 메서드를 호출할 수도 있습니다.

```crystal
case num
when .even?
  do_something
when .odd?
  do_something_else
end

# 다음과 동일
tmp = num
if tmp.even?
  do_something
elsif tmp.odd?
  do_something_else
end
```

끝으로, `case`의 값을 생략할 수도 있습니다.

```crystal
case
when cond1, cond2
  do_something
when cond3
  do_something_else
end

# 다음과 동일
if cond1 || cond2
  do_something
elsif cond3
  do_something_else
end
```

이로써 가독성을 확보할 수 있는 경우가 있습니다.

## 튜플 리터럴

case 표현식이 튜플 리터럴이며 조건 또한 튜플 리터럴일 경우 몇 가지 의미론적 차이가 있습니다.

### 크기는 반드시 동일

```crystal
case {value1, value2}
when {0, 0} # 요소 2개이므로 문제 없음
  # ...
when {1, 2, 3} # 절대로 해당되지 않기 때문에 컴파일 에러
  # ...
end
```

### 밑줄 사용 가능

```crystal
case {value1, value2}
when {0, _}
  # 0 === value1일 때에 해당되며 value2는 테스트하지 않음
when {_, 0}
  # 0 === value2일 때에 해당되며 value1는 테스트하지 않음
end
```

### 묵시적 객체 문법

```crystal
case {value1, value2}
when {.even?, .odd?}
  # value1.even? && value2.odd?일 때에 해당
end
```

### 타입과의 비교는 is_a? 검사

```crystal
case {value1, value2}
when {String, Int32}
  # value1.is_a?(String) && value2.is_a?(Int32)일 때에 해당
  # 컴파일러는 value1을 String으로 value2를 Int32로 추론
end
```
