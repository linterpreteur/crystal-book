# lib

`lib` 선언은 한 라이브러리에 속하는 C 함수와 타입을 하나로 묶습니다.

```crystal
@ [Link("pcre")]
lib LibPCRE
end
```

컴파일러에 의해 강제되는 사항은 아니지만, `lib`의 이름은 보통 `Lib`으로 시작합니다.

속성을 사용하여 링커에 외부 라이브러리를 찾기 위한 플래그를 넘깁니다.

* `@ [Link("pcre")]` 플래그는 링커에 `-lpcre`를 넘기기는 하지만, 컴파일러는 어쨌든 [pkg-config](http://en.wikipedia.org/wiki/Pkg-config)을 사용하려고 먼저 시도할 것입니다.
* `@ [Link(ldflags: "...")]`는 주어진 플래그를 링커에 수정 없이 바로 넘깁니다. 예를 들어, `@ [Link(ldflags: "-lpcre")]`와 같이 쓸 수 있습니다. 일반적으로는 다음과 같이 명령어를 실행하기 위해 백틱을 사용합니다. ``@[Link(ldflags: "`pkg-config libpcre --libs`")]``.
* `@ [Link(framework: "Cocoa")]`는 링커에 `-framework Cocoa` 플래그를 넘깁니다. 이는 Mac OS X에서만 의미가 있습니다.

LibC와 같이, 라이브러리가 묵시적으로 연결되는 경우 속성을 생략할 수 있습니다.
