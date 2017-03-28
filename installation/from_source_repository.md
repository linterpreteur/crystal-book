# 소스 코드로부터

프로젝트 기여하고 싶으시다면 소스 코드로부터 크리스탈을 설치하고 싶으실 겁니다.

1. [크리스탈의 최신 릴리즈](https://crystal-lang.org/docs/installation)를 다운로드합니다. 크리스탈을 컴파일하려면, 크리스탈이 필요하기 때문이죠.

2. 크리스탈이 지원하는 LLVM이 경로에 존재해야 합니다. 현재 크리스탈은 LLVM 3.8, 3.9, 4.0을 지원합니다. 가능하다면 최신 버전을 사용하세요. 맥에서 홈브루를 사용하신다면 설치 시 `--with-llvm` 플래그를 추가하여 이를 자동으로 해결할 수 있습니다.

3. 반드시 [필요한 라이브러리를 모두](https://github.com/crystal-lang/crystal/wiki/All-required-libraries) 설치하세요. [기여 가이드](https://github.com/crystal-lang/crystal/blob/master/CONTRIBUTING.md) 또한 참고하시기 바랍니다.

4. 그리고 저장소를 복제하세요.

```
git clone https://github.com/crystal-lang/crystal.git
```

5. 새로운 버전의 컴파일러를 생성하려면 `make`를 실행하세요.
6. `make spec`을 실행해서 모든 스펙을 통과한다면 모든 것이 제대로 설치된 것입니다.
7. `bin/crystal`로 크리스탈 파일을 실행하세요.

새로운 `bin/crystal`에 대해 더 알아보려면, [컴파일러 사용하기](https://crystal-lang.org/docs/using_the_compiler/) 문서를 참고하세요.

주의: 실제 바이너리는 `.build/crystal`에 위치하지만, `bin/crystal`의 크리스탈을 실행하기 위해선 래퍼(wrapper) 스크립트를 사용하셔야 합니다.
