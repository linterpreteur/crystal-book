# Symbol

[Symbol](http://crystal-lang.org/api/Symbol.html)은 숫자 값 없이 이름만으로 식별되는 상수입니다.

```crystal
:hello
:good_bye

# 공백을 포함한 기호
:"symbol with spaces"

# 물음표와 느낌표로 끝남
:question?
:exclamation!

# 연산자
:+
:-
:*
:/
:==
:<
:<=
:>
:>=
:!
:!=
:=~
:!~
:&
:|
:^
:~
:**
:>>
:<<
:%
:[]
:[]?
:[]=
:<=>
:===
```

내부적으로 기호는 `Int32`로 표현됩니다.
