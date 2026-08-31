---
tags:
  - Tips
---
application.propertiesに以下のような設定を入れると、自動で実行SQLのログを出してくれる。

# SQLのログの出し方。
```properties title="application.properties"
logging.level.org.springframework.jdbc.core.JdbcTemplate=DEBUG
```

```
C:\Users\hirok\.jdks\temurin-25.0.4\bin\java.exe "-javaagent:C:\Program Files\JetBrains\IntelliJ IDEA Community Edition 2023.2.2\lib\idea_rt.jar=57076" -Dfile.encoding=UTF-8 -Dsun.stdout.encoding=UTF-8 -Dsun.stderr.encoding=UTF-8 -classpath C:\Users\hirok\work\spring-book-src-master\0801-training-boot\target\classes;C:\Users\hirok\work\spring-book-src-master\0001-training-common\target\classes;C:\Users\hirok\.m2\repository\org\springframework\boot\spring-boot-starter-jdbc\3.0.1\spring-boot-starter-jdbc-3.0.1.jar;C:\Users\hirok\.m2\repository\org\springframework\boot\spring-boot-starter\3.0.1\spring-boot-starter-3.0.1.jar;C:\Users\hirok\.m2\repository\org\springframework\boot\spring-boot\3.0.1\spring-boot-3.0.1.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-context\6.0.3\spring-context-6.0.3.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-aop\6.0.3\spring-aop-6.0.3.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-expression\6.0.3\spring-expression-6.0.3.jar;C:\Users\hirok\.m2\repository\org\springframework\boot\spring-boot-autoconfigure\3.0.1\spring-boot-autoconfigure-3.0.1.jar;C:\Users\hirok\.m2\repository\org\springframework\boot\spring-boot-starter-logging\3.0.1\spring-boot-starter-logging-3.0.1.jar;C:\Users\hirok\.m2\repository\ch\qos\logback\logback-classic\1.4.5\logback-classic-1.4.5.jar;C:\Users\hirok\.m2\repository\ch\qos\logback\logback-core\1.4.5\logback-core-1.4.5.jar;C:\Users\hirok\.m2\repository\org\apache\logging\log4j\log4j-to-slf4j\2.19.0\log4j-to-slf4j-2.19.0.jar;C:\Users\hirok\.m2\repository\org\apache\logging\log4j\log4j-api\2.19.0\log4j-api-2.19.0.jar;C:\Users\hirok\.m2\repository\org\slf4j\jul-to-slf4j\2.0.6\jul-to-slf4j-2.0.6.jar;C:\Users\hirok\.m2\repository\jakarta\annotation\jakarta.annotation-api\2.1.1\jakarta.annotation-api-2.1.1.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-core\6.0.3\spring-core-6.0.3.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-jcl\6.0.3\spring-jcl-6.0.3.jar;C:\Users\hirok\.m2\repository\org\yaml\snakeyaml\1.33\snakeyaml-1.33.jar;C:\Users\hirok\.m2\repository\com\zaxxer\HikariCP\5.0.1\HikariCP-5.0.1.jar;C:\Users\hirok\.m2\repository\org\slf4j\slf4j-api\2.0.6\slf4j-api-2.0.6.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-jdbc\6.0.3\spring-jdbc-6.0.3.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-beans\6.0.3\spring-beans-6.0.3.jar;C:\Users\hirok\.m2\repository\org\springframework\spring-tx\6.0.3\spring-tx-6.0.3.jar;C:\Users\hirok\.m2\repository\com\h2database\h2\2.1.214\h2-2.1.214.jar com.example.training.TrainingApplication

  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::                (v3.0.1)

2026-08-28T21:12:42.107+09:00  INFO 27644 --- [           main] c.example.training.TrainingApplication   : Starting TrainingApplication using Java 25.0.4 with PID 27644 (C:\Users\hirok\work\spring-book-src-master\0801-training-boot\target\classes started by hirok in C:\Users\hirok\work\spring-book-src-master)
2026-08-28T21:12:42.110+09:00  INFO 27644 --- [           main] c.example.training.TrainingApplication   : No active profile set, falling back to 1 default profile: "default"
2026-08-28T21:12:42.842+09:00  INFO 27644 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Starting...
2026-08-28T21:12:43.018+09:00  INFO 27644 --- [           main] com.zaxxer.hikari.pool.HikariPool        : HikariPool-1 - Added connection conn0: url=jdbc:h2:mem:f8950d05-3408-4bc9-aa05-f65fb3aac677 user=SA
2026-08-28T21:12:43.019+09:00  INFO 27644 --- [           main] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Start completed.
2026-08-28T21:12:43.192+09:00  INFO 27644 --- [           main] c.example.training.TrainingApplication   : Started TrainingApplication in 1.466 seconds (process running for 1.792)
2026-08-28T21:12:43.199+09:00 DEBUG 27644 --- [           main] o.s.jdbc.support.JdbcTransactionManager  : Creating new transaction with name [com.example.training.service.ReservationServiceImpl.reserve]: PROPAGATION_REQUIRED,ISOLATION_DEFAULT
2026-08-28T21:12:43.200+09:00 DEBUG 27644 --- [           main] o.s.jdbc.support.JdbcTransactionManager  : Acquired Connection [HikariProxyConnection@2055019335 wrapping conn0: url=jdbc:h2:mem:f8950d05-3408-4bc9-aa05-f65fb3aac677 user=SA] for JDBC transaction
2026-08-28T21:12:43.202+09:00 DEBUG 27644 --- [           main] o.s.jdbc.support.JdbcTransactionManager  : Switching JDBC Connection [HikariProxyConnection@2055019335 wrapping conn0: url=jdbc:h2:mem:f8950d05-3408-4bc9-aa05-f65fb3aac677 user=SA] to manual commit

★ 2026-08-28T21:12:43.205+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL query
★ 2026-08-28T21:12:43.206+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL statement [SELECT * FROM training WHERE id=?]
★ 2026-08-28T21:12:43.235+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL update
★ 2026-08-28T21:12:43.235+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL statement [UPDATE training SET title=?, start_date_time=?, end_date_time=?, reserved=?, capacity=? WHERE id=?]
★ 2026-08-28T21:12:43.238+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL query
★ 2026-08-28T21:12:43.239+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL statement [SELECT * FROM student_type WHERE code=?]
★ 2026-08-28T21:12:43.240+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL update
★ 2026-08-28T21:12:43.240+09:00 DEBUG 27644 --- [           main] o.s.jdbc.core.JdbcTemplate               : Executing prepared SQL statement [INSERT INTO reservation VALUES (?,?,?,?,?,?,?)]

2026-08-28T21:12:43.241+09:00 DEBUG 27644 --- [           main] o.s.jdbc.support.JdbcTransactionManager  : Initiating transaction commit
2026-08-28T21:12:43.241+09:00 DEBUG 27644 --- [           main] o.s.jdbc.support.JdbcTransactionManager  : Committing JDBC transaction on Connection [HikariProxyConnection@2055019335 wrapping conn0: url=jdbc:h2:mem:f8950d05-3408-4bc9-aa05-f65fb3aac677 user=SA]
2026-08-28T21:12:43.242+09:00 DEBUG 27644 --- [           main] o.s.jdbc.support.JdbcTransactionManager  : Releasing JDBC Connection [HikariProxyConnection@2055019335 wrapping conn0: url=jdbc:h2:mem:f8950d05-3408-4bc9-aa05-f65fb3aac677 user=SA] after transaction
予約の処理が完了しました。 予約ID=10fb8653-0ad5-4de6-a424-9abd391414f9
2026-08-28T21:12:43.246+09:00  INFO 27644 --- [ionShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown initiated...
2026-08-28T21:12:43.250+09:00  INFO 27644 --- [ionShutdownHook] com.zaxxer.hikari.HikariDataSource       : HikariPool-1 - Shutdown completed.

プロセスは終了コード 0 で終了しました
```