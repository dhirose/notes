---
tags:
- 設計
- アンチパターン
- カプセル化
- 出力引数
- 副作用
---

# 出力引数
## 出力引数とは
メソッドや関数に渡された引数のうち、メソッド内部で処理され、結果がその引数を通じて呼び出し元に返されるものを **出力引数** と呼ばれる

## 弊害
カプセル化された構造でない。
参照型引数が変更されることが外部（呼び出し元）からわからない。

```java title="出力引数の例"
class ActorManager {
    void shift(Location location, int shiftX, int shiftY) {
        location.x += shiftX;
        location.y += shiftY;
    }
}

class Location {
    int x;
    int y;
}
```

引数は入力値として渡されるもの。  
このように出力地として扱ってしまうと、メソッド内部のロジックを読んで確認しないといけない。  
メソッドの中身をいちいち気にしなければならない構造は、ロジックを読み解く時間をいたずらに同化させ、可読性を低下させる。

```java title="引数を変更しない構造へ改善"
class Location {
    int x;
    int y;

    Location(final int x, final int y) {
        this.x = x;
        this.y = y;
    }

    void shift(final int shiftX, final int shiftY) {
        this.x += shiftX;
        this.y += shiftY;
    }
}
```