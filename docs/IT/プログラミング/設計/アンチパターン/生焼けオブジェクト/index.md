---
tags:
- 良いコード/悪いコードで学ぶ設計入門
- アンチパターン
---

# 生焼けオブジェクト（helf baked objects）

## 生焼けオブジェクトとは
初期化しないと使い物にならないクラス、または見初期化状態が発生しうるクラス

```java title="生焼けオブジェクトの例"
import java.math.BigDecimal;

public class Main {
    public static void main(String[] args) {
        ContractAmount amount = new ContractAmount();
        System.out.println(amount.salesTaxRate.toString());  // NullPointerException
    }
}

class ContractAmount {
    int amountIncludingTax;  // 税込み金額
    BigDecimal salesTaxRate;  // 消費税率
}
```