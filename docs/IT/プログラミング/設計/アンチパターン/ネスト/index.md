---
tags:
- 設計
- アンチパターン
- カプセル化
---

# ネスト
## if文
### if文のネスト
```java title="if文のネストが多い例"
// 生存しているか判定
if (0 < member.hitPoint) {
    // 行動可能かを判定
    if (member.canAct()) {
        // 魔法力が生存しているかを判定
        if (magic.costMagicPoint <= member.magicPoint) {
            member.consumeMagicPoint(magic.costMagicPoint);
            member.chant(magic);
        }
    }
}
```
```java title="早期return解消"
if (0 <= member.hitPoint) return;
if (!member.canAct()) return;
if (magic.costMagicPoint < member.magicPoint) return;
if (member.technicalPoint < member.technicalPoint) return;  // 新規追加

member.consumeMagicPoint(magic.costMagicPoint);
member.chant(magic);
member.gainTechnicalPoint(magic.incrementTechnicalPoint);  // 新規追加
```

ifのネストは早期リターンで解消できる。  
また、以下のメリットがある
- 条件ロジックと実行ロジックを分離でき、コードの見通しが良くなる。
- ロジックの追加が容易で、追加後もネストが増えない。

### if、else if、elseのネスト

```java title="if、else if、elseのネストが多い例"
float hitPointRate = member.hitPoint / member.maxHitPoint;

HealthCondition currentHealthCondition;
if (hitPointRate == 0) {
    currentHealthCondition = HealthCondition.dead;
} else if (hitPointRate < 0.3) {
    currentHealthCondition = HealthCondition.danger;
} else if (hitPointRate < 0.5) {
    currentHealthCondition = HealthCondition.caution;
} else {
    currentHealthCondition = HealthCondition.fine;
}

return currentHealthCondition;
```

```java title="早期return解消"
float hitPointRate = member.hitPoint / member.maxHitPoint;

if (hitPointRate == 0) return HealthCondition.dead;
if (hitPointRate < 0.3) return HealthCondition.danger;
if (hitPointRate < 0.5) return HealthCondition.caution;

return HealthCondition.fine;
```