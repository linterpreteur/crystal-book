# 매크로

매크로는 컴파일 시간에 AST 노드를 받아 코드를 생성하는 메서드입니다.

```crystal
macro define_method(name, content)
  def {{name}}
    {{content}}
  end
end

# 다음을 생성
#
#     def foo
#       1
#     end
define_method foo, 1

foo #=> 1
```

매크로의 정의부는 일반적인 크리스탈 코드와 유사합니다. 차이점이라면 AST 노드를 조작하는 문법이 추가되어 있다는 것입니다. 생성된 코드는 반드시 유효한 크리스탈 코드여야 합니다. 즉 `end` 없는 `def`나, `when` 표현식 없는 `case` 등은 생성할 수 없습니다.

## 범위

최상위에 선언된 매크로는 어디서든 사용할 수 있습니다. 최상위 매크로가 `private`으로 표시된다면 그 파일에서만 사용할 수 있습니다.

클래스나 모듈에 매크로를 선언할 수도 있으며, 그 경우 해당 모듈이나 클래스에서만 사용할 수 있습니다. 매크로는 또한 상위 클래스와 포함한 모듈 등과 같은 상속 연쇄를 따릅니다.

예를 들어, `with ... yield`를 호출하여 리시버 객체를 받은 블락은 해당 객체의 상속 연쇄에 정의되어 있는 매크로에 접근할 수 있습니다.

```crystal
class Foo
  macro emphasize(value)
    "***#{ {{value}} }***"
  end

  def yield_with_self
    with self yield
  end
end

Foo.new.yield_with_self { emphasize(10) } #=> "***10***"
```

클래스와 모듈에 정의된 매크로 또한 바깥에서 호출할 수 있습니다.

```crystal
class Foo
  macro emphasize(value)
    "***#{ {{value}} }***"
  end
end

Foo.emphasize(10) # => "***10***"
```

## 보간

위에서 보았듯 `{{...}}`를 이용하여 AST 노드를 붙여넣거나 보간합니다.

해당 노드는 그대로 붙여넣어진다는 사실에 주의해야 합니다. 앞서 본 예시에서 심벌을 넘긴다면 생성된 코드는 유효하지 않게 됩니다.

```crystal
# 다음을 생성
#
#     def :foo
#       1
#     end
define_method :foo, 1
```

`:foo`가 매크로에 넘겨졌기 때문에 그대로 보간의 결과가 됩니다. 이 경우처럼 식별자가 필요하다면 `ASTNode#id` 메서드를 사용하면 됩니다.

## 매크로 호출

컴파일 시간에는 AST 노드에 **미리 정해진 종류의** 메서드만을 호출할 수 있습니다. 가상의 [Crystal::Macros](http://crystal-lang.org/api/Crystal/Macros.html) 모듈에 정리되어 있습니다.

예를 들어, 위의 코드에서 `ASTNode#id`를 호출하여 문제를 해결할 수 있습니다.

```crystal
macro define_method(name, content)
  def {{name.id}}
    {{content}}
  end
end

# 올바르게 다음을 생성
#
#     def foo
#       1
#     end
define_method :foo, 1
```

## 조건문

`{% if condition %}` ... `{% end %}` 구문을 통해 조건적으로 코드를 생성할 수 있습니다.

```crystal
macro define_method(name, content)
  def {{name}}
    {% if content == 1 %}
      "one"
    {% else %}
      {{content}}
    {% end %}
  end
end

define_method foo, 1
define_method bar, 2

foo #=> one
bar #=> 2
```

일반 코드와 비슷하게, `Nop`, `NilLiteral`과 거짓인 `BoolLiteral`이 *거짓인* 값으로 취급되며, 나머지는 모두 참인 값으로 취급됩니다.

매크로 조건문은 매크로 정의가 아닐 때에도 사용할 수 있습니다.

```crystal
{% if env("TEST") %}
  puts "지금은 테스트 모드"
{% end %}
```

### 반복문
다음과 같이 `ArrayLiteral`의 요소를 반복할 수 있습니다.

```crystal
macro define_dummy_methods(names)
  {% for name, index in names %}
    def {{name.id}}
      {{index}}
    end
  {% end %}
end

define_dummy_methods [foo, bar, baz]

foo #=> 0
bar #=> 1
baz #=> 2
```

위의 경우 `index` 변수를 쓰지 않을 수도 있습니다.

`HashLiteral`의 요소를 반복하는 것은 다음과 같습니다.

```crystal
macro define_dummy_methods(hash)
  {% for key, value in hash %}
    def {{key.id}}
      {{value}}
    end
  {% end %}
end
define_dummy_methods({foo: 10, bar: 20})
foo #=> 10
bar #=> 20
```

매크로 반복문은 매크로 정의가 아닐 때에도 사용할 수 있습니다.

```crystal
{% for name, index in ["foo", "bar", "baz"] %}
  def {{name.id}}
    {{index}}
  end
{% end %}

foo #=> 0
bar #=> 1
baz #=> 2
```

## 가변 인자와 쪼개기

매크로는 가변 인자를 받을 수 있습니다.

```crystal
macro define_dummy_methods(*names)
  {% for name, index in names %}
    def {{name.id}}
      {{index}}
    end
  {% end %}
end

define_dummy_methods foo, bar, baz

foo #=> 0
bar #=> 1
baz #=> 2
```

인자는 `ArrayLiteral`에 담겨 매크로에 전달됩니다.

추가로 `ArrayLiteral`을 보간할 때 `*`를 사용한다면, 쉼표로 쪼개어 인자를 합치게 될 것입니다.

```crystal
macro println(*values)
   print {{*values}}, '\n'
end

println 1, 2, 3 # outputs 123\n
```

### 타입 정보

매크로를 호출할 때 현재 객체의 타입을 특수한 인스턴스 변스 `@type`을 통해 접근할 수 있습니다. 이 변수의 타입은 `TypeNode`이며, 컴파일 시간에 타입 정보를 읽을 수 있습니다.

매크로가 클래스 메서드에서 호출될 때라고 해도 `@type`은 항상 *인스턴스* 타입입니다.

### 상수

매크로에서 상수를 읽을 수 있습니다.

```crystal
VALUES = [1, 2, 3]

{% for value in VALUES %}
  puts {{value}}
{% end %}
```

상수가 타입 표기일 경우, 그 값은 `TypeNode`입니다.
