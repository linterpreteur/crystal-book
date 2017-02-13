# 블락의 포획

블락을 포획하여 맥락, 즉 클로저로서 저장된 데이터를 갖는 코드 블락인 `Proc`으로 변환할 수 있습니다.

블락을 포획하려면 메서드의 블락 인자를 지정하여 이름과 입출력 타입을 특정해야 합니다.

```crystal
def int_to_int(&block : Int32 -> Int32)
  block
end

proc = int_to_int { |x| x + 1 }
proc.call(1) #=> 2
```

이 코드는 `int_to_int`에 넘겨진 블락을 `block` 변수에 포획하여 반환합니다. `proc`의 타입은 [Proc(Int32, Int32)](http://crystal-lang.org/api/Proc.html)로, `Int32` 인자를 하나 받아 `Int32`를 반환하는 함수입니다.

이 방법으로 블락을 콜백 함수로 저장할 수 있습니다.

```crystal
class Model
  def on_save(&block)
    @on_save_callback = block
  end

  def save
    if callback = @on_save_callback
      callback.call
    end
  end
end

model = Model.new
model.on_save { puts "저장함!" }
model.save # prints "저장함!"
```

이때 `&block`의 타입을 특정하지 않은 것은, 포획된 블락이 인자를 받지 않으며 아무것도 반환하지 않는다는 뜻입니다.

반환 타입을 특정하지 않는다면 어떤 값도 반환되지 않으므로 주의해야 합니다.

```crystal
def some_proc(&block : Int32 ->)
  block
end

proc = some_proc { |x| x + 1 }
proc.call(1) # void
```

반환 값을 받기 위해, 반환 타입을 특정하거나 밑줄을 사용해 어떤 타입이든 받도록 할 수 있습니다.

```crystal
def some_proc(&block : Int32 -> _)
  block
end

proc = some_proc { |x| x + 1 }
proc.call(1) # 2

proc = some_proc { |x| x.to_s }
proc.call(1) # "1"
```

## break와 next

포획된 블락에서는 `return`과 `break`를 사용할 수 없습니다. `next`가 사용된다면 실행이 종료되며 인자로 넘겨진 값이 블락의 값이 됩니다.

## with ... yield

`with ... yield`로 포획된 블락의 리시버를 변경하는 것은 불가능합니다.
