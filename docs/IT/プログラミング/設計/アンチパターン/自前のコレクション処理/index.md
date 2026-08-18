---
tags:
- 設計
- アンチパターン
- コレクション
- 車輪の再発明
- 四角い車輪の再発明
---

# 自前のコレクション処理
```java title="for分を使った判定"
boolean hasPrisonKey = false;
for (Item each : item) {  // itemsはList<Item>型
    if (each.name.equals("牢屋の鍵")) {
        hasPrisonKey = true;
        break;
    }
}
```
```java title="StreamAPIを使った判定"
boolean hasPrisonKey = items.stream().anyMatch(item -> item.name.equals("牢屋の鍵"));
```

上記は同じ結果を得ることができて、StreamAPIを使った方はforもifも書かず簡潔に書ける。  
このようにanyMatchを知っていれば、複雑なロジックを自前で実装する必要はないが、逆に知らなければわざわざ自前で実装する羽目になって複雑になる。

コレクション以外にも様々な便利なメソッドが存在するため、まずは探した方がいい。

## 車輪の再発明
広く使われ確立している解決方法があるにもかかわらず、それを知らずに、または意図的に無視して同じような解決方法を作り出してしまうことを **車輪の再発明** という

## 四角い車輪の再発明
車輪の再発明で、すでにある物より役に立たないものを作り足すことを **四角い車輪の再発明** という

車輪の再発明を避けるには、フレームワークやライブラリを丁寧に調査することが重要。