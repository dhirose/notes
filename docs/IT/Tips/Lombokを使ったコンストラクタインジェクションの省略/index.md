---
tags:
  - Tips
  - Lombok
  - Spring
---

# Lombokを使ったコンストラクタインジェクションの省略
Lombokを使えば、Springフレームワークなどのコンストラクタインジェクションの記述を省略することができる。

```java 
@Service
public class FooService {
    private final BarService barService;

    public FooService(BarService barService) {
        this.barService = barService;
    }
}
```

```java title="Lombokを使って省略した記述"
@Service
@RequiredArgsConstructor
public class FooService {
    private final BarService barService;
}
```
