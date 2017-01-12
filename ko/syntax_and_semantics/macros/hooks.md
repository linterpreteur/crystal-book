# 훅

특수한 상황에 훅으로 호출되는 매크로들이 있습니다.
`inherited`, `included`, `extended`, `method_missing`입니다.
* `inherited`는 하위 클래스가 정의될 경우 컴파일 시간에 호출됩니다. `@type`은 상속을 받는 하위 타입입니다.
* `included`는 모듈이 포함될 경우 컴파일 시간에 호출됩니다. `@type`은 포함하는 하위 타입입니다.
* `extended`는 모듈이 확장될 경우 컴파일 시간에 호출됩니다. `@type`은 확장하는 하위 타입입니다.
* `method_missing`은 메서드를 찾지 못한 경우 컴파일 시간에 호출됩니다.

다음은 `inherited`의 예시입니다.

```crystal
class Parent
  macro inherited
    def lineage
      "{{@type.name.id}} < Parent"
    end
  end
end

class Child < Parent
end

Child.new.lineage #=> "Child < Parent"
```

다음은 `method_missing`의 예시입니다.

```crystal
macro method_missing(call)
  print "Got ", {{call.name.id.stringify}}, " with ", {{call.args.size}}, " arguments", '\n'
end

foo          # Prints: Got foo with 0 arguments
bar 'a', 'b' # Prints: Got bar with 2 arguments
```
