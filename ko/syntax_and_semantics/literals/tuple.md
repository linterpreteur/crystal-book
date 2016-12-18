# Tuple

[Tuple](http://crystal-lang.org/api/Tuple.html)은 튜플 리터럴로 보통 생성됩니다.

```crystal
tuple = {1, "hello", 'x'} # Tuple(Int32, String, Char)
tuple[0]                  #=> 1       (Int32)
tuple[1]                  #=> "hello" (String)
tuple[2]                  #=> 'x'     (Char)
```

[Tuple.new](http://crystal-lang.org/api/Tuple.html#new%28%2Aargs%29-class-method)을 이용해 빈 튜플을 만들 수 있습니다.

튜플 타입을 표기하기 위해 다음과 같이 할 수 있습니다.

```crystal
# Int32, String, Char 튜플의 타입 표기
Tuple(Int32, String, Char)
```

제너릭 타입 인자 및 타입을 받는 기타 조건인 타입 제약에서는 [타입 문법](../type_grammar.html)에 설명된 대로 더 짧은 문법을 사용할 수도 있습니다.

```crystal
# Int32, String, Char 튜플의 배열
Array({Int32, String, Char})
```
