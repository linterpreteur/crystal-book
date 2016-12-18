# 동시성

## 동시성 vs. 병행성

"동시성"과 "병행성"의 정의는 종종 혼동되지만 분명 다릅니다.

동시 시스템은 같은 시간에 실행하지 않더라도 다수의 작업을 담당할 수 있는 시스템을 의미합니다. 이는 주방에서 요리를 하는 것과도 같습니다. 양파를 썰고, 튀기고, 양파를 튀기는 동안 토마토를 썰지만 이 모든 일을 동시에 하는 것은 아닙니다. 작업들 간에 시간을 분배하는 것입니다. 병행성은 한 손으로 양파를 튀기며 다른 손으로는 토마토를 써는 것과 같습니다.

이 글을 쓰는 시점에서, 크리스탈은 동시성을 지원하지만 병행성은 지원하지 않습니다. 몇 가지 작업이 실행될 수 있지만 두 개 이상의 코드가 한꺼번에 실행되지는 않으며 각 작업에 시간이 나뉘어 소비될 것입니다.

크리스탈 프로그램은 동시 표시 후 쓸기 기법을 구현하는 (현재는 [봄 GC](http://www.hboehm.info/gc/)임) 가비지 컬렉터(GC)를 제외하면 단일한 운영체제 스레드에서 실행됩니다.

### 파이버란?

동시성을 달성하기 위해 크리스탈은 파이버를 이용합니다. 파이버는 운영체제 스레드와 비슷하지만 훨씬 더 경량이고 프로세스가 내부적으로 실행을 관리합니다. 따라서 한 프로그램은 여러 파이버를 생성할 수 있으며, 크리스탈이 적당한 때에 이 파이버들을 실행시킬 것입니다.

### 이벤트 루프

I/O와 관련된 것에는 이벤트 루프가 존재합니다. 이벤트 루프는 시간을 많이 잡아먹는 작업들을 위임받으며, 이벤트 루프가 그 작업이 끝나길 기다리는 동안 프로그램은 다른 파이버를 실행할 수 있습니다. 간단한 예는 소켓으로 들어오는 데이터를 기다리는 것입니다.

### 채널

크리스탈에는 [CSP](https://en.wikipedia.org/wiki/Communicating_sequential_processes)에 영감을 받은 채널이 있습니다. 채널을 통해 메모리 공유나 락, 세마포어 같은 특수한 구조에 대해 걱정하지 않고 파이버 간에 데이터를 주고 받을 수 있습니다.

## 프로그램의 실행

프로그램이 시작될 때 최상위 코드를 실행할 메인 파이버가 생성됩니다. 그 후 다른 파이버들이 생성될 수 있습니다. 프로그램의 구성요소는 다음과 같습니다.

* 적당한 때에 파이버를 실행시키는 런타임 스케줄러.
* 파일, 소켓, 파이프, 시그널과 (예컨대 `sleep`할 때) 타이머 등 비동기 작업을 담당하는 또 다른 파이버, 이벤트 루프.
* 파이버 사이에 데이터를 통신할 채널. 런타임 스케줄러가 파이버와 채널을 조절하여 통신을 담당합니다.
* 더 이상 사용하지 않는 메모리를 청소하기 위한 가비지 컬렉터.

### 파이버

파이버는 스레드보다 경량인 실행 단위입니다. 대개는 운영체제 시스템에 주어지는 크기인 8MB의 [스택](https://en.wikipedia.org/wiki/Call_stack)을 갖는 작은 객체입니다.

스레드와 달리 파이버는 협동적입니다. 스레드는 선점형이기 때문에 운영체제가 언제라도 스레드를 인터럽트하고 다른 스레드를 실행할 수 있습니다. 하지만 파이버는 명시적으로 런타임 스케줄러에 다른 파이버로 전환하도록 메시지를 주어야 합니다. 예를 들어 대기 중인 I/O가 있다면, 파이버는 스케줄러에게 "야, 이 I/O 기다려야 되니까 다른 파이버 실행하다가 저 I/O 쓸 수 있어지면 나한테 와"라고 말하게 되는 것입니다.

협동성의 이점은 스레드를 변환하는 문맥 변환의 오버헤드가 사라진다는 것입니다.

파이버는 스레드보다 훨씬 경량입니다. 8MB를 할당받더라도 4KB의 작은 스택을 가지고 시작합니다.

64비트 기기에서는 수백만 개의 파이버를 생성할 수 있습니다. 32비트 기기에서는 그리 많지 않은 512개만을 생성할 수 있습니다. 하지만 32비트 기기는 역사의 뒤안길로 사라지고 있기 때문에, 크리스탈은 미래에 판돈을 걸고 64비트 기기에 초점을 두고 있습니다.

### 런타임 스케줄러

런타임 스케줄러는 다음 목록의 큐를 갖습니다.
* 실행할 준비가 된 파이버. 예를 들어 파이버를 생성하면 그 파이버는 실행할 준비가 되어 있습니다.
* 또 다른 파이버인, 이벤트 루프. 실행할 준비가 된 다른 파이버가 없다면 이벤트 루프는 준비된 비동기 작업이 있는지 확인하고 그 작업을 기다리는 파이버를 실행합니다. 이벤트 루프는 현재 `epoll`이나 `kqueue` 같은 다른 이벤트 메커니즘의 구현인 `libevent`로 구현되어 있습니다.
* 스스로 기다리길 선택한 파이버들. `Fiber.yield`를 호출한 경우입니다. 즉 "나는 계속 실행할 수도 있지만, 원한다면 다른 파이버 실행할 시간도 줄게"인 경우입니다.

### 데이터 통신

지금 시점에선 코드를 실행하는 스레드가 하나만 있기 때문에, 다른 파이버에서 클래스 변수에 접근하고 수정하는 것은 문제가 없습니다. 하지만 다수의 스레드(병행성)가 언어에 도입되고 나면 이 법칙이 깨질 수도 있습니다. 따라서 채널에 메시지를 보냄으로써 통신하는 방법이 권장됩니다. 내부적으로 채널에는 데이터 경쟁을 피하기 위한 락 메커니즘이 모두 구현되어 있습니다. 하지만 외부에서는 통신의 기본 요소로만 사용하기 때문에 사용자는 락을 이용할 필요가 없습니다.

## 예시 코드

### 파이버 생성

`spawn`에 블락을 넘겨서 파이버를 생성할 수 있습니다.

```crystal
spawn do
  # ...
  socket.gets
  # ...
end

spawn do
  # ...
  sleep 5.seconds
  #  ...
end
```

여기선 파이버가 두 개 있는데, 하나는 소켓에서 읽는 파이버이고 하나는 `sleep`을 하는 파이버입니다. 첫 번째 파이버가 `socket.gets` 라인에 도달하면 그 파이버는 정지하고 이벤트 루프에게 소켓에 데이터가 들어오면 파이버를 다시 실행하라는 메시지가 전달된 후 두 번째 파이버가 계속됩니다. 이 파이버는 5초 동안 슬립을 하는데, 이 경우 이벤트 루프는 5초 후에 파이버를 다시 실행하라는 메시지를 받는 것입니다. 실행할 다른 파이버가 없다면 이벤트 루프는 CPU 시간을 소모하지 않은 채로 둘 중 하나의 경우가 일어날 때까지 기다립니다.

`socket.gets`와 `sleep`이 이렇게 동작하는 이유는 런타임 스케줄러나 이벤트 루프와 직접 통신하는 방식으로 구현되어 있기 때문입니다. 놀라운 것은 없습니다. 보통 표준 라이브러리가 이런 과정을 전부 책임지기 때문에 여러분은 신경쓸 필요가 없습니다.

하지만 파이버가 곧바로 실행되는 것은 아니라는 사실에 주의하세요. 예를 들어,

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end
```

위의 코드는 출력하지 않고 바로 종료됩니다.

그 이유는 파이버는 생성되자마자 실행되는 것이 아니기 때문입니다. 따라서 위의 파이버를 생성하는 메인 파이버는 그 실행을 중지하고 프로그램이 종료되는 것입니다.

해결법 중 하나는 `sleep`을 하는 것입니다.

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

sleep 1.second
```

이 프로그램은 이제 1초 동안 "Hello!"를 출력하고 종료될 것입니다. 이는 `sleep` 호출이 메인 파이버를 1초 후에 실행하도록 조정한 뒤, 스케줄러가 "실행할 준비가 된" 다른 파이버, 즉 위의 파이버를 실행하기 때문입니다.

다른 방법은 다음과 같습니다.

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

Fiber.yield
```

여기서 `Fiber.yield`는 스케줄러에게 다른 파이버를 실행하라고 말하는 것입니다. 표준 출력이 막힐 (출력이 준비될 때까지 기다려야 한다고 시스템 호출이 말해줄) 때까지 "Hello!"를 출력한 뒤 메인 파이버가 실행되어 프로그램이 종료됩니다. 이때 표준 출력은 *아마* 막히지 않을 것이므로 프로그램이 영원히 실행될 것입니다.

생성한 파이버를 계속 실행하고 싶다면 인자 없는 `sleep`을 사용할 수 있습니다.

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

sleep
```

물론 위의 프로그램을 `spawn` 없이 반복문 만으로 작성할 수 있습니다. `sleep`은 두 개 이상의 파이버를 생성하는 경우에 더 유용합니다.

### 메서드 호출 생성

블락 대신 메서드 호출을 넘김으로써 파이버를 생성할 수도 있습니다. 다음 예시를 통해 이것이 유용한 이유를 알 수 있습니다.

```crystal
i = 0
while i < 10
  spawn do
    puts(i)
  end
  i += 1
end

Fiber.yield
```

위 프로그램은 "10"을 열 번 출력합니다. 문제는 모든 파이버가 참조하는 변수 `i`가 하나만 있으며, `Fiber.yield`가 실행될 때 그 값은 10이라는 것입니다.

해결책으로 다음과 같이 할 수도 있습니다.

```crystal
i = 0
while i < 10
  proc = ->(x : Int32) do
    spawn do
      puts(x)
    end
  end
  proc.call(i)
  i += 1
end

Fiber.yield
```

이제 제대로 동작합니다. 프록(http://crystal-lang.org/api/Proc.html)을 생성하고 `i`를 넘겨서 호출했기 때문에, 값이 복사되어 생성된 파이버가 그 사본을 받게 된 것입니다.

쓸데없는 보일러플레이트 코드를 피하기 위해 표준 라이브러리에는 호출 표현식을 받아서 위와 같이 써주는 `spawn` 매크로가 있습니다. 따라서, 다음과 같이 쓸 수 있습니다.

```crystal
i = 0
while i < 10
  spawn puts(i)
  i += 1
end

Fiber.yield
```

이는 반복에 따라 변하는 지역변수를 사용할 때 가장 유용합니다. 블락 인자는 다른 경우입니다. 예컨대 다음 코드는 제대로 동작합니다.

```crystal
10.times do |i|
  spawn do
    puts i
  end
end

Fiber.yield
```

### 파이버를 생성하여 완료될 때까지 기다리기

이렇게 채널을 쓸 수 있습니다.

```crystal
channel = Channel(Nil).new

spawn do
  puts "Before send"
  channel.send(nil)
  puts "After send"
end

puts "Before receive"
channel.receive
puts "After receive"
```

출력은 다음과 같습니다.

```text
Before receive
Before send
After receive
```

우선, 프로그램은 파이버를 생성하고 일단 실행하지 않습니다. 그리고 `channel.receive`를 호출하면 메인 파이버는 실행을 멈추고 생성된 파이버가 실행됩니다. 

`channel.send(nil)`이 호출되면 값을 기다리던 `channel.receive`에서 실행이 계속됩니다. 그러면 메인 파이버가 실행을 계속한 후 종료됩니다. 따라서 다른 파이버는 "After send"를 출력할 기회조차 없습니다.

위 예시에서 우리는 파이버가 끝났다는 것만을 알리기 위해 `nil`을 사용했습니다. 파이버 간에 값을 전달하기 위해 채널을 사용할 수도 있습니다.

```crystal
channel = Channel(Int32).new

spawn do
  puts "Before first send"
  channel.send(1)
  puts "Before second send"
  channel.send(2)
end

puts "Before first receive"
value = channel.receive
puts value # => 1

puts "Before second receive"
value = channel.receive
puts value # => 2
```

출력은 다음과 같습니다.

```text
Before first receive
Before first send
1
Before second receive
Before second send
2
```

프로그램이 `receive`를 실행할 때 그 파이버가 실행을 멈추고 다른 파이버가 실행된다는 것에 주의하시길 바랍니다. `send`가 실행되면  그 채널을 기다리고 있던 파이버가 다시 실행됩니다.

여기서는 리터럴 값을 보내고 있지만, 파일을 읽거나 소켓에서 받는 등 다른 방법으로 값을 받을 수도 있습니다. 이 파이버가 I/O를 기다리는 동안 다른 파이버가 실행될 수 있을 것입니다. 그 값이 채널을 통해 보내지고 나면 메인 파이버가 받을 수 있습니다.

```crystal
require "socket"

channel = Channel(String).new

spawn do
  server = TCPServer.new("0.0.0.0", 8080)
  socket = server.accept
  while line = socket.gets
    channel.send(line)
  end
end

spawn do
  while line = gets
    channel.send(line)
  end
end

3.times do
  puts channel.receive
end
```

위 프로그램은 파이버를 두 개 생성합니다. 첫 번째는 TCP 서버를 만들고, 연결을 하나 받은 후 읽은 글자를 줄마다 채널로 보냅니다. 두 번째 파이버는 표준 입력에서 글자를 읽습니다. 메인 파이버는 소켓을 통해서든 표준 입력을 통해서든 채널로 보내진 값을 세 개 읽은 후 프로그램을 종료합니다. `gets` 호출은 파이버를 멈추고 이벤트 루프에게 데이터가 들어온 후 거기서 다시 시작하라는 메시지를 보낼 것입니다.

마찬가지로 여러 파이버의 연산을 기다리다가 값을 하나로 모을 수도 있습니다.

```crystal
channel = Channel(Int32).new

10.times do |i|
  spawn do
    channel.send(i * 2)
  end
end

sum = 0
10.times do
  sum += channel.receive
end
puts sum # => 90
```

물론 생성된 파이버 안에서 `receive`를 사용할 수도 있습니다.

```crystal
channel = Channel(Int32).new

spawn do
  puts "Before send"
  channel.send(1)
  puts "After send"
end

spawn do
  puts "Before receive"
  puts channel.receive
  puts "After receive"
end

puts "Before yield"
Fiber.yield
puts "After yield"
```

출력은 다음과 같습니다.

```text
Before yield
Before send
Before receive
1
After receive
After send
After yield
```

이때 `channel.send`가 제일 먼저 실행되지만, 값을 기다리는 곳이 아직 없기 때문에 다른 파이버가 실행됩니다. 두 번째 파이버가 실행되면 채널에 값이 있으므로 값을 꺼낸 다음 실행이 계속됩니다. `Fiber.yield`는 파이버를 실행 큐의 마지막에 두기 때문에 첫 번째 파이버가 먼저 실행되고, 메인 파이버가 다음에 실행됩니다.

### 버퍼가 있는 채널

지금까지의 예시는 버퍼가 없는 채널을 사용했습니다. 채널에 값을 보내면, 그 채널을 기다리고 있던 파이버가 있다면 그 파이버가 바로 실행되었습니다.

버퍼가 있는 채널을 사용한다면 `send`를 호출하더라도 버퍼가 가득 차 있지 않다면 그 파이버로 바뀌지 않게 됩니다.

```crystal
# 크기 2짜리 버퍼가 있는 채널
channel = Channel(Int32).new(2)

spawn do
  puts "Before send 1"
  channel.send(1)
  puts "Before send 2"
  channel.send(2)
  puts "Before send 3"
  channel.send(3)
  puts "After send"
end

3.times do |i|
  puts channel.receive
end
```

출력은 다음과 같습니다.

```
Before send 1
Before send 2
Before send 3
1
2
After send
3
```

처음 두 번은 값을 보내더라도 다른 파이버로 바뀌지 않습니다. 하지만 세 번째로 보낸 후에는 버퍼가 가득 차기 때문에 메인 파이버로 실행이 넘어갑니다. 이때, 메인 파이버가 값을 두 개 받은 후에 채널이 비워집니다. 세 번째 `receive`에서 메인 파이버는 실행을 멈추고 다른 파이버가 실행됩니다. 세 번째 값이 보내지고, 실행이 종료됩니다.