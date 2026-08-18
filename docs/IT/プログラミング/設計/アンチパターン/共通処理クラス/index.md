---
tags:
- 設計
- アンチパターン
- カプセル化
- 共通処理
- 横断的関心事
---

# 共通処理クラス（Common・Utils）

## 共通部品クラスの特徴
- staticメソッドで共通処理が実装されがち。
- グローバル変数が出現しやすくなる。
- 様々なロジックが雑多に置かれがち

```java title="無関係な共通処理が雑多に置かれがち"
class Common {

    // 省略

    // 税込み金額を計算する
    // static BigDesimal calcAmountIncludingTax(BigDecimal amountExcludingTax, BigDecimal taxRate) {}

    // ユーザーが退会済みの場合true
    // static boolean hasResigned(User user) {}

    // 商品を注文する
    // static void createOrder(Product product) {}

    // 有効な電話番号である場合true
    // static boolean isValidPhoneNumber(String phoneNumber) {}
}
```

共通化はカプセル化により高まる。

## 共通処理の使いどころ
以下のような横断的関心事に関してはstaticな共通処理としてよい
- ログ出力
- エラー検出
- デバッグ
- 例外処理
- キャッシュ
- 同期処理
- 分散処理