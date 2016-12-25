# 할당

변수 할당은 등호(`=`) 문자를 이용합니다.

```crystal
# 지역 변수에 할당
local = 1

# 인스턴스 변수에 할당
@instance = 2

# 클래스 변수에 할당
@@class = 3
```

변수가 각각 무슨 종류인지는 후에 설명이 있습니다.

`=` 문자를 포함하는 문법적 설탕이 몇 가지 있습니다.

```crystal
local += 1 # local = local + 1와 동일

# 위 방법을 다음 연산자와 함께 사용할 수 있습니다.
# +, -, *, /, %, |, &, ^, **, <<, >>

local ||= 1 # local || (local = 1)와 동일
local &&= 1 # local && (local = 1)와 동일
```

`=`로 끝나는 메서드 호출에 대한 문법적 설탕이 있습니다.

```crystal
# 세터
person.name=("John")

# 다음과 동일
person.name = "John"

# 인덱스 할당
objects.[]=(2, 3)

# 다음과 동일
objects[2] = 3

# 할당 관련은 아니지만 문법적 설탕
objects.[](2, 3)

# 다음과 동일
objects[2, 3]
```

`=` 연산자에 관련된 문법적 설탕은 세터와 인덱서에서도 쓸 수 있습니다. `||`와 `&&`가 `[]?` 메서드를 통해 키의 존재 여부를 확인한다는 사실에 주목하시길 바랍니다.

```crystal
person.age += 1 # person.age = person.age + 1와 동일

person.name ||= "John" # person.name || (person.name = "John")와 동일
person.name &&= "John" # person.name && (person.name = "John")와 동일

objects[1] += 2 # objects[1] = objects[1] + 2와 동일

objects[1] ||= 2 # objects[1]? || (objects[1] = 2)와 동일
objects[1] &&= 2 # objects[1]? && (objects[1] = 2)와 동일
```
