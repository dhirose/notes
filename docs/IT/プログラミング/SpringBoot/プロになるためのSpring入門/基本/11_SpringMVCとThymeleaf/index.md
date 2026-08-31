---
tags:
- SpringBoot
- SpringMVC
- Thymeleaf
---

# SpringMVCとThymeleaf
## 必要な設定
- pom.xmlの設定
- Controllerクラスの作成
- Viewの作成
- SpringBootで実行

### pom.xmlの設定
ライブラリはSpringBootが提供するStartersを使用して取得する。
```xml title="pom.xml"
    ...
    <dependency>  <!-- Spring MVCを利用するためのライブラリ -->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>  <!-- Thymeleafを利用するためのライブラリ -->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    <dependency>  <!-- (Startersではない)開発時に便利な機能を提供するライブラリ -->
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
```

### Controllerクラスの作成
- @Controllerアノテーションを付ける
- @GetMappingを付ける
- ModelはSpring MVCが提供する型で、Viewに参照してもらうオブジェクトを格納する
- 戻り値にView名（テンプレートファイル名）を指定する


```java title="Controllerクラス"
@Controller
public class SampleController {

    @GetMapping("/display-sample")  // リクエストとメソッドを紐づける
    public String displaySample(Model model) {
        model.addAttribute("fullName", "埼玉次郎");
        return "sample";
    }

}
```

### Viewの作成
`src/main/resources/templates`配下にファイルを配置する。

```html title="src/main/resources/templates/sample.html"
<!DOCTYPE HTML>
<html xmlns:th="http:///www.thymeleaf.org">
    <head>
        <title>サンプル</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <body>
        <h1 th:text="${fullName}">東京太郎</h1>
    </body>
</html>
```

### SpringBootで実行
- @SpringBootApplicationでSpringBootであることを明示
  - @Configurationが含まれるため、DIコンテナはJavaConfigクラスとして認知する
  - @EnableAutoConfigurationが含まれるため、Thymeleafや必要なBeanが自動的に登録される
  - @ComponentScanが含まれるため、@ControllerクラスがBeanとして登録される

```java title="Controllerクラス"
@SpringBootApplication
public class SampleApplication {

    public static void main(String[] args) {
        SpringApplication.run(SampleApplication.class, args);
    }

}
```

上記のようなmainメソッドを持つクラスを実行すると、Tomcatが起動してリクエストが受け付けられる状態になる。  
`http://localhost:8080/display-sample`にアクセスすればページが表示される。

## 実装の詳細
### ハンドラメソッドと@XxxPapping
|アノテーション|説明|
|-|-|
|@RequestMapping|パスとHTTPメソッドを記述して、メソッドとリクエストを紐付ける（従来の書き方）|
|@GetMapping|@RequestMappingより簡潔な記述の仕方|
|@PostMapping|〃|

```java title="ハンドラメソッドの例"
@Controller
public class CatalogController {
    @RequestMapping(path="/foo/abc", method=RequestMethod.GET)
    public String abc() {...}

    @GetMapping("/foo/xyz")
    public String xyz() {...}

    @PostMapping("/foo/xyz")
    public String postXyz() {...}
}
```

クラスに@RequestMappingを付けると、そのクラス内で使うリクエストハンドラのパスに共通のパスを設定することができる。
```java title="クラスに付けた@RequestMapping"
@Controller
@RequestMapping(path="/foo")
public class CatalogController {
    @GetMapping("/abc")
    public String abc() {...}

    @GetMapping("/xyz")
    public String xyz() {...}

    @PostMapping("/xyz")
    public String postXyz() {...}
}
```

### リクエストパラメータの取得
リクエストパラメータ
`pageNo=1&maxCount=10`

- @RequestParam
    - リクエストパラメータの値を取得する。

```java title="リクエストパラメータを受け取るハンドラメソッド"
    @XxxMapping("/display-list")
    public String displayList(
        @RequestParam("pageNo") int pNo, @RequestParam("maxCount") int max) {
        ...
    }
```

パラメータ名を合わせれば、@RequestParamのパラメータは省略可。
```java title="パラメータ名の省略"
    @XxxMapping("/display-list")
    public String displayList(@RequestParam int pageNo, @RequestParam int maxCount) {
        ...
    }
```

デフォルト値の設定が可能。
```java title="リクエストパラメータのデフォルト値"
    @XxxMapping("/display-list")
    public String displayList(
        @RequestParam(defaultValue="1") int pageNo, @RequestParam(defaultValue="10") int maxCount) {
        ...
    }
```

#### @RequestParam以外でのパラメータの取得方法
リクエストパラメータをプロパティに持つクラスを、ハンドラメソッドの引数に設定して受け取る。

```java title="リクエストパラメータを格納するクラス"
public class PageInput {
    private Integer pageNo;
    private Integer maxCount;
    ... Getter・Setterメソッド
}
```
```java title="値を格納するクラスを引数で定義したハンドラメソッド"
    @XxxMapping("/display-list")
    public String displayList(PageInput pageInput) {...}
```

リクエストパラメータが送信されると、ハンドラメソッドが呼ばれる前にPageInputのオブジェクトが自動的に生成されて値が設定される。

## Viewへのデータの渡し方
Modelオブジェクトの中にデータを格納する必要がある。

```java title="Modelにデータ格納"
    @GetMapping("/display-list")
    public String displayList(@RequestParam String trainingId, Model model) {
        Training training = trainingService.findById(training); 
        model.addAttribute("training", training);
        return "training/trainingDetails";
    }
```

また、Modelにセットする属性名は省略可能。  
この場合、自動的に属性名が割り当てられる。格納したオブジェクトのクラス名を小文字にした文字列が属性名。
```java title="属性名の指定の省略"
    @GetMapping("/display-list")
    public String displayList(@RequestParam String trainingId, Model model) {
        Training training = trainingService.findById(training); 
        model.addAttribute(training);
        return "training/trainingDetails";
    }
```

## Thymeleafでデータを参照

- Modelオブジェクトのデータを参照する場合は`${Modelオブジェクト.プロパティ}`で指定する
- 「#」マークはThymeleafが保持する汎用的なオブジェクトにアクセスするための記述。
    - `#temporals`は日時データを扱うのに便利なオブジェクト。

```html title="Modelオブジェクトのデータを参照"
<table>
    <tr>
        <th>研修タイトル</th>
        <td><span th:text="${training.title}"></span></td>
    </tr>
    <tr>
        <th>開始日時</th>
        <td><span th:text="${#temporals.format(training.startDateTime, 'yyyy/MM/dd HH:mm')}"></span></td>
    </tr>
    <tr>
        <th>予約数</th>
        <td><span th:text="${training.reserved}"></span></td>
    </tr>
</table>
```

毎回オブジェクト名を指定する冗長性を排除するのに役立つのが`th:object`

- `th:object`属性は、指定したオブジェクトを内部的に選択した状態にする。
- `th:object`属性を使っている箇所のプロパティの参照は`*{プロパティ}`になる。

```html title="Modelオブジェクトのデータを参照"
<table th:object="${training}">
    <tr>
        <th>研修タイトル</th>
        <td><span th:text="*{title}"></span></td>
    </tr>
    <tr>
        <th>開始日時</th>
        <td><span th:text="*{#temporals.format(startDateTime, 'yyyy/MM/dd HH:mm')}"></span></td>
    </tr>
    <tr>
        <th>予約数</th>
        <td><span th:text="*{reserved}"></span></td>
    </tr>
</table>
```

## Listオブジェクトの参照
```java title="ListオブジェクトをModelオブジェクトに格納"
    @GetMapping("/display-list")
    public String displayList(Model model) {
        List<Training> trainings = trainingService.findAll(); 
        model.addAttribute(trainings);
        return "training/trainingList";
    }
```
※addAttributeで属性名を省略した場合、Trainingオブジェクト+Listオブジェクトで、trainingListが属性名になる。

- `th:each="変数名:${Modelオブジェクトのリスト}"`でModelオブジェクトのリストを繰り返し処理できる。
- `"@{パス(パラメータ名=値, ...)}"`はURLのパスを埋め込む書き方。
    - `<a th:href="@{/training/display-details(trainingId=${training.id})}">`は以下のようになる。
    - `<a href="/training/display-details?trainingId=p01">リンク</a>`
    - コンテキストパスは自動的に保管してくれる。
        - `<a href="/myapp/training/display-details?trainingId=p01">リンク</a>`

```html title="Modelオブジェクトのデータを参照"
<table border="1">
    <tr>
        <th>研修タイトル</th>
        <th>開始日時</th>
        <th>予約数</th>
    </tr>
    <tr th:each="training:${trainingList}">
        <td>
            <a th:href="@{/training/display-details(trainingId=${training.id})}">
                <span th:text="${training.title}"></span>
            </a>
        </td>
        <td><span th:text="${#temporals.format(startDateTime, 'yyyy/MM/dd HH:mm')}"></span></td>
        <td><span th:text="${reserved}"></span></td>
    </tr>
</table>
```

## 入力画面と入力チェック
### Bean Validationを使用した注力チェック
Java標準機能でBean Validationがある。※Bean Validationの「Bean」と、Springの「Bean」は別物。  
Bean Validationの主な機能は、入力チェックのルールをアノテーションで指定できること。
```java title="Inputクラス"
public class ReservationInput {
    private String trainingId;

    @NotBlank
    private String name;

    @NotBlank
    @Pattern(regexp="0\\d{1,4}-\\d{1,4}-\\d{4}")
    private String phone;

    @NotBlank
    @Email
    private String emailAddress;

    @NotBlank
    private String studentTypeCode;
    ... Getter・Setterメソッド
}
```

主なアノテーション

|アノテーション|用途|
|-|-|
|@NotNull|値がnullではないことを確認する|
|@NotBlank|文字列がnull・空文字・空白文字でないことを確認する（空白文字はNG）|
|@NotEmpty|文字列がnull・空白ではないことを確認する（空白文字はOK）。<br>List・Mapがnullや空（サイズ0）でないことを確認する|
|@Max|指定した値以下の数値であることを確認する。「@Max(10)」は、10以下であることを確認する|
|@Min|指定した値以上の数値であることを確認する。「@Min(10)」は、10以上であることを確認する|
|@Size|文字列の長さや、List・Mapの要素が最小と最大の範囲であることを確認する<br>「@Size(min=5, max=10)は、5以上で10以下であることを確認する」|
|@Email|メールアドレスの形式かどうかを確認する|
|@Pattern|指定した正規表現に合致するかを確認する|
|@AssertTrue|boolean型のフィールドもしくはメソッドの戻り値がtrueであることを確認する。<br>複数のフィールドにまたがった入力の時に便利（相関チェック）。<br>例えば、日付型のフィールドのendDateが、StartDateよりも後であることを確認する場合。|

## 入力画面の表示方法
入力画面を表示するハンドラメソッド。

```java title="初期画面を表示するハンドラメソッド"
@getMapping("/reservation/display-form")
public String displayForm(@RequestParam String trainingId, Model model) {
    ReservationInput reservationInput = new ReservationInput();
    reservationInput.setTrainingId(trainingId);
    reservationInput.setStudentTypeCode("EMPLOYEE");
    mode.addAttribute("reservationInput", reservationInput);  // 画面表示する初期値を設定
    List<StudentType> studentTypes = reservationService.findAllStudentType();
    mode.addAttribute("studentTypeList", studentTypes);
    return "reservation/reservationForm";
}
```

## 入力画面のテンプレートファイル
- th:field
    - inputタグのid属性、name属性、value属性を自動的に出力してくれる。
    - `<input type="text" id="name" name="name" value="" />`のような形式で出力される。
- th:errors
    - 指定したプロパティで入力チェックエラーの時だけ表示される。

```html title="入力画面を表示するテンプレートファイル"
<form th:action="@{/reservation/validate-input}" method="post" th:object="${reservationInput}">
    <table>
        <tr>
            <th>お名前</th>
            <td>
                <input type="text" th:field="*{name}" />
                <div th:errors="*{name}"></div>
            </td>
        </tr>
        <tr>
            <th>電話番号</th>
            <td>
                <input type="text" th:field="*{phone}" />
                <div th:errors="*{phone}"></div>
            </td>
        </tr>
        <tr>
            <th>メールアドレス</th>
            <td>
                <input type="text" th:field="*{emailAddress}" />
                <div th:errors="*{emailAddress}"></div>
            </td>
        </tr>
        <tr>
            <th>受講者のタイプ</th>
            <td>
                <select th:field="*{studentTypeCode}">
                    <option th:each="type:${studentTypeList}" th:value="${type.typeCode}" th:text="${type.typeName}" />
                </select>
            </td>
        </tr>
    </table>
    <input type="hidden" th:field="*{trainingId}" />
    <input type="submit" value="予約内容を確認" />
</form>
```

## 入力チェックの実施方法
- @Validated
    - ハンドラメソッドが呼ばれる前に入力チェックが行われる
- BindingResult
    - 入力チェックの結果。Inputクラスの直後に書かないといけない。
- エラーが起きた場合の入力情報の保持について
    - エラーが起きた場合、エラー文言の表示に加え、入力情報を保持しておきたい。Inputオブジェクトを引数で受け取った場合は、自動的にModelオブジェクトに格納されるため、明示的にModelオブジェクトに格納する必要はない。
    - なお、自動的に格納される場合は、クラス名の先頭を小文字にした文字列になる。

```java title="入力チェックを行うハンドラメソッド"
@PostMapping("/reservation/display-input")
public String validateInput(
    @Validated ReservationInput reservationInput,
    BindingResult bindingResult,
    Model model) {

    if (bindingResult.hasErrors()) {
        List<StudentType> studentTypeList = reservationService.findAllStudentType();
        mode.addAttribute("studentTypeList", studentTypeList);
        return "reservation/reservationForm";
    }
    ...
    return "reservation/reservationConfirmation";
}
```

## エラー文言の設定
入力エラーの文言はデフォルトものが用意されているが、プロパティファイルに記述することで任意の文言を指定することが可能。  
`src/main/resources/messages.properties`に以下のような記述を追加する。

```properties title="messages.propertiesのサンプル"
NotBlank.reservationInput.name=名前は必須です
NotBlank.reservationInput.phone=電話番号は必須です
Pattern.reservationInput.phone=電話番号の書式が不正です
NotBlank.reservationInput.emailAddress=メールアドレスは必須です
Email.reservationInput.emailAddress=メールアドレスの書式が不正です
NotBlank=項目は必須です
```
キーの指定方法は以下の4パターン。上の方が優先度が高い

- アノテーション名.Inputオブジェクトの属性名.フィールド名
- アノテーション名.フィールド名
- アノテーション名.フィールドの型
- アノテーション名

## リクエストパラメータを使用したハンドラメソッドの呼び分け
html側のname属性と、java側のparamsで呼び出すリクエストハンドラをボタンごとに割り当てる。

```html title="リクエストパラメータの割り当て"
<form th:action="@{/reservation/reserve}" method="post">
    <input name="reserve" type="submit" value="予約を確定" />
    <input name="correct" type="submit" value="予約を変更" />
</form>
```

```java title=""
@PostMapping(value="/reserve", params="correct")
public String correctInput(@Validated ReservationInput reservationInput, Model model) {
    List<StudentType> studentTypeList = reservationService.findAllStudentType();
    model.addAttribute(studentTypeList);
    return "reservation/reservationForm";
}

@PostMapping(value="/reserve", params="reserve")
public String reserve(@Validated ReservationInput reservationInput, Model model) {
    Reservation reservation = reservationService.reserve(reservationInput);
    model.addAttribute(reservation);
    return "reservation/reservationCompletion";
}
```

# 例外のハンドリング
ハンドラメソッド内で例外がスローされると、SpringBootが用意したデフォルトのエラー画面が表示される。（WhiteLabel Error Pageが表示された画面）  
デフォルトのエラー画面はユーザーに表示するには不適切なため、独自に用意したエラー画面を表示したい場合がある。  
@ExceptionHandlerアノテーションを使うことにより、簡単に実装することができる。

- @ExceptionHandler
    - ハンドリングしたい例外クラスをパラメータに設定すると、その例外がスローされた場合に@ExceptionHandlerを付けたメソッドが呼ばれる。

```java
@PostMapping(value="/reservation/reserve", params="order")
public String reserve(@Validated ReservationInput reservationInput, Model model) {
    ...
}

@ExceptionHandler(CapacityOverException.class)
public String displayCapacityOverPage() {
    return "reservation/capacityOver";
}
```