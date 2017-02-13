# instance_sizeof

`instance_sizeof` 표현식은 주어진 클래스의 인스턴스 크기를 `Int32`로 반환합니다.

```crystal
class Point
  def initialize(@x, @y)
  end
end

Point.new 1, 2

# 2 x Int32 = 2 x 4 = 8
instance_sizeof(Point) #=> 12
```

인스턴스에 `Int32` 속성이 두 개 있지만 컴파일러는 객체의 ID 값을 위한 `Int32` 속성을 한 개 더 추가합니다. 따라서 위의 코드에서 인스턴스 크기는 8이 아니라 12가 되는 것입니다.
