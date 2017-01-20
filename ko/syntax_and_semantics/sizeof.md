# sizeof

`sizeof` 표현식은 주어진 타입의 크기를 `Int32`로 반환합니다.

```crystal
sizeof(Int32)  #=> 4
sizeof(Int64)  #=> 8
```

[참조형](http://crystal-lang.org/api/Reference.html) 타입이라면, 그 크기는 포인터의 크기와 동일합니다.

```crystal
# 64비트 기기일 때
sizeof(Pointer(Int32)) #=> 8
sizeof(String)         #=> 8
```

이는 참조형의 메모리는 힙에 할당되며 그 포인터가 전달되기 때문입니다. 포인터의 크기가 아니라 클래스 인스턴스의 실질적인 크기를 구하려면, [instance_sizeof](instance_sizeof.html)를 이용합니다.

sizeof의 인자는 [타입](type_grammar.html)으로, 많은 경우 [typeof](typeof.html)와 함께 사용됩니다.

```crystal
a = 1
sizeof(typeof(a)) #=> 4
```
