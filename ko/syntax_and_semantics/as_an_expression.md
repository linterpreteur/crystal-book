# 표현식 용법

`if`의 값은 각 분기에 마지막으로 나오는 표현식의 값입니다.

```crystal
a = if 2 > 1
3
else
4
end
a #=> 3
```

한 분기가 비어 있거나 없다면 `nil`을 가진 것으로 취급됩니다.

```crystal
if 1 > 2
3
end

# 다음과 동일
if 1 > 2
3
else
nil
end

# 다른 예시
if 1 > 2
else
3
end

# 다음과 동일
if 1 > 2
nil
else
3
end
```