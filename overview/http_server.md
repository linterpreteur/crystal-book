# HTTP 서버

좀 더 재미있는 예제, HTTP 서버입니다.

```crystal
require "http/server"

server = HTTP::Server.new(8080) do |context|
  context.response.content_type = "text/plain"
  context.response.print "야호! 지금 시간은 #{Time.now}"
end

puts "Listening on http://127.0.0.1:8080"
server.listen
```

문서를 모두 읽고 난 후에는 위 코드를 이해할 수 있겠지만, 우리는 이미 몇 가지를 알아챌 수 있습니다.

* 다른 파일의 코드를 이용하기 위해 [require](../syntax_and_semantics/requiring_files.html)를 이용합니다.

    ```ruby
    require "http/server"
    ```
* 타입을 지정하지 않고도 [지역 변수](../syntax_and_semantics/local_variables.html)를 정의할 수 있습니다.

    ```ruby
    server = HTTP::Server.new ...
    ```

* 객체에 [메서드](../syntax_and_semantics/classes_and_methods.html)를 호출(하거나 메시지를 전송)함으로써 프로그램을 작성합니다.

    ```ruby
    HTTP::Server.new(8080) ...
    ...
    Time.now
    ...
    puts "http://127.0.0.1:8080에서 작동 중"
    ...
    server.listen
    ```

* 코드 [블락](../syntax_and_semantics/blocks_and_procs.html)을 이용하여 코드를 재사용하고 함수형 세계의 특징을 일부 가져올 수 있습니다.

    ```ruby
    HTTP::Server.new(8080) do |context|
      ...
    end
    ```

* 문자열 보간을 이용해 다른 객체를 포함한 문자열을 손쉽게 만들 수 있습니다. 뿐만 아니라 크리스탈 언어에는 문자열, 해시, 범위, 튜플 등을 생성할 수 있는 [문법](../syntax_and_semantics/literals.html)도 있습니다.

    ```ruby
    "야호! 지금 시간은 #{Time.now}"
    ```


