# if

`if`는 조건이 *참인* 값이라면 `then` 분기를 평가하며, *거짓인* 값이며 `else` 분기가 존재한다면 `else` 분기를 평가합니다.

```crystal
a = 1
if a > 0
a = 10
end
a #=> 10

b = 1
if b > 2
b = 10
else
b = 20
end
b #=> 20
```

`elsif`를 이용해 if-else-if의 연쇄를 작성할 수 있습니다.

```crystal
if some_condition
do_something
elsif some_other_condition
do_something_else
else
do_that
end
```

`if` 분기 다음에 변수의 타입은 각 분기에서의 표현식의 타입에 따라 결정됩니다.

```crystal
a = 1
if some_condition
a = "hello"
else
a = true
end
# a : String | Bool

b = 1
if some_condition
b = "hello"
end
# b : Int32 | String

if some_condition
c = 1
else
c = "hello"
end
# c : Int32 | String

if some_condition
d = 1
end
# d : Int32 | Nil
```

변수가 한 분기에서는 선언되지만 다른 분기에서는 선언되지 않는 경우, `if`문 다음에 그 변수는 `Nil` 타입 또한 포함합니다.

`if` 분기 안에서 변수의 타입은 그 분기에서 주어진 값의 타입이거나, 분기에서 다시 할당되지 않았다면 분기 이전에 주어진 값의 타입입니다.

```crystal
a = 1
if some_condition
a = "hello"
# a : String
a.size
end
# a : String | Int32
```

즉, 변수의 타입은 가장 마지막에 할당 받은 표현식의 타입입니다.

한 분기가 `return`, `next`, `break`, `raise` 등에 의해 `if`문 이후로 진행할 수 없는 경우 그 분기의 값은 `if` 이후에 고려되지 않습니다.

```crystal
if some_condition
e = 1
else
e = "hello"
# e : String
return
end
# e : Int32
```
