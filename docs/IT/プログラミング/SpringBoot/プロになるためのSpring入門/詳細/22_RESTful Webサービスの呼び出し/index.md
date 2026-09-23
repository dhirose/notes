---
tags:
- SpringBoot
---

# RESTful Webサービスの呼び出し
## RestTamplateクラスの紹介
Webサービスを呼び出す際に便利クラスとしてSpringではRestTamplateクラスを提供している。

## RestTemplateとHttpMessageConverter
RestTemplateは、HttpMessageConverterを使って、送受信するデータとJavaのオブジェクトを自動的に変換してくれる。

**リクエスト時**  
RestTemplateは、内部ではHttpMessageConverterを使って、JavaのオブジェクトをJSONやXMLのデータに変換する。

**レスポンス時時**  
HttpMessageConverterを使って、JSONやXMLをJavaのオブジェクトに変換する。

## RestTemplateクラスの主なメソッド
RestTemplateはHTTPのメソッドの応じてJavaのメソッドを提供する。

|HTTPメソッド|RestTemplateクラスのメソッド|
|-|-|
|GET|getForObject<br>getForEntity|
|PUT|put|
|POST|postForLocation<br>postForObject<br>postForEntity|
|DELETE|delete|
|すべて|exchange|

## GET用のメソッド
### getForObjectメソッド
[getForObjectメソッドのjavadoc](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#getForObject(java.lang.String,java.lang.Class,java.lang.Object...))

#### 単一データの取得
以下のリクエスト・レスポンスのデータ構造を想定する。

```http title="GETのリクエスト"
GET /api/trainings/t01 HTTP/1.1
Host: localhost:8080
Accept: application/json
```

```http title="GETのレスポンス"
HTTP/1.1 200 OK
Content-Type: application/json

{
    "id": "t01",
    "title": "ビジネスマナー研修",
    "startDateTime": "2021-08-01T09:30:00",
    "endDateTime": "2021-08-03T17:00:00",
    "reserved": 1,
    "capacity": 10
}
```

```java title="getForObjectの使用例"
Training training = restTemplate.getForObject(
    "http://localhost:8080/api/trainings/{id}", Traingin.class, "t01"
);
```

#### 複数データの取得
以下のリクエスト・レスポンスのデータ構造を想定する。

```http title="GETのリクエスト"
GET /api/trainings/t01 HTTP/1.1
Host: localhost:8080
Accept: application/json
```

```http title="GETのレスポンス"
HTTP/1.1 200 OK
Content-Type: application/json

[
    {
        "id": "t01",
        "title": "ビジネスマナー研修",
        "startDateTime": "2021-08-01T09:30:00",
        "endDateTime": "2021-08-03T17:00:00",
        "reserved": 1,
        "capacity": 10
    },
    {
        "id": "t02",
        "title": "Java研修",
        "startDateTime": "2021-08-01T09:30:00",
        "endDateTime": "2021-08-03T17:00:00",
        "reserved": 5,
        "capacity": 8
    }
]
```

```java title="getForObjectので複数権を取得"
Training[] trainings = restTemplate.getForObject(
    "http://localhost:8080/api/trainings", Traingin[].class
);
```

javaの仕様上`List[Training].class`のような書き方はコンパイルエラーになりできない。  
以下のように変換するとよい。
```java
Training[] trainingArray = restTemplate.getForObject(
    "http://localhost:8080/api/trainings", Traingin[].class
);
List<Training> trainings = Arrays.asList(trainingArray);
```

## POST用のメソッド
[postForLocationのjavadoc](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#postForLocation(java.lang.String,java.lang.Object,java.lang.Object...))

### postForLocationメソッド
以下のリクエスト・レスポンスのデータ構造を想定する。

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

```java title="postForLocationの使用例"
Training training = restTemplate.postForLocation(
    "http://localhost:8080/api/trainings", training
);
```

第二引数はリクエストボディのオブジェクトを渡す。HttpMessageConverterがJSONやXMLに自動で変換してくれる。  
Locationヘッダに値には、新規で登録したリソースのURLが記載されている。

## PUT用のメソッド
[putのjavadoc](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#put(java.lang.String,java.lang.Object,java.lang.Object...))
### putメソッド
以下のリクエスト・レスポンスのデータ構造を想定する。

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

```java title="putの使用例"
restTemplate.put("http://localhost:8080/api/trainings/{id}", training, "t01");
```

Restのガイドラインに沿っている場合、レスポンスボディは空になるから戻り値はvoidになる。

## DELETE用のメソッド
[deleteのjavadoc](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#delete(java.lang.String,java.lang.Object...))

### deleteメソッド
以下のリクエスト・レスポンスのデータ構造を想定する。

```http title="DELETEのリクエスト"
PUT /api/trainings/t03 HTTP/1.1
Host: localhost:8080
```

```http title="PUTのレスポンス"
HTTP/1.1 204 No Content
Content-Length: 0
```

```java title="deleteの使用例"
restTemplate.delete("http://localhost:8080/api/trainings/{id}", "t03");
```

Restのガイドラインに沿っている場合、レスポンスボディは空になるから戻り値はvoidになる。

## RestTemplateオブジェクトの用意
DIコンテナを使う場合と使わない場合のそれぞれで用意の仕方がある。

### DIコンテナを使わない場合

**RestTemplateをコンストラクタを呼んで生成**
```java title="new演算子でオブジェクトを生成"
RestTemplate restTemplate = new RestTemplate();
```

**RestTemplateBuilderを使って生成**
```java title="RestTemplateBuilderクラスを使用"
RestTemplate restTemplate = new RestTemplateBuilder()
    .rootUri("http://localhost:8080").build();
```

### DIコンテナを使う場合
DIコンテナに登録するやり方は、様々な恩恵が受けられる。  
Spring Bootを利用する場合は、オートコンフィグレーションによってRestTemplateBuilderオブジェクトがDIコンテナに登録されるから、@Beanメソッドの引数に受け取れる。

```java
    @Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder) {
        return builder.rootUri("http://localhost:8080").build();
    }
```

こうすることでapplication.propertiesで指定したプロパティの中に、RestTemplateオブジェクトの挙動に関係する部分の設定があればRestTemplateBuilderに反映されるから、RestTemplateにも反映される。
```java title="RestTemplateオブジェクトのインジェクション"
@Component
public class FooClient {
    private final RestTemplate restTemplate;
    public FooClient(RestTemplate restTemplate) {
        this.restTemplate = restTemplate;
    }
    ...
}
```

## RestTemplateクラスの使用サンプル

```java title="一通りの操作サンプル"
        RestTemplate restTemplate1 = new RestTemplateBuilder()
                .rootUri("http://localhost:8080")
                .build();

        TrainingAdminInput trainingAdminInput = new TrainingAdminInput();
        trainingAdminInput.setTitle("SQL入門");
        trainingAdminInput.setStartDateTime(LocalDateTime.of(2021, 12, 1, 9, 30));
        trainingAdminInput.setEndDateTime(LocalDateTime.of(2021, 12, 3, 17, 0));
        trainingAdminInput.setReserved(0);
        trainingAdminInput.setCapacity(8);

        URI location = restTemplate
                .postForLocation("/api/trainings", trainingAdminInput);

        Training training = restTemplate.getForObject(location, Training.class);

        trainingAdminInput.setTitle("SQL入門（改）");
        restTemplate.put(location, trainingAdminInput);
        restTemplate.delete(location);
```

## レスポンスの詳細データを参照する場合
レスポンスのステータスコードやレスポンスヘッダの値を参照したいケースがある。  
その場合、GETとPOSTのリクエストについてはそれぞれgetForEntityメソッドとpostForEntityメソッドを使用するとよい。

いずれのメソッドもResponseEntityオブジェクトを返す。  
ResponseEntityはHTTPレスポンスのデータを表す型。ステータスコード・レスポンスヘッダ・レスポンスボディを持つ。


[getForEntityのjavadoc](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#getForEntity(java.lang.String,java.lang.Class,java.lang.Object...))

[postForEntityのjavadoc](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#postForEntity(java.lang.String,java.lang.Object,java.lang.Class,java.lang.Object...))


```java title="getForEntityの使用例"
ResponseEntity<Training> training = restTemplate.getForEntity(
    "http://localhost:8080/api/trainings/{id}", Traingin.class, "t01"
);
int statusCode = responseEntity.getStatusCode().value();
String etag = responseEntity.getHeaders().getETag();
Training body = responseEntity.getBody();
```

## リクエストの詳細データを設定する場合
RequestEntityはHTTPリクエストのデータを表す型。HTTPのメソッドやURLのパス、リクエストヘッダやリクエストボディといった情報を保持することができる。  
RestTemlateのexchangeメソッドにRequestEntityを渡すことによって、リクエストすることができる。

[exchangeメソッドのjavadoc](https://spring.pleiades.io/spring-framework/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html#exchange(org.springframework.http.RequestEntity,java.lang.Class))

```java title="exchangeメソッドの使用例"
RequestEntity<Void> requestEntity = RequestEntity
    .get("http://localhost:8080/api/trainings/{id}", Traingin.class, "t01")
    .accept(MediaType.APPLICATION_JSON)
    .build();

ResponseEntity<Training> responseEntity = restTemplate.exchange(requestEntity, Training.class);
Training body = responseEntity.getBody();
```

リクエストボディのデータは空の想定だから、型パラメータはVoidを指定している。

## WebClientクラスについて
Springには、RestTemplateクラスと似た役割のクラスとして、WebClientというクラスが提供されている。  
WebClientのほうが後発で、RestTemplateよりも高度な機能が提供されている。  
また、RestTemplateのJavadocには、RestTemplateは今後メンテナンスモード（バグ修正や、小さな機能追加だけ対応）となる旨が記載されている。

WebClientはReactive Programmingをベースにして作られている。