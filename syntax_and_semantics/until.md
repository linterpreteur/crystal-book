# until

`until`은 조건이 *참인* 값이 될 때까지 본체를 실행합니다. `until`은 조건이 반대인 `while`을 나타내는 문법적 편의 사항일 뿐입니다.

```crystal
until some_condition
  do_this
end

# 다음과 동일
while !some_condition
  do_this
end
```

`until`에서도 `break`와 `next`를 사용할 수 있습니다.
