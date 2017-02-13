# 블락 넘기기

포획한 블락을 넘기려면, `&`로 시작하는 블락 인자를 사용합니다.

```crystal
def capture(&block)
  block
end

def invoke(&block)
  block.call
end

proc = capture { puts "안녕" }
invoke(&proc) # prints "안녕"
```

이 예시에서 `invoke`는 블락을 받습니다. `invoke`는 일반적인 인자가 아니라 블락을 받기 때문에 `proc`을 바로 인자로 넘겨줄 수는 없습니다. `proc`을 블락 인자로 넘기려면 `&`을 사용합니다. 그러지 않으면 오류가 발생합니다.

```crystal
invoke(proc) # 오류: 'invoke'의 인자 개수 틀림 (0개 자리에 1개)
```

블락을 받는 메서드에 프록을 넘길 수도 있습니다.

```crystal
def capture(&block)
  block
end

def twice
  yield
  yield
end

proc = capture { puts "안녕" }
twice &proc
```

단순하게 다음과 같이 쓸 수도 있습니다.

```crystal
proc = capture { puts "안녕" }
twice do
  proc.call
end
```

`&` 문법과 `->` 문법을 조합할 수도 있습니다.

```crystal
twice &->{ puts "안녕" }
```

또는,

```crystal
def say_hello
  puts "안녕"
end

twice &->say_hello
```

## 포획하지 않은 블락 넘기기

포획하지 않은 블락을 넘기려면 `yield`를 사용해야 합니다.

```crystal
def foo
  yield 1
end

def wrap_foo
  puts "foo 이전"
  foo do |x|
    yield x
  end
  puts "foo 이후"
end

wrap_foo do |i|
  puts i
end

# 출력:
# foo 이전
# 1
# foo 이후
```

`&block` 문법으로 블락을 넘길 수도 있지만, 입력 인자 타입을 특정해야 하며 클로저가 사용되므로 더 느려집니다.

```crystal
def foo
  yield 1
end

def wrap_foo(&block : Int32 -> _)
  puts "foo 이전"
  foo(&block)
  puts "foo 이후"
end

wrap_foo do |i|
  puts i
end

# 출력:
# foo 이전
# 1
# foo 이후
```

`yield`로 충분하다면 이런 방법은 쓰지 않는 것이 좋습니다. 포획한 블락에서는 `break`와 `next`를 쓸 수 없기 때문에 다음과 같은 코드는 동작하지 않는다는 문제도 있습니다.

```crystal
foo_forward do |i|
  break # error
end
```

요약하자면 `yield`를 사용할 때는 `&block`으로 인자를 넘기는 것을 피해야 합니다.
