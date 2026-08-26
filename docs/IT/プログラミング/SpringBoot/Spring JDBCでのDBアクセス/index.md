---
tags:
- SpringBoot
- Spring JDBCでのDBアクセス
---

# Spring JDBCでのDBアクセス
## Spring JDBCの特徴
SpringJDBCはJDBCをラップしたクラス。  
ConnectionオブジェクトやPreparedStatementオブジェクトの取得、SQLの実行、ConnectionオブジェクトやPreparedStatementオブジェクトのクローズ、SQLExceptionオブジェクトがスローされた際にエラーの原因ごとの日チェック例外に変換してスローするといった処理を行ってくれる。

## JdbcTemplateクラスの使い方
### インスタンス化
データソースをコンストラクタに渡すだけ
```java
DataSouce dataSouce = ...
JdbcTemplate jdbcTemplate = new JdbcTemplate(dataSouce);
```

### 検索系の処理
#### 1カラムを取得
```java title="1レコードの1カラムを取得"
String title = jdbcTemplate.queryForObject(
    "SELECT title FROM training WHERE id=?", String.class, "t01");
```
```java title="複数のパラメータ指定"
String title = jdbcTemplate.queryForObject(
    "SELECT title FROM training WHERE id=? AND title=?", String.class, "t01", "ビジネスマナー研修");
```
```java title="日付型で取得"
LocalDateTime startDateTime = jdbcTemplate.queryForObject(
    "SELECT start_date_time FROM training WHERE id=?", LocalDateTime.class, "t01",);
```
```java title="複数レコードの1カラムを取得"
List<Integer> reserveds = jdbcTemplate.queryForList(
    "SELECT title FROM training", Integer.class);
```

#### レコードをMapオブジェクトで取得
```java title="1レコードをMapで取得"
Map<String, Object> map = jdbcTemplate.queryForMap(
    "SELECT * FROM training WHERE id=?", "t01");
```
```java title="複数レコードをMapで取得"
List<Map<String, Object>> maps = jdbcTemplate.queryForMap(
    "SELECT * FROM training");
```

#### レコードをEntityオブジェクトで取得
```java title="複数レコードをMapで取得"
public class Training {
    private String id;
    private String title;
    private LocalDateTime startDateTime;
    private LocalDateTime endDateTime;
    private Integer reserved;
    private Integer capacity;
    ... Getter・Setterメソッド
}
```

- カラムに対応するフィールドを持たせて、Getter、Setterを定義する。
- フィールド名はSpringJDBCの機能を使って自動的に絡むと紐づけられる。
- カラム名がスネークケース、フィールド名がキャメルケースであっても紐づけられる。
- もし、コンストラクタで全ての値を設定する場合はSetterは不要。

```java title="1レコードをEntityに変換して取得"
Training training = jdbcTemplate.queryForObject("SELECT * FROM training WHERE id=?", new DataClassRowMapper<>(Training.class), "t01")
```
```java title="複数レコードをEntityに変換して取得"
List<Training> trainings = jdbcTemplate.queryForObject("SELECT * FROM training", new DataClassRowMapper<>(Training.class))
```

### 更新系の処理
#### INSERT文
```java title="INSERT文の発行"
int count = jdbcTemplate.update(
    "INSERT INTO training VALUES (?,?,?,?,?,?)",
    "t03", "Spring研修", startDateTime, endDateTime, 0, 8)
```
#### UPDATE文
```java title="UPDATE文の発行"
int count = jdbcTemplate.update(
    "UPDATE SET title=?, start_date_time=?, end_date_time=?, reserved=?, capacity=?, WHERE id=?)",
    "Spring研修", startDateTime, endDateTime, 0, 8, "t03")
```

#### DELETE文
```java title="DELETE文の発行"
int count = jdbcTemplate.update(
    "DELETE FROM training WHERE id=?", "t03")
```

## JdbcTemplateクラスのBean定義
DIコンテナにJdbcTemplateをBeanとして登録すればオブジェクトを色々なところで使いまわせる

```java title="DELETE文の発行"
@Configuration
public class FooConfig {
    @Bean
    public JdbcTemplate jdbcTemplate(DataSouce dataSource) {
        return new JdbcTemplate(dataSource);
    }
}
```