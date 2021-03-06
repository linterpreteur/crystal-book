# 구조체

`class` 대신 `struct`를 사용하여 타입을 정의할 수 있습니다.

```crystal
struct Point
  property x, y

  def initialize(@x : Int32, @y : Int32)
  end
end
```

구조체와 클래스의 차이는 다음과 같습니다.
* 구조체의 `new`를 호출하면 힙 대신 스택에 할당됨
* 구조체는 [값이 전달되며](http://crystal-lang.org/api/Value.html) 클래스는 참조가 전달됨
* 구조체는 암묵적으로 [Value](http://crystal-lang.org/api/Value.html)를 상속하는 [Struct](http://crystal-lang.org/api/Struct.html)를 상속함. 클래스는 암묵적으로 [Reference](http://crystal-lang.org/api/Reference.html)를 상속함.
* 구조체는 추상 구조체가 아닌 구조체를 상속할 수 없음.

마지막 사항에는 이유가 있습니다. 구조체의 메모리 구조는 매우 깔끔하게 정의되어 있습니다. 예를 들어 위에서 본 `Point` 구조체는 8 바이트를 차지합니다. `Point`의 배열을 생성하면 각 인스턴스는 배열의 버퍼 안에 위치합니다.

```crystal
# 배열의 버퍼는 각 Point마다 8 바이트를 차지함
ary = [] of Point
```

`Point`를 상속한다면, 그 타입의 배열은 다른 타입이 포함될 수도 있으며 각 원소의 크기가 그에 따라 커질 수도 있다는 사실을 고려해야 합니다. 이는 완전히 예상 밖의 일입니다. 따라서 추상 구조체가 아닌 구조체는 상속될 수 없습니다. 반면 추상 구조체는 상속될 수 있기 때문에, 그 타입의 배열은 다양한 타입을 담을 수 있습니다.

구조체도 클래스처럼 모듈을 포함하거나 제너릭이 될 수 있습니다.

구조체는 메모리 할당을 자잘하게 많이 하는 것보다 사본을 넘기는 것이 더 효율적일 때 대개 성능을 위해 사용됩니다.

그렇다면 구조체와 클래스 중 어떤 것을 선택해야 할까요? 가장 간편한 방법은 인스턴스 변수가 재할당되지 않는다면, 다시 말해 타입이 불변이라면 구조체를 사용하고 이외의 경우에는 클래스를 사용하는 것입니다.
