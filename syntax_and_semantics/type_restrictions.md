# 타입 제약

타입 제약은 메서드가 받을 수 있는 인자를 제한하는 타입 표기입니다.

```crystal
def add(x : Number, y : Number)
  x + y
end

# 정상 작동
add 1, 2 # 정상 작동

# 오류: Bool, Bool 타입에 맞는 'add' 오버로드 없음
add true, false
```

타입 제약 없이 `add`를 정의했다고 해도, 컴파일 시간 오류가 발생했을 것입니다.

```crystal
def add(x, y)
  x + y
end

add true, false
```

다음과 같은 컴파일 오류가 발생합니다.

```
foo.cr:6에서 오류: 'add(Bool, Bool)' 인스턴스화 중

add true, false
^~~

foo.cr:2에서: Bool에 '+' 메서드 정의되지 않음

  x + y
    ^
```

이는 `add`가 호출될 때 인자의 타입으로 인스턴스화되기 때문입니다. 다른 타입의 조합을 넘길 때마다 다른 인스턴스가 생성됩니다.

유일한 차이는 첫 번째 메시지가 조금 더 명확하다는 것입니다. 하지만 어쨌든 컴파일 시간 오류가 발생한다는 점에서 두 경우 모두 안전하다고 할 수 있습니다. 따라서 보통 오버로드를 정의하는 경우가 아니라면 타입 제약을 명시하지 않는 쪽이 낫습니다. 이로써 더욱 일반적이고 재사용성 있는 코드를 쓸 수 있습니다. 예를 들어 `Number`가 아니지만 `+` 메서드가 있는 클래스를 정의한다면, 타입 제약이 없는 `add` 메서드를 사용할 수는 있어도 제약이 있는 메서드를 사용할 수는 없을 것입니다.

```crystal
# + 메서드가 있지만 Number가 아닌 클래스
class Six
  def +(other)
    6 + other
  end
end

# 타입 제약이 있는 add 메서드
def add(x, y)
  x + y
end

# 정상 작동
add Six.new, 10

# 타입 제약이 있는 add 메서드
def restricted_add(x : Number, y : Number)
  x + y
end

# 오류: Six, Int32 타입에 맞는 'restricted_add' 오버로드 없음
restricted_add Six.new, 10
```

[타입 문법](type_grammar.html)에서 타입 제약에 쓰인 표기에 대해 알아볼 수 있습니다.

## self 제약

`self`는 특수한 타입 제약입니다.

```crystal
class Person
  def ==(other : self)
    other.name == name
  end

  def ==(other)
    false
  end
end

cholsu = Person.new "철수"
another_cholsu = Person.new "철수"
minsu = Person.new "민수"

cholsu == another_cholsu #=> true
cholsu == minsu #=> false (이름이 다름)
cholsu == 1 #=> false (1은 Person이 아님)
```

위의 예시에서 `self`는 `Person`을 쓰는 것과 같습니다. 그러나 `self`는 해당 메서드가 사용될 타입과 자동으로 같아지기 때문에, 모듈에서 쓰이는 경우라면 훨씬 더 유용합니다.

부연하자면 `Person`은 `Reference`를 상속하기 때문에 `Reference`에 이미 정의되어 있는 두 번째 `==` 정의는 굳이 필요하지 않습니다.

클래스 메서드에서조차 `self`는 항상 인스턴스 타입을 나타낼 것입니다.

```crystal
class Person
  def self.compare(p1 : self, p2 : self)
    p1.name == p2.name
  end
end

cholsu = Person.new "철수"
minsu = Person.new "민수"

Person.compare(cholsu, minsu) # 정상 작동
```

`self.class`으로 Person 타입을 나타낼 수 있습니다. 다음 장에서 타입 제약의 `.class` 접미사에 대해 알아봅니다.

## 제약으로서의 클래스

타입 제약에 예컨대 `Int32`를 사용한다면 `Int32`의 인스턴스만을 받는 메서드를 만들 수 있습니다.

```crystal
def foo(x : Int32)
end

foo 1       # 정상 작동
foo "안녕" # 오류
```

메서드가 Int32의 인스턴스가 아니라 타입 자체를 받도록 하려면, `.class`를 이용합니다.

```crystal
def foo(x : Int32.class)
end

foo Int32  # 정상 작동
foo String # 오류
```

이는 인스턴스가 아닌 타입에 기초한 오버로드를 작성할 때 유용합니다.

```crystal
def foo(x : Int32.class)
  puts "Int32지롱"
end

def foo(x : String.class)
  puts "String이네"
end

foo Int32  # "Int32지롱" 출력
foo String # "String이네" 출력
```

## 스플랫의 타입 제약

스플랫의 타입 제약을 특정할 수도 있습니다.

```crystal
def foo(*args : Int32)
end

def foo(*args : String)
end

foo 1, 2, 3       # 정상 작동, 첫 번째 오버로드 호출
foo "a", "b", "c" # 정상 작동, 두 번째 오버로드 호출
foo 1, 2, "hello" # 오류
foo()             # 오류
```

타입을 특정할 때, 튜플의 모든 원소가 그 타입에 맞아야 합니다. 또한 빈 튜플은 어떤 경우에도 해당되지 않습니다. 빈 튜플의 경우를 고려하려면 다음 오버로드를 추가합니다.

```crystal
def foo
  # 튜플이 비어 있는 경우
end
```

타입과 상관 없이 하나 이상의 원소에 해당되는 경우는 `Object`를 제약으로 사용합니다.

```crystal
def foo(*args : Object)
end

foo() # 오류
foo(1) # 정상 작동
foo(1, "x") # 정상 작동
```

## 자유 변수

`forall`을 이용해 인자의 타입을 받는 타입 제약을 만들 수 있습니다.

```crystal
def foo(x : T) forall T
  T
end

foo(1)       #=> Int32
foo("안녕") #=> String
```

즉, `T`는 메서드를 인스턴스화 하는 데 사용된 타입을 나타냅니다.

자유 변수로 제너릭 타입의 타입 인자를 빼낼 수도 있습니다.

```crystal
def foo(x : Array(T)) forall T
  T
end

foo([1, 2])   #=> Int32
foo([1, "a"]) #=> (Int32 | String)
```

인스턴스 대신 타입을 받는 메서드를 만들기 위해서는 자유 변수에 `.class`를 붙입니다.

```crystal
def foo(x : T.class) forall T
  Array(T)
end

foo(Int32)  #=> Array(Int32)
foo(String) #=> Array(String)
```

자유 변수를 다수 이용해 여러 인자에 타입을 맞추어볼 수도 있습니다.

```crystal
def push(element : T, array : Array(T)) forall T
  array << element
end

push(4, [1, 2, 3]) # 정상 작동
push("oops", [1, 2, 3]) # 오류
```

