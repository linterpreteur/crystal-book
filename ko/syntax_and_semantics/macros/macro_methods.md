# 매크로 메서드

매크로 메서드를 이용해 하위 타입에 따라 다르게 작동하는 상위 클래스의 메서드를 정의할 수 있습니다.

`def`는 `@type`과 같은 매크로 표현을 포함할 경우 묵시적으로 `macro def`로 취급됩니다.

```crystal
class Object
  def instance_vars_names
    {{ @type.instance_vars.map &.name.stringify }}
  end
end

class Person
  def initialize(@name : String, @age : Int32)
  end
end

person = Person.new "John", 30
person.instance_vars_names #=> ["name", "age"]
```

`macro def`를 명시할 수도 있지만, 이 기능은 추후 사라질 예정입니다.

```crystal
class Object
  macro def instance_vars_names
    {{ @type.instance_vars.map &.name.stringify }}
  end
end
```

매크로 정의에서 인자는 AST 노드로 전달됩니다. 따라서 매크로 확장에서 이 노드 정보에 접근(`{{some_macro_argument}}`)할 수 있습니다. 하지만 macro def의 경우는 그렇지 않습니다. 이 경우 인자의 목록은 해당 매크로 메서드에 의해 생성된 메서드에 전달된 인자와 같습니다. 컴파일 시간 값에는 접근할 수 없는 것입니다.

```crystal
class Object
  def has_instance_var?(name) : Bool
    # 매크로 확장에서의 이름은 접근할 수 없으므로
    # 매크로 언어를 사용해 배열을 생성하고
    # 실행 시간에 포함 여부를 확인해야 함
    {{ @type.instance_vars.map &.name.stringify }}.includes? name
  end
end

person = Person.new "John", 30
person.has_instance_var?("name") #=> true
person.has_instance_var?("birthday") #=> false
```
