---
tags:
- 設計
- アンチパターン
---

# 型判定の分岐
インターフェースを使っているのに型判定しては、インターフェースに意味がない。

```java title="型判定で分岐している良くない例"
/** 宿泊料金を表す */
interface HotelRates {
    Money fee();  // 料金
}

/** 通常宿泊料金 */
class RegularRates implements HotelRates {
    public Money fee() {
        return new Money(7000);
    }
}

/** プレミアム宿泊料金 */
class PremiumRates implements HotelRates {
    public Money fee() {
        return new Money(12000);
    }
}

// 型判定による繁忙期料金の切り替え
Money busySeasonFee;
if (hotelRates instanceof RegularRates)
    busySeasonFee = hotelRates.fee().add(new Money(3000));
else if (hotelRates instanceof RegularRates)
    busySeasonFee = hotelRates.fee().add(new Money(5000));
```

繁忙期の料金もインターフェースに定義して判定をしないようにする。

```java title="繁忙期の計算ロジックもインターフェースに定義する"
/** 宿泊料金を表す */
interface HotelRates {
    Money fee();  // 料金
    Money busySeasonFee();  // 繁忙期料金
}

/** 通常宿泊料金 */
class RegularRates implements HotelRates {
    public Money fee() {
        return new Money(7000);
    }

    public Money busySeasonFee() {
        return fee().add(new Money(3000));
    }
}

/** プレミアム宿泊料金 */
class PremiumRates implements HotelRates {
    public Money fee() {
        return new Money(12000);
    }
    public Money busySeasonFee() {
        return fee().add(new Money(5000));
    }
}


// 繁忙期料金の型判定ロジックが不要になった
Money busySeasonFee = hotelRates.busySeasonFee();
```