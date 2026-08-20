---
tags:
- 設計
- アンチパターン
- ファーストリストコレクション（First-Class Collection）
---

# ファーストリストコレクション
## ファーストリストコレクションとは
以下の2つの機能が備わっている必要がある

- コレクション型インスタンス変数
- 完全性を保証するようにコレクション型インスタンス変数を操作するメソッド

以下にファーストリストコレクションを使用せず、同じようなコレクションを操作するメソッドが実装されたコードを、ファーストリストコレクションに置き換えた例を書く。

### ファーストリストコレクションを使用していないコード
```java title="ファーストリストコレクションを使用していないコード"
/** フィールドマップ上の制御を担当するクラス */
class FieldManager {
    /** メンバーを追加する */
    void addMember(List<Member> members, Member newMember) {
        if (members.stream().anyMatch(member -> member.id == newMember.id))
            throw new RuntimeException("既にパーティに加わっています。");
        if (members.size() == MAX_MEMBER_COUNT)
            throw new RuntimeException("これ以上メンバーを追加できません。");

        members.add(newMember);
    }

    /** パーティメンバーが1人でも生存している場合trueを返す。 */
    boolean partyIsAlive(List<Member> members) {
        return members.stream().anyMatch(member -> member.isAlive());
    }
}
/** ゲーム中の特別イベントを制御するクラス */
class SpecialEventManager {
    /** メンバーを追加する。 */
    void addMember(List<Member> members, Member newMember) {
        members.add(newMember);
    }
}

/** 先頭を制御するクラス */
class BattleManager {
    /** メンバーを追加する */
    boolean membersAreAlive(List<Member> members) {
        boolean result = false;
        for (Member each : members) {
            if (each.isAlive()) {
                result = true;
                break;
            }
        }

        return result;
    }
}
```

### ファーストリストコレクションを使用したコード
```java title="ファーストリストコレクションを使用したコード"
class Party {
    static final int MAX_MEMBER_COUNT = 4;
    private final List<Member> members;

    Party() {
        members = new ArrayList<Member>();
    }

    Party(List<Member> members) {
        this.members = members;
    }

    Party add(final Member newMember) {
        if (members.stream().anyMatch(member -> member.id == newMember.id))
            throw new RuntimeException("既にパーティに加わっています。");
        if (members.size() == MAX_MEMBER_COUNT)
            throw new RuntimeException("これ以上メンバーを追加できません。");

        final List<Member> adding = new ArrayList<>(members);
        adding.add(newMember);
        return new Party(adding);
    }

    boolean isAlive() {
        return members.stream().anyMatch(each -> each.isAlive());
    }

    boolean exists(final Member member) {
        return members.stream().anyMatch(each -> each.id == member.id);
    }

    boolean isFull() {
        return members.size() == MAX_MEMBER_COUNT;
    }
}
```

ファーストリストコレクションを使うことにより、コレクションと操作がカプセル化され保守性の高いコードになる。

### 外部へ渡す場合はコレクションを変更できなくする。
インスタンス変数にfinal修飾子を付けても、コレクション内部のデータは変更可能。  
外部にデータを渡す際は、コレクション要素の追加や削除をできないように不変にする。  
unmodifiableListを使うと変更不可のListを返せる。

```java title="ファーストリストコレクションを使用したコード"
class Party {
    // 中略
    List<Member> members() {
        return Collections.unmodifiableList(members);
    }
```