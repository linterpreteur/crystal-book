# NamedTuple

[NamedTuple](http://crystal-lang.org/api/NamedTuple.html)은 보통 이름 달린 튜플 리터럴로 생성합니다.

```crystal
tuple = {name: "Crystal", year: 2011} # NamedTuple(name: String, year: Int32)
tuple[:name] # => "Crystal" (String)
tuple[:year] # => 2011      (Int32)
```

이름 달린 튜플의 타입을 표기하려면 다음과 같이 할 수 있습니다.

```crystal
# x: Int32, y: String의 이름 달린 튜플 타입 표기
NamedTuple(x: Int32, y: String)
```

타입 제약, 제너릭 타입 인자, 그 외 타입을 써야 하는 경우에는 [타입 문법](../type_grammar.html)에 설명된 대로 더 짧은 문법을 사용할 수도 있습니다.

```crystal
# x: Int32, y: String의 이름 달린 튜플의 배열
Array({x: Int32, y: String})
```

문자열 리터럴 또한 이름 달린 튜플의 키가 될 수 있습니다.

```crystal
{"this is a key": 1}
```
