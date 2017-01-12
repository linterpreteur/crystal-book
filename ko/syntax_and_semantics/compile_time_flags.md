# 컴파일 시간 플래그

타입과 메서드 등 코드를 컴파일 시간의 플래그를 토대로 하여 조건적으로 정의할 수 있습니다. 이 플래그는 기본적으로는 [LLVM Target Triple](http://llvm.org/docs/LangRef.html#target-triple)을 읽어서 `-`을 경계로 쪼갠 것입니다. 컴파일 목표를 알아내기 위해 `llvm-config --host-target`을 실행할 수 있습니다.

```bash
$ llvm-config --host-target
x86_64-unknown-linux-gnu

# 따라서 플래그는 x86_64, unknown, linux, gnu
```

추가로 프로그램이 `--release`로 컴파일되었다면 `release` 플래그가 설정됩니다.

`flag?` 매크로 메서드로 플래그를 검사할 수 있습니다.

```crystal
{% if flag?(:x86_64) %}
  # 64비트 플랫폼 전용 코드
{% else %}
  # 64비트가 아닌 플랫폼 전용 코드
{% end %}
```

true 혹은 false를 반환하므로, `&&`와 `||` 또한 사용할 수 있습니다.

```crystal
{% if flag?(:linux) && flag?(:x86_64) %}
  # 64비트 리눅스 전용 코드
{% end %}
```

플래그는 보통 C 바인딩에서 조건적으로 타입과 함수를 정의하는 데 쓰입니다. 예를 들어 아주 유명한 `size_t` 타입은 크리스탈에서 다음과 같이 정의할 수 있습니다.

```crystal
lib C
  {% if flag?(:x86_64) %}
    alias SizeT = UInt64
  {% else %}
    alias SizeT = UInt32
  {% end %}
end
```
