# 크로스 컴필레이션

크리스탈은 기초적인 [크로스 컴필레이션](http://en.wikipedia.org/wiki/Cross_compiler)을 지원합니다.

이를 위해 컴파일러 실행 파일은 두 가지 플래그를 제공합니다.

* `--cross-compile`: 크로스 컴필레이션 모드를 활성화
* `--target`: 사용할 [LLVM Target Triple](http://llvm.org/docs/LangRef.html#target-triple)로 [컴파일 시간 플래그](compile_time_flags.html)를 설정

설치한 LLVM 3.5를 이용하여 `llvm-config --host-target`을 실행하면 `--target` 플래그를 알아낼 수 있습니다. 예를 들어 리눅스에서는 "x86_64-unknown-linux-gnu"를 출력할 것입니다.

`--target`을 통해 자동으로 설정되지 않은 컴파일 시간 플래그를 설정하기 위해서는 명령어 라인에서 `-D` 플래그를 주어야 합니다.

이로써 리눅스에서 돌아갈 프로그램을 맥에서 컴파일할 수 있습니다.

```bash
crystal build your_program.cr --cross-compile --target "x86_64-unknown-linux-gnu"
```

`.o`([오브젝트 파일](http://en.wikipedia.org/wiki/Object_file))이 생성되며, 목표로 둔 운영체제에서 실행할 명령어가 출력될 것입니다.

```bash
cc your_program.o -o your_program -lpcre -lrt -lm -lgc -lunwind
```

반드시 이 `.o` 파일을 복사하고 해당 명령어를 실행해야 합니다. 그 후에 실행 파일을 그 운영체제에서 사용할 수 있습니다.

이 과정은 컴파일러를 아직 사용할 수 없는 운영체제로 컴파일러 자체를 새롭게 이식하기 위하여 사용됩니다. 크리스탈 컴파일러를 컴파일하기 위해서는 기존의 크리스탈 컴파일러가 필요하기 때문에, 컴파일러를 아직 사용할 수 없는 시스템에서 사용할 수 있는 대안은 두 가지가 있습니다.
* 루비로 작성된 컴파일러 중 최신 버전을 가져와서 현재 버전까지의 컴파일러를 컴파일
* 해당 운영체제를 목표로 `.o` 파일을 만들어 컴파일러를 생성

전자는 다소 길고 복잡하지만, 후자는 훨씬 쉽습니다.

크로스 컴파일링은 다른 실행 파일에도 똑같게 적용됩니다. 하지만 그 주 목적은 컴파일러입니다. 크리스탈이 한 시스템에서 쓸 수 없는 상태라면 크로스 컴파일링을 고려해볼 수 있습니다.
