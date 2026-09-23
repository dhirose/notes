---
tags:
- SpringBoot
---

# プロパティの外部化
## 外部化したプロパティを設定する場所
代表的な場所として以下がある。

### プロパティファイル
application.propertiesなどのファイルに設定するデータ

```properties title="プロパティファイル"
foo=aaa
```

### 環境変数
OSに設定できるデータ
```shell title="環境変数"
export bar=bbb
```

### システムプロパティ
アプリケーション内で設定できるデータ
> ```shell title="システムプロパティ"
> java -Dbaz=ccc ...
> ```

## プロパティの読み込み
Springは、読み込んだ環境変数とシステムプロパティをEnviroment型のオブジェクトで一元管理する。  

SpringBootを使用した場合は、application.propertiesも自動的に読み込まれてEnviromentで管理される。  

開発者が独自に用意したプロパティファイルはJavaConfigに@PropertySourceを付けて、プロパティファイルの場所を指定して読み込む。
```java title="@PropertySourceの使用例"
@Configuration
@PropertySource("abc.properties")
public class FooConfig {}
```

### 本番とステージングでのプロパティファイルの切り替え
環境ごとに読み込む設定変える場合、環境ごとのプロパティファイルとJavaConfigを用意して、起動パラメータで読み込むプロファイルを指定する。  

プロファイルについては[6_コンフィグレーションの切り替え](./../../基本/6_コンフィグレーションの切り替え/index.md)を参照

=== "プロパティ"

    === "本番環境"

        ```properties title="本番用のプロパティファイル(prod.properties)"
        db.url=jdbc:postgresql://prod.example.com/training
        ```

    === "ステージング環境"

        ```properties title="ステージング用のプロパティファイル(stg.properties)"
        db.url=jdbc:postgresql://stg.example.com/training
        ```


=== "Java"

    === "本番環境"
        ```java title="本番環境のJavaConfig"
        @Configuration
        @Profile("prod")
        @PropertySource("prod.properties")
        public class ProdConfig {}
        ```

    === "ステージング環境"
        ```java title="ステージング環境のJavaConfig"
        @Configuration
        @Profile("stg")
        @PropertySource("stg.properties")
        public class StgConfig {}
        ```

@Profileによって、それぞれプロファイル「stg」と「prod」に即している。  
さらに、@PropertySourceでそれぞれ別のプロパティファイルを読み込んでいる。