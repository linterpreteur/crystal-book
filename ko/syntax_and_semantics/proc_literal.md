# Proc 리터럴

블락을 포획하는 것은 [Proc 리터럴](literals/proc.html)을 선언하여 메서드에 [넘기는](block_forwarding.html) 것과 동일합니다.

```crystal
def some_proc(&block : Int32 -> Int32)
  block
end

x = 0
proc = ->(i : Int32) { x += i }
proc = some_proc(&proc)
proc.call(1)  #=> 1
proc.call(10) #=> 11
x #=> 11
```

[리터럴](literals/proc.html) 항목에서 설명했듯, 프록은 기존 메서드로부터 생성할 수도 있습니다.

```crystal
def add(x, y)
  x + y
end

adder = ->add(Int32, Int32)
adder.call(1, 2) #=> 3
```
