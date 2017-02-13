# 프로그래밍 언어 크리스탈

이 문서는 프로그래밍 언어 크리스탈의 문서입니다.

크리스탈은 다음을 목표로 하는 프로그래밍 언어입니다.

* 루비와 비슷한 문법 (하지만 호환성은 목표가 아님)
* 정적 타입을 가지지만, 변수나 함수 인자의 타입을 명시할 필요는 없음.
* 크리스탈로 바인딩을 작성함으로써 C 코드를 호출할 수 있음.
* 보일러플레이트 코드를 방지하기 위해 컴파일 타임 평가와 코드 생성이 있음.
* 효율적인 네이티브 코드로 컴파일됨.

## 빌드

```
$ git clone https://github.com/crystal-lang/crystal-book.git
$ cd crystal-book
$ npm install -g gitbook-cli@2.3.0
$ npm install
$ gitbook serve
Live reload server started on port: 35729
Press CTRL+C to quit ...

info: 8 plugins are installed
info: loading plugin "ga"... OK
...
Starting server ...
Serving book on http://localhost:4000

```

HTML 출력물이 _book 폴더에 저장될 것입니다. 로컬에서 파일을 연다면 링크가 작동하지 않을 수 있습니다.
의존성을 전역으로 설치하고 싶지 않다면, 도커 환경을 이용할 수 있습니다.

```
$ docker-compose up
...
gitbook_1  | Starting server ...
gitbook_1  | Serving book on http://localhost:4000
gitbook_1  | Restart after change in file node_modules/.bin
...
```

## 기여

스스로가 유용한 인물이라고 생각하시나요? 버그 혹은 불분명한 서술을 찾으신다면 이 문서에 언제든 기여하실 수 있습니다. 공식 저장소로(https://github.com/crystal-lang/crystal-book) 풀 리퀘스트를 보내실 수 있습니다. 번역에 관한 것은 번역본 저장소로(https://github.com/linterpreteur/crystal-book) 문의해 주세요.

감사합니다!

### 페이지 추가

`overview/hello_word.md`와 같이 원하는 위치에 마크다운 파일을 생성하여 페이지를 추가할 수 있습니다. 그 후 문서의 목차인 `SUMMARY.md` 파일에 링크를 추가하세요.