# 다중 할당

쉼표(`,`)를 이용하여 동시에 다수의 변수를 선언하고 할당할 수 있습니다.

```crystal
name, age = "Crystal", 1

# 다음과 동일
temp1 = "Crystal"
temp2 = 1
name = temp1
age = temp2
```

위에서 볼 수 있듯 쉼표로 구분된 표현식은 임시 변수에 할당되기 때문에, 한 줄로 변수 내용을 서로 바꿀 수도 있습니다.

```crystal
a = 1
b = 2
a, b = b, a
a #=> 2
b #=> 1
```

등호의 우측에 표현식을 하나만 있다면 인덱스가 있는 타입으로 취급되어 다음 문법적 설탕이 적용됩니다.

```crystal
name, age, source = "Crystal,1,github".split(",")

# 다음과 동일
temp = "Crystal,1,github".split(",")
name = temp[0]
age = temp[1]
source = temp[2]
```

등호의 좌측에 변수가 하나만 있다면 우측은 배열로 취급됩니다.

```crystal
names = "John", "Peter", "Jack"

# 다음과 동일
names = ["John", "Peter", "Jack"]
```

다중 할당은 `=`로 끝나는 메서드와 함께 이용할 수도 있습니다.

```crystal
person.name, person.age = "John", 32

# 다음과 동일
temp1 = "John"
temp2 = 32
person.name = temp1
person.age = temp2
```

그리고 인덱서(`[]=`)와 함께 이용할 수도 있습니다.

```crystal
objects[1], objects[2] = 3, 4

# 다음과 동일
temp1 = 3
temp2 = 4
objects[1] = temp1
objects[2] = temp2
```
