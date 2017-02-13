# 기본값

메서드의 인자에 기본값을 지정할 수 있습니다. 기본값을 지정한 인자는 기본값이 없는 인자 뒤에 있어야 합니다.

```crystal
class Person
  def become_older(by = 1)
    @age += by
  end
end

cholsu = Person.new "철수"
cholsu.age #=> 0

cholsu.become_older
cholsu.age #=> 1

cholsu.become_older 2
cholsu.age #=> 3
```

# 이름 달린 인자

순서가 아니라 이름을 이용해서도 인자를 특정할 수 있습니다.

```crystal
cholsu.become_older by: 5
```

인자가 여러 개 있다면, 필요한 인자가 모두 주어지는 한 호출에 사용되는 이름의 순서는 중요하지 않습니다.

```crystal
def some_method(x, y = 1, z = 2, w = 3)
  # 어쩌구저쩌구...
end

some_method 10                   # x: 10, y: 1, z: 2, w: 3
some_method 10, z: 10            # x: 10, y: 1, z: 10, w: 3
some_method 10, w: 1, y: 2, z: 3 # x: 10, y: 2, z: 3, w: 1
some_method y: 10, x: 20         # x: 20, y: 10, z: 2, w: 3

some_method y: 10                # 오류, 인자 없음: x
```

메서드에서 (다음 장에 설명할 개념인) 스플랫을 특정한다면, 이름 달린 인자로만 인자를 넘길 수는 없습니다. 이는 인자가 어떻게 대응될지 알아내는 것이 매우 어려워지기 때문입니다. 이런 경우에는 위치에 따른 인자를 추론하는 것이 더욱 쉽습니다.
