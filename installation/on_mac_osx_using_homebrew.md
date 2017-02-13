# Mac OSX에서 Homebrew를 이용하여

맥에서는 [Homebrew](http://brew.sh/)를 이용하여 손쉽게 크리스탈을 설치하실 수 있습니다.

```
brew update
brew install crystal-lang
```

프로젝트에 기여하시려면 LLVM 또한 설치하는 것이 유용합니다. 마지막 줄을 다음과 같이 바꾸어 LLVM을 설치할 수 있습니다.

```
brew install crystal-lang --with-llvm
```

## OSX 10.11 엘 카피탄에서의 문제 해결

다음 오류가 발생한다면

```
ld: library not found for -levent
```

커맨드 라인 툴을 재설치하고 기본 활성 툴체인을 선택하세요.

```
$ xcode-select --install
$ xcode-select --switch /Library/Developer/CommandLineTools
```
