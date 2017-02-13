# 모듈

모듈에는 두 가지 용도가 있습니다.

* 다른 타입, 메서드, 상수를 위한 네임스페이스로
* 다른 타입에 혼합될 수 있는 부분 타입으로

모듈을 네임스페이스로 사용하는 예입니다.

```crystal
module Curses
  class Window
  end
end

Curses::Window.new
```

라이브러리를 작성한다면 이름 충돌을 방지하기 위해 모듈 안에 메서드 등을 정의해야 합니다. 표준 라이브러리는 아주 흔한 타입과 메서드를 정의하기 때문에 긴 이름을 방지하기 위해서 보통 네임스페이스를 사용하지 않습니다.

모듈을 부분 타입으로 사용하려면 `include` 혹은 `extend`를 사용합니다.

`include`는 그 타입이 모듈에 정의된 메서드를 인스턴스 메서드로 포함하겠다는 뜻입니다.

```crystal
module ItemsSize
  def size
    items.size
  end
end

class Items
  include ItemsSize

  def items
    [1, 2, 3]
  end
end

items = Items.new
items.size #=> 3
```

위 예시는 `Items` 클래스에 `size` 메서드를 붙여넣은 것처럼 작동합니다. 이는 각 타입이 조상 타입, 또는 부모 타입의 목록을 들고 있음으로써 구현됩니다. 이 목록은 기본적으로는 상위 클래스로 시작합니다. 모듈이 타입에 포함된다면 모듈은 그 리스트 **앞쪽에** 추가됩니다. 한 타입에서 메서드를 찾을 수 없는 경우, 컴파일러는 그 다음으로 이 목록에서 메서드를 찾습니다. `super`를 호출할 때는 조상 목록의 맨 앞에 있는 것이 사용됩니다.

모듈은 다른 모듈을 포함할 수 있기 때문에 컴파일러는 메서드를 찾을 수 없을 때 그 모듈이 포함하고 있는 모듈에서 그 메서드를 찾습니다.

`extend`는 그 타입이 모듈에 정의된 메서드를 클래스 메서드로 포함하겠다는 뜻입니다.

```crystal
module SomeSize
  def size
    3
  end
end

class Items
  extend SomeSize
end

Items.size #=> 3
```

`include`와 `extend`로 모듈에 정의된 상수를 하위 타입에서 사용할 수 있습니다.

두 경우 모두 최상위 수준에서 사용한다면 네임스페이스를 쓰지 않고도 메서드와 변수 및 상수를 사용할 수 있게 됩니다. (하지만 이름 충돌이 일어날 확률은 높아집니다.)

```crystal
module SomeModule
  class SomeType
  end

  def some_method
    1
  end
end

include SomeModule

SomeType.new # 정상 작동, SomeModule::SomeType과 동일
some_method  # 정상 작동, 1
```

## extend self

`extend self`는 모듈에서 흔히 쓰이는 패턴입니다.

```crystal
module Base64
  extend self

  def encode64(string)
    # ...
  end

  def decode64(string)
    # ...
  end
end
```

이로써 모듈을 네임스페이스로 사용할 수 있습니다.

```crystal
Base64.encode64 "hello" #=> "aGVsbG8="
```

프로그램에서 모듈을 포함한다면 네임스페이스 없이도 메서드를 호출할 수 있습니다.

```crystal
include Base64

encode64 "hello" #=> "aGVsbG8="
```

메서드의 이름이 모듈과 어느 정도 관련이 있는 것이 아니라면 이름 충돌의 가능성은 더 높습니다.

모듈의 인스턴스를 만드는 것은 불가능합니다.

```crystal
module Moo
end

Moo.new # Moo:Class의 `new` 메서드 정의되지 않음
```
