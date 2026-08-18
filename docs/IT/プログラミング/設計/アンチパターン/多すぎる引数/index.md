---
tags:
- 設計
- アンチパターン
- カプセル化
- 多すぎる引数
---

# 多すぎる引数

## 弊害
メソッドに引数を渡すのは、その引数を使って何か処理をしたいから。  
引数の量が多いということはそれだけ処理させたい内容が膨らむことになる。  
内容が増えると、ロジックの複雑化、重複実装が増える。


### ゲームの魔法の例
ゲームにおける魔法力には以下のような使用があるとする。

- 魔法を使うと、魔法力が一定量減少する
- 回復アイテムなどにより、魔法力は一定量回復する
- 魔法力には最大値がある
- 魔法力は最大値まで回復可能
- 一部の装備品は、魔法力の最大値を増加させる効果を持つ

```java title="引数が多いメソッドの例"
    /**
     * 魔法力を回復する
     * ① 装備品の魔法力最大値増加効果を追加した魔法力最大値を求める。
     * ② 魔法力最大値を超えないように魔法力を回復させる
     *
     * @param currentMagicPoint       現在の魔法力残量
     * @param originalMaxMagicPoint   オリジナルの魔法力最大値
     * @param maxMagicPointIncrements 魔法力最大値の増分
     * @param recoveryAmount          回復量
     * @return 回復後の魔法力残量
     */
    int recoverMagicPoint(
            int currentMagicPoint
            , int originalMaxMagicPoint
            , List<Integer> maxMagicPointIncrements
            , int recoveryAmount) {

        int currentMaxMagicPoint = originalMaxMagicPoint;
        for (int each : maxMagicPointIncrements)
            currentMaxMagicPoint += each;  // ①

        // ②
        return Math.min(currentMagicPoint + recoveryAmount, currentMaxMagicPoint);  // 2つの値のうち小さい方を返す
    }
```
回復以外の処理を行っている。（魔法力最大値の増加計算）  
⇒このような処理は様々なケースでの利用が考えられ、重複コードを生む可能性がある

## 意味のある単位ごとにクラス化する
引数が多すぎることを解消するには。概念的に意味のあるクラスにカプセル化することが重要。  
魔法力を中心概念ととらえて、魔法力に関係する値のインスタンス変数と操作するメソッドを持たせる。

```java title="意味のある単位にカプセル化した例"
/** 魔法力 */
class MagicPoint {
    private int currentAmount;  // 現在の魔法力残量
    private int originalMaxAmount;  // オリジナルの魔法力残量
    private final List<Integer> maxIncrements;  // 魔法力最大値の増分

    // コンストラクタ省略

    /** @return 現在の魔法力残量 */
    int current() {
        return currentAmount;
    }

    /** @return 魔法力の最大量 */
    int max() {
        int amount = originalMaxAmount;
        for (int each : maxIncrements)
            amount += each;
        return amount;
    }

    /**
     * 魔法力を回復する
     * @param recoveryAmount 回復量
     */
    void recover(final int recoveryAmount) {
        currentAmount = Math.min(currentAmount + recoveryAmount, max());
    }

    /**
     * 魔法力を消費する
     * @param consumeAmount 消費量
     */
    // void consume(final int consumeAmount) { ... }
}
```