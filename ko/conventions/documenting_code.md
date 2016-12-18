# 문서 작성

크리스탈 문서 주석은 [마크다운](https://daringfireball.net/projects/markdown/)의 서브셋을 이용합니다.

* 문서는 클래스, 모듈, 메서드의 바로 위에 위치해야 합니다. 빈 칸이 있어서는 안 됩니다.

```crystal
# A unicorn is a **legendary animal** (see the `Legendary` module) that has been
# described since antiquity as a beast with a large, spiraling horn projecting
# from its forehead.
class Unicorn
end

# 주의: 이 주석은 클래스에 붙지 않습니다.

class Legendary
end
```

* 메서드의 주석은 메서드 요약과 메서드 상세 내용에 포함됩니다. 전자는 첫 줄만을, 후자는 문서 전체를 포함합니다. 즉,

  1. 첫 줄에는 메서드의 목적과 기능을 서술하고
  2. 그 다음에 상세 사항과 쓰임새를 보충하는 것이

선호됩니다.
예시는 다음과 같습니다.

``````crystal
# Returns the number of horns this unicorn has.
#
# ```
# Unicorn.new.horns # => 1
# ```
def horns
  @horns
end
``````

* 3인칭을 사용하세요. `Return the number of horns this unicorn has`보다 `Returns the number of horns this unicorn has`가 좋습니다.

* 매개변수 이름은 *기울여 써야* 합니다. (별표 `*` 또는 밑줄 `_` 하나로 둘러싸기)

```crystal
# Creates a unicorn with the specified number of *horns*.
def initialize(@horns = 1)
  raise "Not a unicorn" if @horns != 1
end
```

* 크리스탈 코드를 포함하는 코드 블락은 백틱 세 개로 감싸거나 스페이스 네 개로 들여쓰면 됩니다.

``````crystal
# ```
# unicorn = Unicorn.new
# unicorn.speak
# ```
``````

또는

```crystal
#     unicorn = Unicorn.new
#     unicorn.speak
```

* 프로그램 출력물 등 텍스트 블락을 표현하려면 백틱 세 개와 "text" 키워드를 이용해야 합니다.

``````crystal
# ```text
# "I'm a unicorn"
# ```
``````

* 다른 타입으로 링크를 걸려면 백틱 하나로 감싸세요.

```crystal
# the `Legendary` module
```

* 현재 문서를 달고 있는 타입의 메서드로 링크를 걸려면 `#horns` 또는 `#index(char)`과 같이 우물 정자를 쓰고 백틱으로 감싸세요.

* 다른 타입의 메서드로 링크를 거는 법은 `OtherType#method(arg1, arg2)` 또는 `OtherType#method`와 같습니다.

```crystal
# Check the number of horns with `#horns`.
# See what a unicorn would say with `Unicorn#speak`.
```

* 코드 블락에서 표현식의 값을 나타내려면 `#=>`를 사용하세요.

```crystal
1 + 2             # => 3
Unicorn.new.speak # => "I'm a unicorn"
```

* 이전 정의와 동일한 주석을 사용하려면 `ditto`를 이용하세요.

```crystal
# ditto
def number_of_horns
  horns
end
```

* `:nodoc:`을 통해 생성된 문서로부터 퍼블릭 선언을 숨길 수 있습니다. 프라이빗 그리고 프로텍티드 메서드는 항상 숨겨져 있습니다.

```crystal
class Unicorn
  # :nodoc:
  class Helper
  end
end
```

### 클래스, 모듈, 메서드에 플래그 달기

유효한 키워드가 주어지면 크리스탈은 문제 사항과 주의점을 강조하는 시각적 플래그를 자동으로 생성합니다.

지원되는 키워드는 다음과 같습니다.

- BUG
- DEPRECATED
- FIXME
- NOTE
- OPTIMIZE
- TODO

플래그 키워드는 각 줄에서 첫 단어여야 하며 모두 대문자입니다. 키워드 후에 콜론을 붙일 수 있으며, 가독성을 위해 붙이는 쪽이 권장됩니다.

``````crystal
# Makes the unicorn speak to STDOUT
#
# NOTE: Although unicorns don't normally talk, this one is special
# TODO: Check if unicorn is asleep and raise exception if not able to speak
# TODO: Create another `speak` method that takes and prints a string
def speak
  puts "I'm a unicorn"
end

# Makes the unicorn talk to STDOUT
#
# DEPRECATED: Use `speak`
def talk
  puts "I'm a unicorn"
end
``````

### 크리스탈의 코드 포매터 이용하기

크리스탈의 내장 코드 포매터를 이용해 코드뿐 아니라 문서 블락에 있는 코드 예시 또한
포맷할 수 있습니다.

현재 디렉토리의 모든 `.cr` 파일을 자동으로 포맷하는 `crystal tool format`이
호출될 때 자동으로 이루어집니다.

단일 파일을 포맷하는 법은 다음과 같습니다.

```
$ crystal tool format file.cr
```

한 디렉토리의 모든 `.cr` 파일을 포맷하는 법은 다음과 같습니다.

```
$ crystal tool format src/
```

이 툴을 이용해 코드 스타일을 통일하고 크리스탈 자체의 문서를 개선할 수 있습니다.

또한 포매터는 빠르기 때문에 파일을 일일히 포맷하지 않고 프로젝트 전채를 포맷하더라도
시간의 손실은 매우 적습니다.

### 전체 예시

``````crystal
# A unicorn is a **legendary animal** (see the `Legendary` module) that has been
# described since antiquity as a beast with a large, spiraling horn projecting
# from its forehead.
#
# To create a unicorn:
#
# ```
# unicorn = Unicorn.new
# unicorn.speak
# ```
#
# The above produces:
#
# ```text
# "I'm a unicorn"
# ```
#
# Check the number of horns with `#horns`.
class Unicorn
  include Legendary

  # Creates a unicorn with the specified number of *horns*.
  def initialize(@horns = 1)
    raise "Not a unicorn" if @horns != 1
  end

  # Returns the number of horns this unicorn has
  #
  # ```
  # Unicorn.new.horns # => 1
  # ```
  def horns
    @horns
  end

  # ditto
  def number_of_horns
    horns
  end

  # Makes the unicorn speak to STDOUT
  def speak
    puts "I'm a unicorn"
  end

  # :nodoc:
  class Helper
  end
end
``````

### 문서 생성

프로젝트의 문서를 생성하려면 `crystal doc`을 호출하세요. `doc` 디렉토리에 `doc/index.html` 진입점을 가진 문서가 생성될 것입니다.
프로젝트 루트의 `src` 디렉토리에 있는 모든 파일이 대상입니다.
