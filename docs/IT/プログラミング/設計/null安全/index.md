---
tags:
- 設計
- null安全
---

# null安全
## nullを返さない、渡さない、代入しない
null例外やnullチェックを避けるために、nullが入り込まない設計にすることが重要。このような仕組みを **null安全** という
具体的には以下を満たすようにするとよい。

- nullを返さない：メソッドの戻り値としてnullを渡さない
- nullを渡さない：メソッドの引数にnullを渡さない
- nullを代入しない：変数にnullを代入しない

nullが入り込む前提でロジックを組むと、いたるところでnullチェックが必要になる。  
nullは参照を持っていない状態を表すものであって、未設定の状態をnullとして実装するべきではない。  
未設定という状態を設定するべき。

以下は、null安全を実装したコード例。  
EMPTYという未設定を表す状態を設定するようにしている。

```java title="null安全を実装したコード例"
public class Main {
    public static void main(String[] args) {

        Equipment arm = new Equipment("普通の腕装備",10,10,10);
        Member member = new Member(Equipment.EMPTY, Equipment.EMPTY,arm);
    }
}


class Equipment {
    static final Equipment EMPTY = new Equipment("装備なし", 0, 0, 0);

    final String name;
    final int price;
    final int defence;
    final int magicDefence;

    Equipment(final String name, final int price, final int defence, final int magicDefence) {
        if (name.isEmpty())
            throw new IllegalArgumentException("無効な名前");

        this.name = name;
        this.price = price;
        this.defence = defence;
        this.magicDefence = magicDefence;
    }
}

class Member {
    private Equipment head;
    private Equipment body;
    private Equipment arm;

    Member(Equipment head, Equipment body, Equipment arm) {
        this.head = head;
        this.body = body;
        this.arm = arm;
    }

    void takeOffAllEquipment() {
        head = Equipment.EMPTY;
        body = Equipment.EMPTY;
        arm = Equipment.EMPTY;
    }
}
```