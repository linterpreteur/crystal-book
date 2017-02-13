# 코딩 스타일

이 스타일은 표준 라이브러리에서 사용됩니다. 여러분의 프로젝트에 이 스타일을 이용하신다면 다른 개발자가 더 친숙하게 느낄 수 있을 것입니다.

## 네이밍

__타입 이름__은 캐멀 케이스입니다.

```crystal
class ParseError < Exception
end

module HTTP
  class RequestHandler
  end
end

alias NumericValue = Float32 | Float64 | Int32 | Int64

lib LibYAML
end

struct TagDirective
end

enum Time::DayOfWeek
end
```

__메서드 이름__은 언더스코어 케이스입니다.

```crystal
class Person
  def first_name
  end

  def date_of_birth
  end

  def homepage_url
  end
end
```

__변수명__은 언더스코어 케이스입니다.

```crystal
class Greeting
  @@default_greeting = "Hello world"

  def initialize(@custom_greeting = nil)
  end

  def print_greeting
    greeting = @custom_greeting || @@default_greeting
    puts greeting
  end
end
```

__상수명__은 스크리밍 케이스입니다.

```crystal
LUCKY_NUMBERS     = [3, 7, 11]
DOCUMENTATION_URL = "http://crystal-lang.org/docs"
```

### 줄임말

클래스 이름에서, 줄임말은 _모두 대문자_입니다. 예를 들어, `HTTP`나 `LibXML`과 같습니다.

메서드 이름에서, 줄임말은 _모두 소문자_입니다. 예를 들어, `#from_json`이나 `#to_io`와 같습니다.

### Lib

`Lib` 이름은 `Lib`으로 시작합니다. 예를 들어, `LibC`, `LibEvent2`와 같습니다.

### 디렉토리와 파일 이름

한 프로젝트 안에서

- `/`는 README 문서와 프로젝트 설정(CI나 에디터 설정), 그리고 기타 프로젝트 수준의 문서(변경사항이나 기여 가이드)를 포함합니다.
- `src/`는 프로젝트의 소스 코드를 포함합니다.
- `spec/`는 `crystal spec`으로 실행할 수 있는 프로젝트 스펙을 포함합니다.
- `bin/`은 실행 파일을 포함합니다.

파일 경로는 내용물의 네임스페이스와 일치해야 합니다. 파일명은 정의하는 클래스 혹은 네임스페이스에 따라 _언더스코어 케이스_로 짓습니다.

예를 들어, `HTTP::WebSocket`은 `src/http/web_socket.cr`에 정의되어 있습니다.

## 공백

네임스페이스, 메서드, 블락, 혹은 기타 상황에서 __스페이스 2개__로 코드를 들여씁니다.

```crystal
module Scorecard
  class Parser
    def parse(score_text)
      begin
        score_text.scan(SCORE_PATTERN) do |match|
          handle_match(match)
        end
      rescue err : ParseError
        # 에러 처리 ...
      end
    end
  end
end
```

클래스 안에서, 메서드 정의, 상수, 내부 클래스 정의를 _한 줄씩 띄어서_ 분리하세요.

```crystal
module Money
  CURRENCIES = {
    "EUR" => 1.0,
    "ARS" => 10.55,
    "USD" => 1.12,
    "JPY" => 134.15,
  }

  class Amount
    getter :currency, :value

    def initialize(@currency, @value)
    end
  end

  class CurrencyConversion
    def initialize(@amount, @target_currency)
    end

    def amount
      # 변환 구현 ...
    end
  end
end
```
