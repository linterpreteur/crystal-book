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

변수가 각각 무슨 종류인지는 추후 설명하겠습니다.

`=` 문자를 포함하는 문법적 편의 사항이 몇 가지 있습니다.

```crystal
local += 1 # local = local + 1와 동일

# 위와 같은 표기는 다음 연산자도 사용할 수 있습니다.
# +, -, *, /, %, |, &, ^, **, <<, >>

local ||= 1 # local || (local = 1)와 동일
local &&= 1 # local && (local = 1)와 동일
```

`=`로 끝나는 메서드 호출에 대한 문법적 편의 사항이 있습니다.

```crystal
# 세터
person.name=("철수")

# 다음과 동일
person.name = "철수"

# 인덱스 할당
objects.[]=(2, 3)

# 다음과 동일
objects[2] = 3

# 할당 관련은 아니지만 문법적 편의 사항
objects.[](2, 3)

# 다음과 동일
objects[2, 3]
```

`=` 연산자에 관련된 문법적 편의 사항은 세터와 인덱서에서도 쓸 수 있습니다. `||`와 `&&`가 `[]?` 메서드를 통해 키의 존재 여부를 확인한다는 사실을 알아둡시다.

```crystal
person.age += 1 # person.age = person.age + 1와 동일

person.name ||= "철수" # person.name || (person.name = "철수")와 동일
person.name &&= "철수" # person.name && (person.name = "철수")와 동일

objects[1] += 2 # objects[1] = objects[1] + 2와 동일

objects[1] ||= 2 # objects[1]? || (objects[1] = 2)와 동일
objects[1] &&= 2 # objects[1]? && (objects[1] = 2)와 동일
```
