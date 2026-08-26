---
tags:
- java
---

# Systemクラス

## setPropertyスタティックメソッド
javaコード上でシステムプロパティを設定する。
```java
System.setProperty("spring.profiles.active","production");
```

もし、javaコード上でなく、起動時にシステムプロパティを設定する場合は、以下のようになる
```powershell
java -Dspring.profiles.active=production mainメソッドを持つクラス名
```