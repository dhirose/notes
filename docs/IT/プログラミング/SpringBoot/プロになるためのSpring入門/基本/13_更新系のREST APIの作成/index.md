---
tags:
- SpringBoot
- Restful
---

# 更新系のREST APIの作成
以下のリクエスト・レスポンスを取得することを想定する。

```http title="PUTのリクエスト"
PUT /api/trainings/t01 HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
    "title": "ビジネスマナー研修(改)",
    "startDateTime": "2021-08-01T09:30:00",
    "endDateTime": "2021-08-03T17:00:00",
    "reserved": 1,
    "capacity": 10
}
```

```http title="PUTのレスポンス"
HTTP/1.1 204 No Content
Content-Length: 0
```

## PUTに対応したハンドラメソッド
以下実装。

```java title="PUTメソッドに対応するハンドラメソッド"
@Controller
public class TrainingAdminRestController {
    ...
    @PutMapping("/api/trainings/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void updateTraining(@PathVariable String id,
        @Validated @RequestBody TrainingAdminInput trainingAdminImput
    ) {
        trainingAdminImput.setId(id);
        return trainingAdminService.update(trainingAdminImput);
    }
}
```

- ステータスコードは204を返したいから、@ResponseStatusを指定
- 戻り値voidでレスポンスボディを空にする
- 引数のTrainingAdminInputはリクエストぼてぃのデータを保持するクラス。@RequestBodyでリクエストボディのデータをオブジェクトに変換する

## POST時のリクエストとレスポンス

以下のリクエスト・レスポンスを取得することを想定する。

```http title="POSTのリクエスト"
POST /api/trainings HTTP/1.1
Host: localhost:8080
Content-Type: application/json

{
    "title": "SQL入門",
    "startDateTime": "2021-08-01T09:30:00",
    "endDateTime": "2021-08-03T17:00:00",
    "reserved": 0,
    "capacity": 8
}
```

```http title="POSTのレスポンス"
HTTP/1.1 201 Created
Location: http://localhost:8080/api/trainings/t99
```

## POSTに対応したハンドラメソッド
以下実装。

```java title="POSTメソッドに対応するハンドラメソッド"
@Controller
public class TrainingAdminRestController {
    ...
    @PostMapping("/api/trainings/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public ResponseEntity<void> registerTraining(
        @Validated @RequestBody TrainingAdminInput trainingAdminImput
    ) {
        Training training = trainingAdminImput.register(trainingAdminImput);

        URI location = ServletUriComponentsBuilder
            .fromCurrentRequestUri()
            .path("{id}")
            .buildAndExpand(training.getId())
            .toUri();
        return ResponseEntity.created(location).build();
    }
}
```

- POSTの場合はレスポンスヘッダにLocationヘッダを指定するため、ResponseEntityを戻り値にする必要がある。
    - 型パラメータ(<>)にはレスポンスボディに対応するオブジェクトを指定する。
- ServletUriComponentsBuilderはLocationヘッダに指定するURLを作る際に便利なSpringBootのクラス。


## DELETE時のリクエストとレスポンス

以下のリクエスト・レスポンスを取得することを想定する。

```http title="POSTのリクエスト"
DELETE /api/trainings/t03 HTTP/1.1
Host: localhost:8080
```

```http title="POSTのレスポンス"
HTTP/1.1 204 No Content
Content-Length:0
```

## DELETEに対応したハンドラメソッド
以下実装。

```java title="DELETEメソッドに対応するハンドラメソッド"
@Controller
public class TrainingAdminRestController {
    ...
    @DeleteMapping("/api/trainings/{id}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deleteTraining(@PathVariable String id) {
        trainingAdminService.delete(id);
    }
}
```

## 例外時のステータスコードの指定
ハンドラメソッドが例外をスローした場合、デフォルトだとステータスコード500（Internal Server Error）を返す。

```java title="例外をスローするハンドラメソッド"
@Controller
public class TrainingAdminRestController {
    ...
    @DeleteMapping("/api/trainings/{id}")
    public void getTraining(@PathVariable String id) {
        Training training = trainingAdminService.findById(id);
        if (training == null) throw new DataNotFoundException("データが見つからない id=" + id);
        
        return training;
    }

    @ExceptionHandler(DataNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public MyErrorData handleNotFound() {
        ...
        return myErrorData;
    }
}
```

ハンドラメソッド内(deleteTraining)でエラーがした場合にハンドリングしたい場合、@ExceptionHandlerでハンドリングしたい例外クラスを指定して@ResponseStatusで返したいレスポンスコードを指定する。  
また、戻り値で返したオブジェクトはHttpMessageConverterによってJSON等に変換されて、レスポンスボディに記述される。

また、ステータスコードを指定するほかの方法として、SpringMVCが提供するResponseStatusExceptionクラスを使用することも可能

```java title="例外をスローするハンドラメソッド"
@Controller
public class TrainingAdminRestController {
    ...
    @DeleteMapping("{id}")
    public void getTraining(@PathVariable String id) {
        Training training = trainingAdminService.findById(id);
        if (training == null) throw new ResponseStatusException(HttpStatus.NOT_FOUND);
        
        return product;
    }
}
```
これにより、@ExceptionHandlerを付けたメソッドを作成しなくてよくなる。