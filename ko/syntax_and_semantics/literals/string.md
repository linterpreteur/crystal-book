# String

[String](http://crystal-lang.org/api/String.html)은 UTF-8의 불변 연속열을 나타냅니다.

String은 UTF-8 문자를 따옴표로 감싼 문자열 리터럴로 보통 생성합니다.

```crystal
"hello world"
```

문자열 안에서 백슬래시를 이용해 특수한 문자를 표기할 수 있습니다.

```crystal
'\"' # 큰 따옴표
'\\' # 백슬래시
'\e' # 이스케이프
'\f' # 폼 피드
'\n' # 개행문자
'\r' # 캐리지 리턴
'\t' # 탭
'\v' # 수직 탭
```

백슬래시 뒤에 8진수로 세 개까지의 숫자를 써서 코드 포인트를 표기할 수도 있습니다.

```crystal
"\101" # == "A"
"\123" # == "S"
"\12"  # == "\n"
"\1"   # 코드 포인트 1의 문자 하나를 갖는 문자열
```

백슬래시와 (`\u`) 뒤에 네 개의 16진수 문자를 써서 유니코드 코드 포인트를 표기할 수도 있습니다.

```crystal
"\u0041" # == "A"
```

혹은 중괄호를 이용하여 여섯 개까지의 숫자(0부터 10FFFF까지)를 쓸 수도 있습니다.

```crystal
"\u{41}"    # == "A"
"\u{1F52E}" # == "🔮"
```

문자열은 여러 줄에 걸쳐 이어질 수 있습니다.

```crystal
"hello
      world" # same as "hello\n      world"
```

위의 예시에서 문자열 앞뒤의 공백과 개행 문자는 결과 문자열에 포함된다는 사실에 주목하세요. 이를
피하려면, 여러 리터럴을 백슬래시로 합침으로써 문자열을 여러 줄로 나누어 쓸 수 있습니다.

```crystal
"hello " \
"world, " \
"no newlines" # "hello world, no newlines"와 동일
```

또는, 백슬래시 뒤에서 개행을 함으로써 문자열 리터럴을 만들 수도 있습니다.

```crystal
"hello \
     world, \
     no newlines" # "hello world, no newlines"와 동일
```

이 경우 문자열 앞의 공백은 결과 문자열에 포함되지 않습니다.

쌍따옴표나 괄호 등 문자를 많이 포함하는 문자열을 작성할 때는 다음과 같은 대안이 있습니다.

```crystal
# 쌍따옴표와 중첩 괄호 지원
%(hello ("world")) # "hello (\"world\")"와 동일

# 쌍따옴표와 중첩 대괄호 지원
%[hello ["world"]] # "hello [\"world\"]"와 동일

# 쌍따옴표와 중첩 중괄호 지원
%{hello {"world"}} # "hello {\"world\"}"와 동일

# 쌍따옴표와 중첩 홑화살괄호 지원
%<hello <"world">> # "hello <\"world\">"와 동일
```

## 히어닥(Heredoc)

문자열을 만들기 위해 "히어닥"을 사용할 수도 있습니다.

```crystal
<<-XML
<parent>
  <child />
</parent>
XML
```

"히어닥"은 `<<-식별자`로 시작합니다. 이때 `식별자`는 글자로 시작하며 글자와 숫자의 연속열인 식별자입니다. "히어닥"은 `식별자`로 시작하는 줄에서 끝납니다. 이 줄에서 앞에 나오는 공백은 무시되고, 이 줄은 개행 문자 혹은 영문자나 숫자가 아닌 문자로 끝납니다.

마지막 특징 덕분에 히어닥에서 메서드를 호출하거나 히어닥을 괄호 안에 쓸 수 있게 됩니다.

```crystal
<<-SOME
hello
SOME.upcase # => "HELLO"

def upcase(string)
  string.upcase
end

upcase(<<-SOME
  hello
  SOME) # => "HELLO"
```

히어닥 앞에 있는 공백은 마지막 `식별자`가 가진 공백의 수에 따라 제거됩니다. 예시는 다음과 같습니다.

```crystal
# "Hello\n  world"와 동일
<<-STRING
  Hello
    world
  STRING

# "  Hello\n    world"와 동일
<<-STRING
    Hello
      world
  STRING
```

## 보간

문자열에 표현식을 포함하기 위해 문자열 보간을 사용할 수 있습니다.

```crystal
a = 1
b = 2
"sum = #{a + b}"        # "sum = 3"
```

이때 `#{...}` 안에 있는 표현식 각각에 대해 `Object#to_s(IO)`를 호출합니다.

## 보간과 탈출 문자 제외

`%q`를 통해 보간이나 탈출 문자 없는 문자열을 만들 수 있습니다.

```crystal
%q(hello \n #{world}) # => "hello \\n \#{world}"
```

`%q(...)`의 구분자로는 `{}`, `[]`, `<>` 또한 이용할 수 있습니다.

보간과 탈출 문자 없는 히어닥 또한 가능합니다. 히어닥 구분자를 작은 따옴표로 감싸기만 하면 됩니다.

```crystal
<<-'HERE'
hello \n #{world}
HERE
```
