# 성능

다음 팁을 이용해 스피드와 메모리 측면에서 프로그램의 성능을 최대로 끌어올릴 수 있습니다.

## 조기 최적화

도날드 크누스(Donald Knuth)는 말한 바 있습니다.

> 우리는 97% 정도의 작은 효율성에 대해서는 잊어야 한다. 조기 최적화가 만악의 근원이다. 하지만 치명적인 3%에서의 기회를 놓쳐서는 안 된다.

그렇다고 프로그램을 작성하는 도중 의미론적으로 동일하면서 조금만 바꿔도 빨라진다는 것을 깨달았을 때 그 기회를 놓쳐서는 안 됩니다.

그리고 꼭 병목이 어디인지 알아내기 위해 프로그램을 항상 프로파일링하세요. Mac OSX에서는 XCode에 내장된 [Instruments Time Profiler](https://developer.apple.com/library/prerelease/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/Instrument-TimeProfiler.html)를 사용할 수 있습니다. 리눅스에서는 C/C++ 프로그램을 프로파일링 할 수 있는 [gprof](https://sourceware.org/binutils/docs/gprof/) 같은 프로그램이라면 작동할 것입니다.

반드시 `--release` 플래그를 이용하여 최적화를 켠 후에 프로그램을 프로파일링 하는 걸 잊지 마세요.

## 메모리 할당을 피할 것

프로그램을 만들 때 할 수 있는 최고의 최적화 기술 중 하나는 쓸데없는 메모리 할당을 피하는 것입니다. **클래스**의 인스턴스를 생성할 때 메모리 할당이 일어납니다. 이때 힙 메모리를 할당하게 됩니다. **구조체**의 인스턴스를 생성할 때는 스택 메모리를 사용하기 때문에 성능이 영향을 받지 않습니다. 스택과 힙 메모리가 무엇이 다른지 모르신다면 꼭 [이곳](https://www.google.com.ar/webhp?sourceid=chrome-instant&ion=1&espv=2&ie=UTF-8#q=stack%20vs%20heap%20memory)을 참조하세요.

힙 메모리를 할당하는 것은 느리고, 그 메모리를 나중에 해제해야 하기 때문에 가비지 컬렉터(GC)에 더 부담을 줍니다.

힙 메모리 할당을 피하기 위한 방법이 몇 가지 있습니다. 표준 라이브러리는 이에 대해 프로그래머를 돕기 위한 방식으로 설계되어 있습니다.

### IO에 출력할 때 임시 문자열을 만들지 말 것

표준 출력에 숫자를 쓰기 위하여 다음과 같이 할 수 있습니다.

```
puts 123
```

많은 프로그래밍 언어에서의 구현은, 객체를 문자열로 바꾸는 `to_s` 같은 메서드를 호출하고 그 문자열을 표준 출력에 출력하는 것입니다. 문제 없이 작동하는 방식이지만 결점이 있습니다. 금방 버릴 임시 문자열을 힙 메모리에 쓰고 생성한다는 것입니다. 이는 힙 메모리 할당을 수반하므로 GC에 좀 더 부담이 됩니다.

크리스탈에서 `puts`는 객체에 `to_s(io)`를 호출하여 문자열을 출력하려는 IO에 그 객체를 넘겨줄 것입니다.

따라서, 다음은 반드시 피해야 합니다.

```
puts 123.to_s
```

이는 임시 문자열을 생성합니다. 항상 객체를 IO에 직접 집어넣으세요.

타입을 작성할 때는 반드시 `to_s`뿐 아니라 `to_s(io)` 또한 오버라이드 하세요. 그리고 그 메서드에서 임시 문자열을 가급적이면 생성하지 않는 것이 좋습니다.

```crystal
class MyClass
  # 좋은 예
  def to_s(io)
    # 임시 문자열 없이 "1, 2"를 IO에 추가함
    x = 1
    y = 2
    io << x << ", " << y
  end

  # 나쁜 예
  def to_s(io)
    x = 1
    y = 2
    # 문자열 보간을 사용하면 임시 문자열이 생기므로
    # 가급적 삼가야 함
    io << "#{x}, #{y}"
  end
end
```

임시 문자열을 반환하는 대신 IO에 붙여넣는 이 철학은 JSON이나 YAML API 등 다른 API에도 적용됩니다. 이때 데이터를 IO에 직접 쓰기 위해 `to_json(io)`와 `to_yaml(io)`를 정의해야 합니다. 여러분의 API 정의에도 이 전략을 이용해야 합니다.

시간을 비교해 봅시다.

```crystal
# io_benchmark.cr
require "benchmark"

io = MemoryIO.new

Benchmark.ips do |x|
  x.report("to_s 없이") do
    io << 123
    io.clear
  end

  x.report("to_s 사용") do
    io << 123.to_s
    io.clear
  end
end
```

출력물은 다음과 같습니다.

```
$ crystal io_benchmark.cr --release
without to_s  69.71M (± 6.14%)       fastest
   with to_s  14.68M (± 2.38%)  4.75× slower
```

시간뿐만 아니라 메모리 사용의 측면에서 또한 향상되었다는 점을 기억해 둡시다.

### 임시 객체를 계속 만들지 말 것

다음 코드를 읽어보세요.

```crystal
lines_with_language_reference = 0
while line = gets
  if ["크리스탈", "루비", "자바"].any? { |string| line.includes?(string) }
    lines_with_language_reference += 1
  end
end
puts "크리스탈, 루비, 자바가 나오는 줄: #{lines_with_language_reference}"
```

위의 프로그램은 작동하긴 하지만 엄청난 성능 문제가 있습니다. 반복을 돌 때마다 `["crystal", "ruby", "java"]` 배열이 새로 생성됩니다.
잊지 마세요. 배열 리터럴은 인스턴스를 생성하고 값을 넣는 작업을 위한 문법적 편의 사항일 뿐이며 반복이 일어날 때마다 매번 배열이 생성될 것입니다.

해결법은 두 가지가 있습니다.

1. 튜플을 사용합니다. 위의 프로그램에서 `{"crystal", "ruby", "java"}`를 사용하면 똑같아 보이지만, 튜플은 힙 메모리를 쓰지 않기 때문에
더 빠르고, 메모리를 더 적게 먹고, 컴파일러가 프로그램을 최적화할 확률이 더 높습니다.

  ```crystal
  lines_with_language_reference = 0
  while line = gets
    if {"크리스탈", "루비", "자바"}.any? { |string| line.includes?(string) }
      lines_with_language_reference += 1
    end
  end
  puts "크리스탈, 루비, 자바가 나오는 줄: #{lines_with_language_reference}"
  ```

2. 배열을 상수로 바꿉니다.

  ```crystal
  LANGS = ["크리스탈", "루비", "자바"]

  lines_with_language_reference = 0
  while line = gets
    if LANGS.any? { |string| line.includes?(string) }
      lines_with_language_reference += 1
    end
  end
  puts "크리스탈, 루비, 자바가 나오는 줄: #{lines_with_language_reference}"
  ```

튜플을 사용하는 것이 더 권장됩니다.

반복문에서의 명시적 배열 리터럴은 임시 객체가 생겨나는 한 경우이지만, 메서드 호출을 통해서도 임시 객체가 생길 수 있습니다. 예를 들어 `Hash#keys`는 호출될 때마다 키를 담은 새로운 배열을 반환합니다. 대신에 `Hash#each_key`, `Hash#has_key?` 등 다른 메서드를 사용할 수 있습니다.

### 가능하면 구조체를 이용할 것

타입을 **클래스**가 아니라 **구조체**로 선언한다면 객체 생성은 스택 메모리를 사용하므로, 힙 메모리를 사용할 때보다 더욱 저렴하고 GC에 부담을 주지 않습니다.

하지만 항상 구조체를 사용할 수는 없습니다. 구조체는 값으로 전달되기 때문에 메서드에 구조체를 넘기고 메서드가 그 구조체를 변경한다고 해도 호출하는 쪽에서는 변화를 볼 수 없기 때문에 버그가 생길 수 있습니다. 최선의 방식은 불변 객체, 특히 객체가 작은 경우에 대해서만 구조체를 사용하는 것입니다.

예시는 다음과 같습니다.

```crystal
# class_vs_struct.cr
require "benchmark"

class PointClass
  getter x
  getter y

  def initialize(@x : Int32, @y : Int32)
  end
end

struct PointStruct
  getter x
  getter y

  def initialize(@x : Int32, @y : Int32)
  end
end

Benchmark.ips do |x|
  x.report("class") { PointClass.new(1, 2) }
  x.report("struct") { PointStruct.new(1, 2) }
end
```

이는 다음을 출력합니다.

```
$ crystal class_vs_struct.cr --release
 class  28.17M (± 2.86%) 15.29× slower
struct 430.82M (± 6.58%)       fastest
```

## 문자열 순회

크리스탈의 문자열은 항상 UTF-8로 인코드한 바이트를 담습니다. UTF-8은 가변 길이 인코딩이기 때문에, ASCII 범위의 문자가 바이트 하나로 표현될지라도 한 문자는 여러 바이트에 의해 표현될 수 있습니다. 때문에 `String#[]`을 이용해 문자열을 인덱싱 하는 것은 주어진 위치의 문자를 찾기 위해 바이트가 매번 디코드되어야 하므로 `O(1)` 작업이 아닙니다. 크리스탈의 문자열은, 모든 문자가 ASCII라는 것을 알 때에 한해 `String#[]`을 `O(1)`으로 구현하는 최적화 기법을 사용합니다. 하지만 일반적으로는 그러지 못합니다.

따라서 이 방식의 문자열의 순회은 최적이 아니며 실제로는 `O(n^2)`의 차수를 갖습니다.

```crystal
string = ...
while i < string.size
  char = string[i]
  # ...
end
```

위의 코드는 두 번째 문제가 있습니다. 문자열의 크기(`size`)와 문자열에 있는 바이트의 숫자(`bytesize`)는 다르기 때문에 문자열의 크기를 계산하는 것 또한 느리다는 것입니다. 하지만 문자열이 크기를 계산하고 나면 그 결과를 캐시합니다. 그래도 느린 것은 `String#[]` 때문입니다.

대신 사용할 수 있는 것은 순회 메서드(`each_char`, `each_byte`, `each_codepoint`)를 사용하거나, 더욱 저수준인 `Char::Reader` 구조체를 사용하는 것입니다. 예를 들어 `each_char`은 다음과 같이 사용합니다.

```crystal
string = ...
string.each_char do |char|
  # ...
end
```
