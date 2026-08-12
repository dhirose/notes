---
tags:
  - プログラミング
  - 設計
---

# Comsumerとメソッドチェーンを組み合わせたシンプル実装

## 実装例
```text title="パッケージ構成"
C:\Users\hirok\work\SampleJava\src
│  ItemTest.java
│
└─sample
        Item.java
```

```java title="デフォルトパッケージ"
import sample.Item;

public class ItemTest {
    public static void main(String[] args) {
        Item.save(item -> {
            item.id("A10")
                    .name("LX100G")
                    .description("sample")
                    .price(100);
        });
    }
}

```
```java title="sampleパッケージ"
package sample;

import java.util.function.Consumer;

public class Item {
    private String id;
    private String name;
    private String description;
    private int price;

    public Item id(String id) {
        this.id = id;
        return this;
    }

    public Item name(String name) {
        this.name = name;
        return this;
    }

    public Item description(String description) {
        this.description = description;
        return this;
    }

    public Item price(int price) {
        this.price = price;
        return this;
    }

    @Override
    public String toString() {
        return "Item{" +
                "id='" + id + '\'' +
                ", name='" + name + '\'' +
                ", description='" + description + '\'' +
                ", price=" + price +
                '}';
    }

    public static void save(Consumer<Item> con) {
        Item item = new Item();
        con.accept(item);
        System.out.println("save:" + item);
    }

}

```