# Char

[Char](http://crystal-lang.org/api/Char.html) 타입은 [유니코드](http://en.wikipedia.org/wiki/Unicode) [코드 포인트](http://en.wikipedia.org/wiki/Code_point)를 나타내며 32비트를 차지합니다.

UTF-8 문자를 작은 따옴표로 둘러싸서 생성합니다.

```crystal
'a'
'z'
'0'
'_'
'あ'
```

역슬래시를 사용하여 특수한 문자를 표기할 수 있습니다.

```crystal
'\'' # 작은 따옴표
'\\' # 역슬래시
'\e' # 이스케이프
'\f' # 폼 피드
'\n' # 개행문자
'\r' # 캐리지 리턴
'\t' # 탭
'\v' # 수직 탭
```

역슬래시와 (`\u`) 뒤에 네 개의 16진수 문자를 써서 유니코드 코드 포인트를 표기할 수도 있습니다.

```crystal
'\u0041' # == 'A'
```

혹은 중괄호를 이용하여 여섯 개까지의 숫자(0부터 10FFFF까지)를 쓸 수도 있습니다.

```crystal
'\u{41}'    # == 'A'
'\u{1F52E}' # == '🔮'
```
