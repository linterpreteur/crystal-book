# next

`while` 반복에서 다음 실행으로 넘어가기 위해 `next`를 사용할 수 있습니다. `next`를 실행한 후, `while`의 조건을 다시 확인하여 *참인* 값이라면, 몸체가 실행됩니다.

```crystal
a = 1
while a < 5
  a += 1
  if a == 3
    next
  end
  puts a
end
# 위 코드는 2, 4, 5를 출력
```
