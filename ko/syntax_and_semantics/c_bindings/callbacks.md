# 콜백 함수

C 선언에서의 함수 타입을 이용할 수 있습니다.

```crystal
lib X
  # C에서는
  #
  #    void callback(int (*f)(int));
  fun callback(f : Int32 -> Int32)
end
```

그러면 함수([Proc](http://crystal-lang.org/api/Proc.html))를 다음과 같이 넘길 수 있습니다.

```crystal
f = ->(x : Int32) { x + 1 }
X.callback(f)
```

함수를 호출하는 줄에서 바로 선언하면 인자의 타입을 생략할 수 있습니다. 컴파일러가 `fun`의 시그니처에 따라 타입을 추가할 것입니다.

```crystal
X.callback ->(x) { x + 1 }
```

하지만 C에 넘겨진 함수는 클로저를 형성하지 않는다는 사실을 기억해야 합니다. 컴파일러가 클로저가 전달되는 것을 감지한다면, 컴파일 시간 오류가 발생합니다.

```crystal
y = 2
X.callback ->(x) { x + y } # 오류: C 함수에 클로저를 보낼 수 없음
```

컴파일러가 이를 컴파일 시간에 감지하지 못한다면 실행 시간 예외가 일어납니다.

[타입 문법](../type_grammar.html)에서 콜백과 프록 타입에 쓰인 표기에 대해 알아볼 수 있습니다.

콜백 대신에 `NULL`을 넘기려면, `nil`을 넘기면 됩니다.

```crystal
# C에서의 callback(NULL)와 동일
X.callback nil
```

### C 함수에 클로저 넘기기

대부분의 경우 콜백이 있는 C 함수는 사용자 정의 데이터를 위한 인자가 있습니다. 이 데이터는 콜백의 인자로 넘겨집니다. 예를 들어, 콜백을 계속 호출하는 C 함수를 가정해 봅시다.

```crystal
lib LibTicker
  fun on_tick(callback : (Int32, Void* ->), data : Void*)
end
```

이 함수를 올바르게 감싸는 방법은 Proc을 콜백 데이터로 보내고, 그 데이터를 Proc으로 변환하여 호출하는 것입니다.

```crystal
module Ticker
  # 사용자 콜백은 Void*가 없음
  def self.on_tick(&callback : Int32 ->)
    # GC의 수집을 피하기 위해 크리스탈에서 저장해야 함
    @@callback = callback

    # Proc은 {Void*, Void*}이므로, Void*로 변환할 수 없고,
    # "상자"에 메모리를 할당하고 프록을 담아야 함
    boxed_data = Box.box(callback)

    # 클로저를 만들지 않는 콜백과 boxed_data를 콜백 데이터로 넘김
    LibTicker.on_tick(->(tick, data) {
      # 이제 Box.unbox로 데이터를 Proc으로 되돌림
      data_as_callback = Box(typeof(callback)).unbox(data)
      # 마침내 사용자 콜백을 호출
      data_as_callback.call(tick)
    }, boxed_data)
  end
end

Ticker.on_tick do |tick|
  puts tick
end
```

`@@callback`에 콜백을 저장한다는 데 주의해야 합니다. 이렇게 하지 않으면 코드가 더 이상 참조하지 않을 때 GC가 콜백을 쓰레기 수집할 것입니다. C 라이브러리는 물론 콜백을 저장하겠지만, 크리스탈의 GC는 그 사실을 알 방법이 없기 때문입니다.

## Raises 속성

C 함수가 예외를 던질 수 있는 사용자 정의 콜백을 실행할 경우, `@[Raises]` 속성으로 표기해야 합니다.

컴파일러는 메서드가 `@[Raises]` 속성을 갖거나 예외를 일으키는 메서드를 호출한다면 이 속성을 (재귀적으로) 추론해냅니다.

하지만 일부 C 함수는 다른 C 함수가 실행할 콜백을 받을 수 있습니다. 예를 들어 가상의 라이브러리를 하나 가정해봅시다.

```crystal
lib LibFoo
  fun store_callback(callback : ->)
  fun execute_callback
end

LibFoo.store_callback ->{ raise "안 돼!" }
LibFoo.execute_callback
```

`store_callback`에 전해진 콜백이 예외를 일으킨다면 `execute_callback` 또한 예외를 일으킬 것입니다. 하지만 컴파일러는 `execute_callback`이 `@[Raises]`로 표기되어 있지 않기 때문에 잠재적으로 예외를 일으킬 수 있다는 사실을 알 수가 없습니다. 이런 경우에는 수동으로 표시해 주어야 합니다.

```crystal
lib LibFoo
  fun store_callback(callback : ->)

  @ [Raises]
  fun execute_callback
end
```

`@[Raises]`로 제대로 표시하지 않을 경우, 이 함수의 호출을 감싸는 `begin/rescue` 블럭은 예상대로 동작하지 않을 것입니다.
