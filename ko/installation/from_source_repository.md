# 소스 코드로부터

기여하고 싶으시다면 소스 코드로부터 크리스탈을 설치하고 싶으실 겁니다. 하지만 크리스탈은 크리스탈 자신으로 작성되어 있기 때문에, 일단 상기한 방법 중 하나를 이용하여 컴파일러를 실행시켜야 합니다.

또한 해당 경로에 LLVM 3.5 혹은 3.6이 설치되어 있어야 합니다. 맥에서 Homebrew를 이용하신다면 `--with-llvm` 플래그를 추가하여 자동으로 설치할 수 있습니다.

1. 반드시 [필요한 라이브러리를 모두](https://github.com/crystal-lang/crystal/wiki/All-required-libraries) 설치하세요. [기여 가이드](https://github.com/crystal-lang/crystal/blob/master/CONTRIBUTING.md) 또한 참고하시기 바랍니다.

2. 그리고 저장소를 복제하세요.

```
git clone https://github.com/crystal-lang/crystal.git
```

3. 새로운 버전의 컴파일러를 생성하려면 `make`를 실행하세요.
4. `make spec`을 실행해서 모든 스펙을 통과한다면 모든 것이 제대로 설치된 것입니다.
5. `bin/crystal`로 크리스탈 파일을 실행하세요.

새로운 `bin/crystal`에 대해 더 알아보려면, [컴파일러 사용하기](https://crystal-lang.org/docs/using_the_compiler/) 문서를 참고하세요.

주의: 실제 바이너리는 `.build/crystal`에 위치하지만, `bin/crystal`의 크리스탈을 실행하기 위해선 래퍼(wrapper) 스크립트를 사용하셔야 합니다.
