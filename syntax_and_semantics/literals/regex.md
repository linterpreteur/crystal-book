# Regex

정규 표현식은 [Regex](http://crystal-lang.org/api/Regex.html) 클래스에 의해 표현됩니다. 이는 대개 리터럴로 생성됩니다.

```crystal
foo_or_bar = /foo|bar/
heeello    = /h(e+)llo/
integer    = /\d+/
```

정규 표현식 리터럴은 `/`에 의해 구분되며 [PCRE](http://pcre.org/pcre.txt) 문법을 사용합니다.

또한 다음 수정자가 따라올 수 있습니다.

* i: 대소문자 무시 (PCRE_CASELESS)
* m: 멀티라인 (PCRE_MULTILINE)
* x: 확장 (PCRE_EXTENDED)

예시는 다음과 같습니다.

```crystal
r = /foo/imx
```

슬래시는 탈출 문자로 쓰여야 합니다.

```crystal
slash = /\//
```

다른 문법 또한 가능합니다.

```crystal
r = %r(regex with slash: /)
```
