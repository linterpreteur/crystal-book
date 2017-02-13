# break

`while` 반복에서 벗어나기 위해 `break`를 사용할 수 있습니다.

```crystal
a = 2
while (a += 1) < 20
  if a == 10
    # 'puts a'로 진행
    break
  end
end
puts a #=> 10
```
