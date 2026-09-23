---
tags:
- SpringBoot
- Restful
---

# RESTful Webサービスの作成
## サーバ側をステートレスにする
RESTでは、サーバ側をステートレスにして、クライアント側にステートを保持することが推奨される。  
例えば、サーバーを増やしてスケールアウトして処理性能を上げたくても、サーバ側にステートを保持すると、そのような柔軟な拡張が難しくなるため。

## HTTPメソッドと@XxxMapping
SpringMVCでのREST実装は、リクエストハンドラとHTTPメソッドを対応付けて実装をする。

|HTTPメソッド|アノテーション|
|-|-|
|GET|@GetMapping|
|POST|@PostMapping|
|PUT|@PutMapping|
|DELETE|@DeleteMapping|

```java title="PUTメソッドに対応するハンドラ"
@PutMapping("/products/...")
public void update(...) {...}
```

## HttpMessageConverterによるリクエスト・レスポンスデータの変換
RESTではリクエストボディやレスポンスボディのデータ形式をクライアント側が指定できる。  
クライアント側が指定したデータ形式に従って、データを自動的に変換してくれるのがHttpMessageConverter。  
※実際の変換はSpringMVCではなく、Jacksonというライブラリが行っている

変換するデータ形式に応じて（JSON、XMLなど）、HttpMessageConverterインターフェースを実装した具象クラスが複数提供されている。

## ステータスコードの指定
明示的にHTTPステータスコードを設定する場合は、@ResponseStatusで設定できる。  
指定しない場合は、200（OK）を返す。

```java title="ステータスコードの指定"
@PutMapping("/products/...")
@ResponseStatus(HttpStatus.NO_CONTENT)
public void update(...) {...}
```

## リクエストヘッダの参照
@RequestHeaderでリクエストヘッダを取得することができる。
```java title="リクエストヘッダの参照"
@GetMapping("/foo")
public Foo getFoo(@RequestHeader(HttpHeaders.USER_AGENT) String userAgent) {...}
```

## レスポンスヘッダの指定
SpringMVCはハンドラメソッドの戻り値をResponseEntityで返すと、ResponseEntityオブジェクトの内容に従ってレスポンスデータを生成する。  
ResponseEntityはステータスコード、レスポンスヘッダ、レスポンスボディの情報を保持する。

ResponseEntityは自分でHTTPレスポンス細かく操作したいときに使うといい。

```java title="レスポンスヘッダの指定"
@GetMapping("/foo")
public ResponseEntity<Foo> getFoo() {
    Foo foo = ...
    return ResponseEntity.ok()
        .eTag(foo.getVersion())
        .header(HttpHeaders.DATA, ...)
        .header("abc", "def")
        .body(foo);
}
```

- ETagヘッダ
    - リリースバージョンを指定するヘッダ。クライアント側でデータをキャッシュしてもらうときなどに使う。
- header
    - headerメソッドで任意のヘッダーを設定できる。第1引数:ヘッダー名、第2引数:値
- body
    - bodyメソッドで指定したオブジェクトは、HttpMessageConverterによってJSONやXMLなどに変換される。

## 参照系のREST APIの作成
以下のリクエスト・レスポンスを取得することを想定する。

```http title="GETのリクエスト"
GET /api/trainings/t01
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

以下実装。

```java title="GETメソッドに対応するハンドラメソッド"
@Controller
public class TrainingAdminRestController {
    ...
    @GetMapping("/api/trainings/{id}")
    @ResponseBody
    public Training getTraining(@PathVariable String id) {
        return trainingAdminService.findById(id);
    }
}
```

- @ResponseBody
    - 戻り値で返したオブジェクトをHttpMessageConverterを使ってJSONやXMLに形式変換の指示を出すアノテーション

REST APIを作成するばあ、ハンドラメソッドに@ResponseBodyを付けるケースは多いらしい。毎回、@ResponseBodyを付けるのは面倒だからSpring MVCは@RestControllerをControllerクラスに付ければ、@ResponseBodyを付けなくても考慮して動いてくれる

```java title="GETメソッドに対応するハンドラメソッド"
@RestController
public class TrainingAdminRestController {
    ...
    @GetMapping(/api/trainings/{id})
    public Training getTraining(@PathVariable String id) {
        return trainingAdminService.findById(id);
    }
}
```