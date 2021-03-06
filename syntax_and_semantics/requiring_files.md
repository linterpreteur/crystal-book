# 다른 파일의 사용

성능을 측정하는 작은 프로그램 같은 경우라면 파일 하나에 프로그램 코드를 전부 넣어도 괜찮을 것입니다. 하지만 규모가 커진다면 여러 파일에 코드를 분리해 넣어야 유지보수 및 코드 이해에 문제가 없습니다.

`require "..."`를 통해 컴파일러가 다른 파일을 처리하도록 명령합니다. 이 표현식은 문자열 리터럴 하나를 인자로 받으며, 다양한 방법으로 사용될 수 있습니다.

파일이 `require`되고 나면, 컴파일러는 그 절대 경로를 기억해두었다가 같은 파일을 `require`하도록 요청받을 경우 그 요청을 무시합니다.

## require "파일명"

require 경로에서 이름이 "filename"인 파일을 찾습니다.

require 경로란 컴파일러에 동봉되는 표준 라이브러리 위치와 (유닉스 셸에서 `pwd`로 얻을 수 있는) 현재 작업 디렉토리에 상대적인 "libs" 디렉토리를 의미합니다. 컴파일러는 이 경로만을 검색하게 됩니다.

검색은 다음 순서로 이루어집니다.

* require 경로에 "filename.cr" 파일이 존재할 경우 그 파일이 포함됩니다.
* "filename" 디렉토리가 존재하며 그 디렉토리 바로 아래에 "filename.cr" 파일이 있을 경우 그 파일이 포함됩니다.
* 이외의 경우 컴파일 시간 오류가 발생합니다.

전형적인 프로젝트 구조를 가정하면, 두 번째 규칙은 아주 유용합니다.

```
- project
  - libs
    - foo
      foo.cr
    - bar
      bar.cr
  - src
    - project.cr
  - spec
    - project_spec.cr
```

## require "./filename"

require 표현식을 포함하는 파일에 상대적인 경로에서 "filename"을 찾습니다.

검색은 다음 순서로 이루어집니다.

* 현재 파일과 같은 경로에 "filename.cr" 파일이 존재할 경우 그 파일이 포함됩니다.
* "filename" 디렉토리가 존재하며 그 디렉토리 바로 아래에 "filename.cr" 파일이 있을 경우 그 파일이 포함됩니다..
* 이외의 경우 컴파일 시간 오류가 발생합니다.

이 상대 경로 표현식은 한 프로젝트에서 같은 경로에 있는 다른 파일을 참조하기 위해 주로 사용됩니다. specs에서 코드를 나타내기 위해 사용할 수도 있습니다.

```crystal
# spec/project_spec.cr에서
require "../src/project"
```

## 기타 형태

두 경우 모두 중첩된 이름을 사용하여 중첩 디렉토리에서 이름을 찾게 됩니다.

* `require "foo/bar/baz"`는 require 경로에서 "foo/bar/baz.cr" 또는 "foo/bar/baz/baz.cr"를 검색합니다.
* `require "./foo/bar/baz"`는 현재 경로에서 "foo/bar/baz.cr" 또는 "foo/bar/baz/baz.cr"를 검색합니다.

"../"로 현재 경로의 상위 디렉토리에 접근할 수 있습니다. 따라서 `require "../../foo/bar"` 또한 하나의 방법입니다.

어떤 경우든 특수한 `*` 접미사와 `**` 접미사를 사용할 수 있습니다.

* `require "foo/*"`는 "foo" 디렉토리 안의 모든 ".cr" 파일을 포함하지만, "foo" 안의 디렉토리는 포함하지 않습니다.
* `require "foo/**"`는 "foo" 디렉토리 안의 모든 ".cr" 파일을 포함하며, "foo" 안의 모든 디렉토리를 재귀적으로 포함합니다.
