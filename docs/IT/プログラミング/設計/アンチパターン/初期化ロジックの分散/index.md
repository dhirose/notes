---
tags:
- 設計
- アンチパターン
- カプセル化
- ファクトリメソッド
---

# 初期化ロジックの分散

## 弊害
コンストラクタを公開すると様々な用途に使用され、結果関連するロジックが分散されてメンテナンスが大変になる。

以下はECサイトや決済サービスでの会員ごとの初期化。  
初期化時のポイントと生成ロジックが分離されていて、ポイント変更などの仕様変更があると生成している箇所すべてに影響が出る。
```java title="生成ロジックがカプセル化できていない例"
public class Main {
    public static void main(String[] args) {
        GiftPoint standardMemberShipPoint = new GiftPoint(3000);  // 標準会員
        GiftPoint premiumMemberShipPoint = new GiftPoint(10000);  // プレミアム会員
    }
}

class GiftPoint {
    private static final int MIN_POINT = 0;
    final int value;

    GiftPoint(final int point) {
        if (point < MIN_POINT) throw new IllegalArgumentException("ポイントが0以上ではありません。");
        value = point;
    }

    /**
     * ポイントを加算する
     * @param other 加算ポイント
     * @return 加算後の残余ポイント
     */
    GiftPoint add(final GiftPoint other) {
        return new GiftPoint(value + other.value);
    }

    // 残余ポイントが消費ポイント以上であればture（省略）
    // boolean isEnough() {}

    // ポイントを消費する（省略）
    // GiftPoint consume() {}
}
```

## privateコンストラクタ+ファクトリメソッドで目的別初期化
こうした初期化ロジックの分散を防ぐためにコンストラクタをprivateにして、目的別のファクトリメソッドを用意する。  
こうすることで初期化ロジックがファクトリメソッド1か所に集約される。

```java title=""
public class Main {
    public static void main(String[] args) {
        GiftPoint standardMemberShipPoint = GiftPoint.forStandardMembership();  // 標準会員
        GiftPoint premiumMemberShipPoint = GiftPoint.forPremiumMembership();  // プレミアム会員
    }
}

class GiftPoint {
    private static final int MIN_POINT = 0;
    private static final int STANDARD_MEMBERSHIP_POINT = 3000;
    private static final int PREMIUM_MEMBERSHIP_POINT = 10000;
    final int value;

    private GiftPoint(final int point) {
        if (point < MIN_POINT) throw new IllegalArgumentException("ポイントが0以上ではありません。");
        value = point;
    }

    /**
     * @return 標準会員向け入会ギフトポイント
     */
    static GiftPoint forStandardMembership() {
        return new GiftPoint(STANDARD_MEMBERSHIP_POINT);
    }

    /**
     * @return プレミアム会員向け入会ギフトポイント
     */
    static GiftPoint forPremiumMembership() {
        return new GiftPoint(PREMIUM_MEMBERSHIP_POINT);
    }

    /**
     * ポイントを加算する
     * @param other 加算ポイント
     * @return 加算後の残余ポイント
     */
    GiftPoint add(final GiftPoint other) {
        return new GiftPoint(value + other.value);
    }

    // 残余ポイントが消費ポイント以上であればture（省略）
    // boolean isEnough() {}

    // ポイントを消費する（省略）
    // GiftPoint consume() {}
}
```

※生成ロジックが増えすぎたら、ファクトリクラスも検討する。