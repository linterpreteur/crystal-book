# finalize

클래스에 소멸자, 즉 `fianlize` 메서드를 정의하면 그 클래스의 인스턴스에 대한 쓰레기 수집이 일어날 때 그 메서드가 호출됩니다.

```crystal
class Foo
  def finalize
    # Foo가 쓰레기 수집될 때 호출됨
    # 관리되지 않는 자원(C 라이브러리나 구조체 등)을 해제
  end
end
```

이 메서드를 사용하여 크리스탈 GC(쓰레기 수집기)가 직접 관리하지 못하는, 외부 라이브러리의 자원을 해제합니다.

이러한 경우의 예시는 [`IO::FileDescriptor#finalize`](https://crystal-lang.org/api/IO/FileDescriptor.html#finalize-instance-method) 또는 [`OpenSSL::Digest#finalize`](https://crystal-lang.org/api/OpenSSL/Digest.html#finalize-instance-method)에서 볼 수 있습니다.

**주의**:

`finalize` 메서드는 객체가 `initialize` 메서드를 통해 완전히 초기화된 후에만 호출됩니다. `initialize` 메서드에서 예외가 일어날 경우 `finalize`는 호출되지 않습니다. 클래스에서 소멸자를 정의하는 경우, `initialize` 메서드에서 예외가 일어났을 때 그 예외를 모두 처리하고 자원을 적절히 해제해야 합니다.

쓰레기 수집이 일어나는 동안 새 객체를 할당한다면, 예기치 못한 동작이 일어날 수 있으며 높은 확률로 프로그램이 중지될 것입니다.
