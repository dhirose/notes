---
tags:
- SpringBoot
---

# SpringJDBC_JOINした結果の取得
## 1対1のテーブルのJOIN
### テーブルとマッピングクラスのサンプル
#### テーブル

**reservation**

|<u>id</u>|<span style="text-decoration: underline dotted;">student_type_id</span>|name|
|-|-|-|
|r01|st01|東京太郎|

**student_type**

|<u>id</u>|code|name|
|-|-|-|
|st01|EMPLOYEE|会社員|

---

**JOIN後のテーブル**

|id|student_type_id|name|id|code|name|
|-|-|-|-|-|-|
|r01|st01|東京太郎|st01|EMPLOYEE|会社員|

#### クラス
```java title="Reservationクラス"
public class Reservation {
    // getter,setter省略
    private String id;
    private String student_type_id;
    private String name;
    private String StudentType StudentType;
}
```
```java title="StudentTypeクラス"
public class StudentType {
    // getter,setter省略
    private String id;
    private String code;
    private String name;
}
```

### DataClassRowMapperの欠点
上記のテーブルreservationとstudent_typeを結合してデータを取得してJavaオブジェクトにマッピングする場合、
どのテーブルのどのカラムなのか判別できないため、AS句を使って別名を付ける必要がある。  
だたその場合、DataClassRowMapperの仕様上以下の問題があり、Entityオブジェクトへの変換ができない。

- フィールドとカラム名が異なる場合にマッピングされない
- 複数のEntityオブジェクトへの変換ができない。

```java title="DataClassRowMapperを使用した検索"
Reservation reservation = jdbcTemplate.queryForObject(
    "SQL(省略)", new DataClassRowMapper<>(Reservation.class), id
)
```

DataClassRowMapperの様々な使い方は[8_Spring JDBCでのDBアクセス](./../../基本/8_Spring%20JDBCでのDBアクセス/index.md)を参照

上記の問題を解決して、Entityへの変換をするのに **RowMapperインターフェース** を用いる。

### RowMapperインターフェースの実装による解決
もともとDataClassRowMapperはRowMapperインターフェースの実装クラス。  
RowMapperインターフェースを自分で実装したクラスを用意して、マッピング処理を実装してEntityに変換してあげればDataClassRowMapperの問題点を解決できる。  
以下実装例。
```java title="RowMapperインターフェースの実装の実装例"
public class ReservationRepositoryImpl implements ReservationRepository {
    public Reservation selectById(String id) {
        return jdbcTemplate.queryForObject(
            "SQL(省略)", new ReservationRowMapper(), id
        )
    }

    static class ReservationRowMapper implements RowMapper<Reservation> {
        @Override
        public OrderItem mapRow(ResultSet rs, int rowNum) throws SQLException {
            StudentType studentType = new StudentType();
            studentType.setId(rs.getString("st_id"));
            studentType.setCode(rs.getString("st_code"));
            studentType.setName(rs.getString("st_name"));

            Reservation reservation = new Reservation();
            reservation.setId(rs.getString("r_id"));
            reservation.setStudentTypeId(rs.getString("r_student_type_id"));
            reservation.setName(rs.getString("r_name"));
            reservation.setStudentType(studentType);
            return reservation;
        }
    }
}
```

この例は、インナークラスで書いてあるけど、RowMapperはFunctionalInterfaceの為、ラムダ式で書くことも可能。

```java title="RowMapperの定義"
/**
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package org.springframework.jdbc.core;

import java.sql.ResultSet;
import java.sql.SQLException;
import org.springframework.lang.Nullable;

@FunctionalInterface
public interface RowMapper<T> {
    @Nullable
    T mapRow(ResultSet rs, int rowNum) throws SQLException;
}
```

## 1対多のテーブルのJOIN
RowMapperインターフェースは1件のレコードに対する処理しか記述できない。  
複数件のレコードに対応するにはResultSetExtractorインターフェースを実装する。  
ResultSetExtractorは取得した全件のレコードをまとめて変換するためのメソッドが定義されている。
### テーブルとマッピングクラスのサンプル
#### テーブル

**training**

|<u>id</u>|title|start_date_time|
|-|-|-|
|t01|ビジネスマナー研修|2021/1/20|

**reservation**

|<u>id</u>|<span style="text-decoration: underline dotted;">training_id</span>|name|
|-|-|-|
|r01|t01|東京太郎|
|r02|t02|神奈川次郎|

---

**JOIN後のテーブル**

|t_id|t_title|t_start_date_time|r_id|r_training_id|r_name|
|-|-|-|-|-|-|
|t01|ビジネスマナー研修|2021/1/20|r01|t01|東京太郎|
|t01|ビジネスマナー研修|2021/1/20|r02|t01|神奈川次郎|

#### クラス
```java title="Reservationクラス"
public class Reservation {
    // getter,setter省略
    private String id;
    private String student_type_id;
    private String name;
    private String StudentType StudentType;
}
```
```java title="StudentTypeクラス"
public class StudentType {
    // getter,setter省略
    private String id;
    private String code;
    private String name;
}
```

以下実装例。
```java title="ResultSetExtractorインターフェースの実装の実装例"
public class TrainingRepositoryImpl implements TrainingRepository {
    public Reservation selectById(String id) {
        return jdbcTemplate.query(
            "SQL(省略)", new TrainingResultSetExtractor(), id
        )
    }

    static class TrainingResultSetExtractor implements ResultSetExtractor<Training> {
        @Override
        public Training extractData(ResultSet rs) throws SQLException, DataAccessException {
            Training training = null;
            while(rs.next()) {
                if (training == null) {
                    Training training = new Training();
                    training.setReservations(new ArrayList<>());
                    training.setId(rs.getString("t_id"));
                    training.setTitle(rs.getString("t_title"));
                    training.setStartDateTime(rs.getTimestamp("t_start_date_time").toLocalDateTime());
                }
                Reservation reservation = new Reservation();
                reservation.setId(rs.getString("r_id"));
                reservation.setTraining(rs.getString("r_training_id"));
                reservation.setName(rs.getString("r_name"));
                training.getReservations().add(reservation);
            }
            return reservation;
        }
    }
}
```

ResultSetExtractorはFunctionalInterfaceの為、ラムダ式で書くことも可能。

```java title="ResultSetExtractorの定義"
//
// Source code recreated from a .class file by IntelliJ IDEA
// (powered by FernFlower decompiler)
//

package org.springframework.jdbc.core;

import java.sql.ResultSet;
import java.sql.SQLException;
import org.springframework.dao.DataAccessException;
import org.springframework.lang.Nullable;

@FunctionalInterface
public interface ResultSetExtractor<T> {
    @Nullable
    T extractData(ResultSet rs) throws SQLException, DataAccessException;
}
```