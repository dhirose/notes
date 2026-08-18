---
tags:
- 設計
- アンチパターン
---

# フラグ引数
以下のようなboolean型やint型のフラグ引数を渡して処理を分岐させるのは、処理の内部をロジックを見ないといけないし、可読性が低下して、開発生産性が低下する。

```java title="フラグ引数を使った良くない例"
void damage(boolean damageFlag, int damageAmount) {
    if (damageFlag) {
        // ヒットポイントダメージ
        member.hitPoint -= damageAmount;
        if (0 < member.hitPoint) return;

        member.hitPoint = 0;
        member.addState(StateType.dead);
    } else {
        // 魔法力ダメージ
        member.magicPoint -= damageAmount;
        if (0 < member.damageFlag) return;

        member.damageFlag = 0;
    }
}

void execute(int processNumber) {
    if (processNumber == 0) {
        // アカウント登録処理
    } else if (processNumber == 1) {
        // 配送完了メール送信処理
    } else if (processNumber == 2) {
        // 処理
    } else if (processNumber == 3) {
        // 処理
    }
}
```

## メソッドを分離する
せめてメソッドを分離する。  
もっといいのは、ストラテジパターンで機能を取り換える。

```java title="ヒットポイントダメージと魔法力ダメージとでメソッドを分離する"
void hitPointDamage(int damageAmount) {
    member.hitPoint -= damageAmount;
    if (0 < member.hitPoint) return;

    member.hitPoint = 0;
    member.addState(StateType.dead);
}

void magicPointDamage(int damageAmount) {
    // 魔法力ダメージ
    member.magicPoint -= damageAmount;
    if (0 < member.damageFlag) return;

    member.damageFlag = 0;
}
```

## ストラテジパターンで機能を取り換える
```java title="ストラテジパターン、enum、mapで機能を取り換える"
interface Damage {
    void execute(final int damageAmount);
}

class HitPointDamage implements Damage {
    public void execute(final int damageAmount) {
        member.hitPoint -= damageAmount;
        if (0 < member.hitPoint) return;

        member.hitPoint = 0;
        member.addState(StateType.dead);
    }
}

class MagicPointDamage implements Damage {
    public void execute(final int damageAmount) {
        member.magicPoint -= damageAmount;
        if (0 < member.damageFlag) return;

        member.damageFlag = 0;
    }
}

class Attack {
    private final Map<DamageType, Damage> damages;
    Attack () {
        damages = Map.of(
                DamageType.hitPoint, new HitPointDamage(),
                DamageType.magicPoint, new MagicPointDamage()
        );
    }

    void applyDamage(final DamageType damageType, final int damageAmount) {
        final Damage damage = damages.get(damageType);
        damage.execute(damageAmount);
    }
}

enum DamageType {
    hitPoint,
    magicPoint
}
```