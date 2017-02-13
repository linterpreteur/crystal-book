# 커넥션 풀

커넥션이 열린다는 것은 일반적으로 TCP 커넥션 혹은 소켓이 열린다는 것을 의미합니다. 소켓은 구문을 하나씩 처리합니다. 다수의 쿼리를 동시에 실행하거나 데이터베이스로의 요청을 병행적으로 처리해야 한다면 여러 개의 커넥션을 활성화해야 합니다.

데이터베이스는 데이터베이스를 사용할 프로그램과는 독립적인 서비스이므로, 커넥션이 끊기고 데이터베이스가 재시작되는 등 프로그램에서 예상치 못하는 다양한 상황이 있을 수 있습니다.

이 문제를 해결하기 위해서는 커넥션 풀이 깔끔한 선택입니다.

`crystal-db`로 데이터베이스를 열면 커넥션 풀이 생성됩니다. `DB.open`은 커넥션 풀을 하나뿐이 아니라 모두 다루는 `DB::Database` 객체를 반환합니다.

```crystal
DB.open("mysql://root@localhost/test") do |db|
  # db is a DB::Database
end
```

`db.query`, `db.exec`, `db.scalar` 등을 이용해 쿼리를 실행할 때의 알고리듬은 다음과 같습니다.

1. 풀에서 사용할 수 있는 커넥션을 찾음.
   1. 생성해야 하는 경우이며 그럴 수 있다면 하나를 생성.
   2. 풀에서 새 커넥션을 생성할 수 없을 경우 커넥션을 사용할 수 있을 때까지 대기.
      1. 하지만 너무 오래 걸리면 취소.
2. 풀에서 해당 커넥션을 꺼내옴.
3. SQL 명령어를 실행.
4. `DB::ResultSet`가 코드 블락에 넘겨지지 않았다면 커넥션을 풀로 되돌려줌. 넘겨진 경우, ResultSet이 닫힐 때 커넥션이 풀로 반환됨.
5. 구문 결과를 반환.

커넥션이 생성될 수 없거나 구문이 실행되는 동안 커넥션이 끊길 경우 위 과정이 반복됩니다.

> 재시도는 `DB::Database`를 이용해 구문이 보내질 경우에만 해당됩니다. `DB::Conenction` 또는 `DB::Transaction`으로 구문이 보내졌다면 특정한 커넥션 객체를 사용해야 하므로 실패한 경우에도 다시 시도되지 않습니다.

## 설정

커넥션 URI에 쿼리 문자열로 인수를 넘겨서 풀의 행동 방식을 설정할 수 있습니다.

| Name | Default value |
| :--- | :--- |
| initial\_pool\_size | 1 |
| max\_pool\_size | 0 \(unlimited\) |
| max\_idle\_pool\_size | 1 |
| checkout\_timeout | 5.0 \(seconds\) |
| retry\_attempts | 1 |
| retry\_delay | 1.0 \(seconds\) |

`DB::Database`가 열렸을 때 `initial_pool_size`개의 커넥션이 생성됩니다. 커넥션 개수는 `max_pool_size`를 넘을 수 없습니다. 커넥션을 풀에 되돌릴 때 `max_idel_pool_size`만큼 놀고 있는 커넥션이 있다면, 되돌리려고 했던 커넥션은 닫히게 됩니다.

`max_pool_size`에 도달했는데 커넥션이 필요한 경우 이미 존재하는 커넥션 중 하나가 사용할 수 있어질 때까지 `checkout_timeout`초까지 기다립니다.

커넥션이 끊기거나 생성되지 못한 경우 `retry_delay`초 간격으로 `retry_attempts`회까지 재시도합니다.

## 예제

다음 프로그램은 현재 시간을 MySQL로부터 가져와 출력합니다. 몇 초 동안 커넥션이 끊기거나 서버가 내려가도 예외가 일어나지 않고 프로그램이 계속 실행됩니다.

```crystal
# 파일 이름: sample.cr
require "mysql"

DB.open "mysql://root@localhost?retry_attempts=8&retry_delay=3" do |db|
  loop do
    pp db.scalar("SELECT NOW()")
    sleep 0.5
  end
end
```

```
$ crystal sample.cr
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:57
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:57
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:58
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:58
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:59
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:59
# stop mysql server for some seconds
db.scalar("SELECT NOW()") # => 2016-12-16 16:37:06
db.scalar("SELECT NOW()") # => 2016-12-16 16:37:06
db.scalar("SELECT NOW()") # => 2016-12-16 16:37:07
```