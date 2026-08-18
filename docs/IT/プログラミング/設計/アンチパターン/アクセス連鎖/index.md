---
tags:
- 設計
- アンチパターン
- カプセル化
- デメテルの法則
- 尋ねるな命じろ（Tell, Don't Ask.）
---

# アクセス連鎖
## 弊害
このようにドット繋ぎでアクセスすると、関連ロジックがバラバラになる。結果アクセスしている箇所に変更が生じた場合、使用箇所すべてにその影響を受ける。  
アクセス連鎖で内部詳細を渡り歩くのは、デメテルの法則に違反する。


```java title="アクセス連鎖が多い例"
    /**
     * 鎧を装備する
     * @param memberId 装備変更したいメンバーID
     * @param newArmor 装備する鎧
     */
    void equipArmor(int memberId, Equipment newArmor) {
        if (party.members.get(memberId).equipments.canChange)
            party.members.get(memberId).equipments.armor = newArmor;
    }
```

## 尋ねるな命じろ（Tell, Don't Ask.）
他のオブジェクトの内部状態を尋ねたり、その状態に応じて呼び出し側が判断したりするのではなく、呼び出し側がただメソッドで命ずるだけで、命令された側で適切な判断や制御をするような設計。

```java title="詳細なロジックを呼ばれる側に実装した例"
/** 装備中の防具一覧 */
class Equipments {
    private boolean canChange;
    private Equipment head;
    private Equipment armor;
    private Equipment arm;

    /**
     * 鎧を装備する
     * @param newArmor 装備する鎧
     */
    void equipArmor(final Equipment newArmor) {
        if (canChange)
            armor = newArmor;
    }

    /** 全装備を解除する */
    void deactivateAll() {
        head = Equipment.EMPTY;
        armor = Equipment.EMPTY;
        arm = Equipment.EMPTY;
    }
}
```

## デメテルの法則
最小知識の法則とも呼ばれる。
「自分が直接関係するオブジェクトだけやり取りするべき」というルール。
他のオブジェクトの内部構造に詳しくならないようにする
「他人の友達には話しかけない」
[お前のデメテルの法則は間違っている ～getter/setterの必要性～](https://tech-blog.rakus.co.jp/entry/20200701/programming)