# 컴파일러의 사용

컴파일러를 [설치](../installation/README.md)하고 나면 `crystal` 바이너리가 생성됩니다.

앞으로 달러 기호(`$`)는 커맨드 라인을 나타냅니다.

## 컴파일과 실행을 한 번에

파일명 하나를 가지고 `crystal`을 호출하여 컴파일과 실행을 한 방에 끝낼 수 있습니다.

```
$ crystal some_program.cr
```

크리스탈 파일은 `.cr` 확장자로 끝납니다.

혹은 `run` 명령어를 이용할 수도 있습니다.

```
$ crystal run some_program.cr
```

## 실행 파일 생성

실행 파일을 만들기 위해서는 `build` 명령어를 이용하세요.

```
$ crystal build some_program.cr
```

실행할 수 있는 `some_program` 파일이 생성됩니다.

```
$ ./some_program
```

**주의:** 생성된 실행 파일은 **기본 설정으로는 완전히 최적화되지 않습니다**. 최적화를 켜기 위해, `--release` 플래그를 이용하세요.

```
$ crystal build some_program.cr --release
```

프로덕션을 위한 실행 파일을 만드는 경우이거나 벤치마크를 수행하실 때는 반드시 `--release` 플래그를 이용하세요.

기본적으로 최적화되지 않는 이유는 최적화 없이도 성능이 꽤 괜찮은 데다가, 컴파일 시간이 훨씬 줄어들기 때문에 `crystal` 명령어를 거의 인터프리터인 것처럼 사용할 수 있기 때문입니다.

## 프로젝트 혹은 라이브러리 생성

`init` 명령어를 사용하여 표준 디렉토리 구조를 갖는 크리스탈 프로젝트를 만들 수 있습니다.

```
$ crystal init lib my_cool_lib
      create  my_cool_lib/.gitignore
      create  my_cool_lib/LICENSE
      create  my_cool_lib/README.md
      create  my_cool_lib/.travis.yml
      create  my_cool_lib/shard.yml
      create  my_cool_lib/src/my_cool_lib.cr
      create  my_cool_lib/src/my_cool_lib/version.cr
      create  my_cool_lib/spec/spec_helper.cr
      create  my_cool_lib/spec/my_cool_lib_spec.cr
Initialized empty Git repository in ~/my_cool_lib/.git/
```

## 기타 명령어와 옵션

모든 명령어 목록을 보시려면 인자 없이 `crystal`을 호출하세요.

```
$ crystal
Usage: crystal [command] [switches] [program file] [--] [arguments]

Command:
    init                     generate a new project
    build                    build an executable
    deps                     install project dependencies
    docs                     generate documentation
    env                      print Crystal environment information
    eval                     eval code from args or standard input
    play                     starts crystal playground server
    run (default)            compile and run program
    spec                     compile and run specs (in spec directory)
    tool                     run a tool
    help, --help, -h         show this help
    version, --version, -v   show version
```

특정한 명령어와 함께 쓸 수 있는 옵션을 보시려면, 명령어 뒤에 `--help`를 입력하세요.

```
$ crystal build --help
Usage: crystal build [options] [programfile] [--] [arguments]

Options:
    --cross-compile                  cross-compile
    -d, --debug                      Add symbolic debug info
    -D FLAG, --define FLAG           Define a compile-time flag
    --emit [asm|llvm-bc|llvm-ir|obj] Comma separated list of types of output for the compiler to emit
    -f text|json, --format text|json Output format text (default) or json
    -h, --help                       Show this message
    --ll                             Dump ll to .crystal directory
    --link-flags FLAGS               Additional flags to pass to the linker
    --mcpu CPU                       Target specific cpu type
    --no-color                       Disable colored output
    --no-codegen                     Don't do code generation
    -o                               Output filename
    --prelude                        Use given file as prelude
    --release                        Compile in release mode
    -s, --stats                      Enable statistics output
    --single-module                  Generate a single LLVM module
    --threads                        Maximum number of threads to use
    --target TRIPLE                  Target triple
    --verbose                        Display executed commands
```
