# out

[waitpid](http://www.gnu.org/software/libc/manual/html_node/Process-Completion.html) 함수를 생각해 봅시다.

```crystal
lib C
  fun waitpid(pid : Int32, status_ptr : Int32*, options : Int32) : Int32
end
```

문서를 읽어보면 이렇게 나와 있습니다.

```
status_ptr이 널 포인터가 아니라면 status_ptr이 가리키는 객체에 자식 프로세스의 상태 정보가 저장된다.
```

함수를 이렇게 이용할 수 있습니다.

```crystal
pid = ...
options = ...
status_ptr = uninitialized Int32

C.waitpid(pid, pointerof(status_ptr), options)
```

이로써 우리는 값을 채울 `status_ptr` 포인터를 넘길 수 있습니다.

`out` 인자를 통해 더 짧게 쓸 수도 있습니다.

```crystal
pid = ...
options = ...

C.waitpid(pid, out status_ptr, options)
```

인자가 `Int32*`이기 때문에, 컴파일러가 자동으로 `Int32` 타입의 `status_ptr` 변수를 선언할 것입니다.

인자가 어떤 타입의 포인터이며, 함수가 그 포인터가 가리키는 값을 채우기만 한다면 이는 어떤 타입에도 적용될 것입니다.
