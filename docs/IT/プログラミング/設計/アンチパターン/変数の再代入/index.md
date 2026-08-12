---
tags:
- 設計
- 良いコード/悪いコードで学ぶ設計入門
- アンチパターン
- アンチパターン
---

## 弊害
再代入はコードの途中で変数の意図が変わる。

## 改善効果
全体としてどんな値を使っているのかわかりやすくなる。

### コード例

```java title="アンチパターン"
public class Main {
    public static void main(String[] args) {
        int playerArmPower = 10;
        int playerWeaponPower = 15;

        int enemyBodyDefence = 30;
        int enemyArmorDefence = 40;

        int damageAmount = 0;

        damageAmount = playerArmPower + playerWeaponPower;
        damageAmount = damageAmount - ((enemyBodyDefence + enemyArmorDefence) / 2);

        if (damageAmount < 0)
            damageAmount = 0;
    }
}
```

```java title="修正後"
public class Main {
    public static void main(String[] args) {
        int playerArmPower = 10;
        int playerWeaponPower = 15;

        int enemyBodyDefence = 30;
        int enemyArmorDefence = 40;

        int totalPlayerAttackPower = playerArmPower + playerWeaponPower;
        int totalEnemyDefence = enemyBodyDefence + enemyArmorDefence;
        int damageAmount = totalPlayerAttackPower - (totalEnemyDefence / 2);

        if (damageAmount < 0)
            damageAmount = 0;
    }
}
```