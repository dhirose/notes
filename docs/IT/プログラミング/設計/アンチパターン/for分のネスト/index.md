---
tags:
- 設計
- アンチパターン
---

# for分のネスト
## continueでネストを解消
```java title="ネストが深く可読性が悪いコード"
for (Member member : members) {
    if (0 < member.hitPoint) {
        if (member.containsState(StateType.poison)) {
            member.hitPoint -= 10;
            if (member.hitPoint <= 0) {
                member.hitPoint = 0;
                member.addState(StateType.dead);
                member.removeState(StateType.poison);
            }
        }
    }
}
```

```java title="continueでネストを解消"
for (Member member : members) {
    if (member.hitPoint == 0) continue;
    if (!member.containsState(StateType.poison)) continue;

    member.hitPoint -= 10;

    if (0 < member.hitPoint) continue;

    member.hitPoint = 0;
    member.addState(StateType.dead);
    member.removeState(StateType.poison);
}
```

## breakでネストを解消
```java title="同じ条件でswitch文を複数実装している例"
/** ネストが深く可読性が悪いコード */
int totalDamage = 0;
for (Member member : members) {
    if (member.hasTeamAttackSucceeded()) {
        int damage = (int) (member.attack() * 1.1);
        if (30 <= damage) {
            totalDamage += damage;
        } else {
            break;
        }
    } else {
        break;
    }
}
```
```java title="breakでネストを解消"
int totalDamage = 0;
for (Member member : members) {
    if (!member.hasTeamAttackSucceeded()) break;

    int damage = (int) (member.attack() * 1.1);

    if (damage < 30 ) break;

    totalDamage += damage;
}
```