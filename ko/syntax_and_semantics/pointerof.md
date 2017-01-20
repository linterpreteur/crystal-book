# pointerof

`pointerof` 표현식은 변수 혹은 인스턴스 변수의 내용물을 가리키는 [Pointer](http://crystal-lang.org/api/Pointer.html)를 반환합니다.

변수의 예시입니다.

```crystal
a = 1

ptr = pointerof(a)
ptr.value = 2

a #=> 2
```

인스턴스 변수의 예시입니다.

```crystal
class Point
  def initialize(@x, @y)
  end

  def x
    @x
  end

  def x_ptr
    pointerof(@x)
  end
end

point = Point.new 1, 2

ptr = point.x_ptr
ptr.value = 10

point.x #=> 10
```

`pointerof`는 포인터를 사용하므로, [안전하지 않은](unsafe.html) 코드로 간주됩니다.

