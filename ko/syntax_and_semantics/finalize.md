# finalize

클래스에 소멸자, 즉 `fianlize` 메서드를 정의하면 그 클래스의 인스턴스에 대한 쓰레기 수집이 일어날 때 그 메서드가 호출됩니다.

```crystal
class Foo
  def finalize
    # Foo가 쓰레기 수집될 때 호출
    puts "#{self} 잘 가!"
  end
end

# "... 잘 가!"을 무한히 출력
loop do
  Foo.new
end
```

**주의:** `finalize` 메서드는 객체가 `initialize` 메서드를 통해 완전히 초기화된 후에만 호출됩니다. `initialize` 메서드에서 예외가 일어날 경우 `finalize`는 호출되지 않습니다. 클래스에서 소멸자를 정의하는 경우, `initialize` 메서드에서 예외가 일어났을 때 그 예외를 모두 처리하고 자원을 적절히 해제해야 합니다.
