# 신규 변수

매크로가 코드를 생성하고 나면 크리스탈 파서는 매크로가 호출된 맥락의 지역 변수를 가지고 그 코드를 분석합니다.

예시를 보면 쉽게 이해할 수 있습니다.

```crystal
macro update_x
  x = 1
end

x = 0
update_x
x #=> 1
```

지역변수를 일부러 읽고 쓰는 코드를 줄이기 위해 쓰일 수도 있겠지만, 실수로 지역 변수를 덮어쓸지도 모르는 일입니다. 이를 피하기 위해, `%name`과 같이 신규 변수를 선언할 수 있습니다.

```crystal
macro dont_update_x
  %x = 1
  puts %x
end

x = 0
dont_update_x # outputs 1
x #=> 0
```

이 예시에서, `%x`는 현재 범위의 정적 변수와 이름이 충돌하지 않는 변수를 선언하는 것입니다.

추가로 신규 변수는 다른 AST 노드에 대하여 `%var{key1, key2, ..., keyN}`과 같이 선언할 수도 있습니다.

```crystal
macro fresh_vars_sample(*names)
  # First declare vars
  {% for name, index in names %}
    print "Declaring: ", "%name{index}", '\n'
    %name{index} = {{index}}
  {% end %}

  # Then print them
  {% for name, index in names %}
    print "%name{index}: ", %name{index}, '\n'
  {% end %}
end

fresh_vars_sample a, b, c

# Sample output:
# Declaring: __temp_255
# Declaring: __temp_256
# Declaring: __temp_257
# __temp_255: 0
# __temp_256: 1
# __temp_257: 2
```

위 코드는 인덱스 있는 변수를 세 개 선언하여 값을 할당하고 알맞은 인덱스를 출력하는 예시입니다.
