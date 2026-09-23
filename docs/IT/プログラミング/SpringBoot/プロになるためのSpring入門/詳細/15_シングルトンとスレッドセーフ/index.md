---
tags:
- SpringBoot
- シングルトン
- スレッドセーフ
---

# シングルトンとスレッドセーフ
DIコンテナで管理されるBeanは基本的にはシングルトン。（シングルトンでない使い方もできるが利用シーンは少ない）

## シングルトンとスレッドセーフ
JavaではAPサーバへのリクエストが送信されると、リクエストごとにスレッドが割り当てられる。（マルチスレッド）  
マルチスレッドでも安全にプログラムが動くことをスレッドセーフという。  
シングルトンのオブジェクトを使う場合は、スレッドセーフを意識する必要がある。

## スレッドセーフでないソースコード
``` java title="スレッドセーフでないソースコード"
class FooService
    private int totalPrice;

    public int calculateOrderPrice(int orderNo) {
        List<OrderItem> items = ...
        for (Order order : items) {
            totalPrice += item.getPrice();
        }
        return (int)(totalPrice * 1.1);
    }
}
```
複数のスレッドがこのクラスのオブジェクトのメソッドを呼び出すと、同じtotalPriceフィールドの領域を使用することになる。


以下のような場合に不具合が起きる。  
Aさん：calculateOrderPriceをリクエスト  
Aスレッド：Aさんの合計金額をtotalPriceに設定する  
Bさん：calculateOrderPriceをリクエスト  
Bスレッド：Bさんの合計金額をtotalPriceに設定する<span style="color:red">（Aさんの合計金額を上書き）</span>  
Aスレッド：Bさんの合計金額に消費税を乗せて返却<span style="color:red">（Bさんの合計金額を返してしまう）</span>  
Bスレッド：Bさんの合計金額に消費税を乗せて返却  

## スレッドセーフなソースコード
スレッドセーフなソースコードにするには、スレッドごとに変わるような値をフィールドではなく、ローカル変数に保持するようにする。

``` java title="スレッドセーフでないソースコード"
class FooService
    public int calculateOrderPrice(int orderNo) {
        List<OrderItem> items = ...
        int totalPrice = 0;
        for (Order order : items) {
            totalPrice += item.getPrice();
        }
        return (int)(totalPrice * 1.1);
    }
}
```

ローカル変数の領域はオブジェクトに対してではなくスレッドに対して用意される。

## Beanとして管理しないオブジェクト
オブジェクトの役割によっては、スレッドごとに変わる値をフィールドで保持するものがある。  
代表的なものはEntityオブジェクト。(DDDのEntityではない)

メソッドを実行しているスレッドによって値が変わることになる。  
例えば、Aスレッドではidがp01のデータ、Bスレッドではidがp03のデータを取得してEntityクラスに格納する。  
このようなオブジェクトはシングルトンとして利用するとスレッドセーフにならないため、通常はBeanとして管理しない。


補足として、Springを使用する場合はアプリケーション上の全てのオブジェクトをBeanとして管理するイメージではなく、Beanとして管理しないオブジェクトも普通にある。