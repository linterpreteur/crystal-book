# 데이터베이스

관계형 데이터베이스에 접근하기 위해서는 사용하고자 하는 데이터베이스 서버를 위한 샤드가 필요합니다. [crystal-lang/crystal-db](https://github.com/crystal-lang/crystal-db) 패키지는 다양한 드라이버를 통합하는 API를 제공합니다.

다음 패키지는 모두 crystal-db 패키지와 호환됩니다.

* SQLite는 [crystal-lang/crystal-sqlite3](https://github.com/crystal-lang/crystal-sqlite3)
* MySQL과 MariaDB는 [crystal-lang/crystal-mysql](https://github.com/crystal-lang/crystal-mysql)
* Postgres는 [will/crystal-pg](https://github.com/will/crystal-pg)

이 항목에서는 crystal-db의 API와 Postgres, MySQL, SQLite의 차이로 인해 변경해 주어야 하는 SQL 명령어를 보겠습니다.

일부 드라이버에는 Postgres의 `LISTEN`과 `NOTIFY` 같은 추가 기능이 있을 수도 있습니다.

## 샤드 설치

위 목록에서 적절한 드라이버를 골라 `shard.yml`에 샤드를 추가합니다.

`crystal-lang/crystal-db`를 명시적으로 추가할 필요는 없습니다.

이 항목에서는 `crystal-lang/crystal-mysql`을 사용합니다.

```yaml
dependencies:
  mysql:
    github: crystal-lang/crystal-mysql
```

## 데이터베이스 열기

`DB.open`로 연결 URI를 통해 데이터베이스에 손쉽게 연결할 수 있습니다. URI의 스키마가 사용될 드라이버를 결정합니다. 다음 예제는 root 사용자로 비밀번호 blank를 가지고 test란 이름의 로컬 MySQL 데이터베이스에 연결하는 예제입니다.

```crystal
require "db"
require "mysql"

DB.open "mysql://root@localhost/test" do |db|
  # ... db로 쿼리를 보냄
end
```

다른 연결 URI도 사용할 수 있습니다.

* `sqlite3:///path/to/data.db`
* `mysql://user:password@server:port/database`
* `postgres://server:port/database`

`Database#close`를 호출한다는 전제 하에 블락을 받지 않는 `DB.open`을 사용할 수도 있습니다.

```crystal
require "db"
require "mysql"

db = DB.open "mysql://root@localhost/test"
begin
  # ... db로 쿼리를 보냄
ensure
  db.close
end
```

## 실행

`Database#exec`으로 SQL 구문을 실행합니다.

```crystal
db.exec "create table contacts (name varchar(30), age int)"
```

데이터를 보낼 때 인자를 넘김으로써 SQL 인젝션을 방지할 수 있습니다.

```crystal
db.exec "insert into contacts values (?, ?)", "John", 30
db.exec "insert into contacts values (?, ?)", "Sarah", 33
```

주의: pg 드라이버를 사용할 때는 `?` 대신 `$1`, `$2` 등을 사용합니다.

## 쿼리

쿼리를 실행하여 결과를 얻기 위해서는 `Database#query`를 이용하며, `Database#exec`에서와 같은 방식으로 인자를 사용합니다.

`Database#query`는 `ResultSet`를 반환하며 `ResultSet`은 사용된 후 연결을 닫야 합니다. `Database#open`과 마찬가지로 블락을 넘겨 호출한다면 `ResultSet`은 묵시적으로 닫힙니다.

```crystal
db.query "select name, age from contacts order by age desc" do |rs|
  rs.each do
    # ... ResultSet의 각 행에 대하여 실행
  end
end
```

데이터베이스로부터 값을 읽을 때에는 크리스탈이 사용할 만한 타입 정보가 없습니다. 데이터베이스에서 얻을 수 있는 데이터의 타입 `T`를 넘겨 `rs.read(T)`를 호출해야 합니다.

```crystal
db.query "select name, age from contacts order by age desc" do |rs|
  rs.each do
    name = rs.read(String)
    age = rs.read(Int32)
    puts "#{name} (#{age})"
    # => 영희 (33)
    # => 홍길동 (30)
  end
end
```

`#query`를 기반으로 구현된 간편한 메서드들이 다수 있습니다..

한 번에 여러 열을 읽을 수도 있습니다.

```crystal
name, age = rs.read(String, Int32)
```

또는 한 행만을 읽을 수 있습니다.

```crystal
name, age = db.query_one "select name, age from contacts order by age desc limit 1", as: { String, Int32 }
```

ResultSet을 명시적으로 다루지 않으면서도 스칼라 값을 읽을 수 있습니다.

```crystal
max_age = db.scalar "select max(age) from contacts"
```

데이터베이스에서 구문을 실행하는 메서드들은 전부 `DB::QueryMethods`에 정의되어 있습니다.