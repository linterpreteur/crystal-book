# struct

`lib` 안의 `struct` 선언은 C 구조체를 선언합니다.

```crystal
lib C
  # C에서는
  #
  #  struct TimeZone {
  #    int minutes_west;
  #    int dst_time;
  #  };
  struct TimeZone
    minutes_west : Int32
    dst_time     : Int32
  end
end
```

같은 타입의 여러 속성을 한 번에 선언할 수도 있습니다.

```crystal
lib C
  struct TimeZone
    minutes_west, dst_time : Int32
  end
end
```

재귀적 구조체 또한 예상대로 동작합니다.

```crystal
lib C
  struct LinkedListNode
    prev, _next : LinkedListNode*
  end
  
  struct LinkedList
    head : LinkedListNode*
  end
end
```

구조체의 인스턴스는 `new`로 생성할 수 있습니다.

```crystal
tz = C::TimeZone.new
```

스택에 구조체가 할당될 것입니다.

C 구조체는 모든 필드를 0으로 초기화합니다. 정수와 실수는 0에서 시작하고, 포인터는 0의 주소 값을 가집니다.

`uninitialized`를 이용하여 이를 방지할 수 있습니다.

```crystal
tz = uninitialized C::TimeZone
tz.minutes_west #=> 임의의 쓰레기 값
```

속성을 쓰고 읽을 수 있습니다.

```crystal
tz = C::TimeZone.new
tz.minutes_west = 1
tz.minutes_west #=> 1
```

할당하는 값이 속성의 값과 다를 경우 [to_unsafe](to_unsafe.html)가 시도됩니다.

[named arguments](../default_and_named_arguments.html)와 비슷한 문법으로 속성을 초기화할 수도 있습니다.

```crystal
tz = C::TimeZone.new minutes_west: 1, dst_time: 2
tz.minutes_west #=> 1
tz.dst_time     #=> 2
```

C 구조체는 함수와 메서드에 값으로 (복사되어) 전달되며, 메서드에서 반환될 때에도 값으로 전달됩니다.

```crystal
def change_it(tz)
  tz.minutes_west = 1
end

tz = C::TimeZone.new
change_it tz
tz.minutes_west #=> 0
```

[타입 문법](../type_grammar.html)에서 구조체 속성 타입에 쓰인 표기에 대해 알아볼 수 있습니다.
