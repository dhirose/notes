---
tags:
- 設計
- アンチパターン
- カプセル化
---

# staticメソッドの誤用

## 弊害
- staticメソッドはインスタンス変数を持つことができないため、データとデータを操作するロジックが乖離してカプセル化できない。

```java title="staticメソッドを使ったカプセル化ができていない例"
public class Main {
    public static void main(String[] args) {
        MoneyData moneyData1 = new MoneyData(10);
        MoneyData moneyData2 = new MoneyData(20);
        moneyData1.amount = OrderManager.add(moneyData1.amount, moneyData2.amount);
    }
}

// 注文を管理するクラス
class OrderManager {
    static int add(int moneyAmount1, int moneyAmount2) {
        return moneyAmount1 + moneyAmount2;
    }
}

class MoneyData {
    int amount;
    MoneyData(int amount) {
        this.amount = amount;
    }
}
```

## インスタンスメソッドのフリをしたstaticメソッドに注意
以下のコードのaddメソッドはインスタンスメソッドだが、インスタンス変数discountRateを全く使っていない。  
このようなメソッドはstaticを付けても問題なく動作する。  
結局、実質staticメソッドであり、うまくカプセル化できていない。

```java title=""
class PaymentManager {
    private int discountRate;  // 割引率

    // コンストラクタ省略

    int add(int moneyAcount1, int moneyAcount2) {
        return moneyAcount1 + moneyAcount2;
    }
}
```

## どういうときにstaticメソッドを使えばいいか
関係しあうデータとロジックがバラバラになる心配がない場合に、staticメソッドが使える。
例として、ファクトリメソッドがあげられる。