---
tags:
  - ハック
---

# フォルダのツリー表示（Windows）
## フォルダ階層のみ
```powershell
C:\Users\hirok\work\SampleJava> tree
フォルダー パスの一覧
ボリューム シリアル番号は 0000001B A49C:AE88 です
C:.
├─.idea
│  └─codeStyles
├─out
│  └─production
│      └─SampleJava
│          └─sample
└─src
    └─sample
C:\Users\hirok\work\SampleJava>
```

## ファイルも含めて階層表示
```powershell
C:\Users\hirok\work\SampleJava> tree /F
フォルダー パスの一覧
ボリューム シリアル番号は 0000003C A49C:AE88 です
C:.
│  .gitignore
│  SampleJava.iml
│
├─.idea
│  │  .gitignore
│  │  checkstyle-idea.xml
│  │  misc.xml
│  │  modules.xml
│  │  workspace.xml
│  │
│  └─codeStyles
│          codeStyleConfig.xml
│          Project.xml
│
├─out
│  └─production
│      └─SampleJava
│          │  ItemTest.class
│          │  Main.class
│          │
│          └─sample
│                  Factory.class
│                  Item.class
│                  Sample.class
│                  Test.class
│
└─src
    │  ItemTest.java
    │  Main.java
    │
    └─sample
            Factory.java
            Item.java
            Sample.java
            Test.java

C:\Users\hirok\work\SampleJava>
```

## カレントディレクトリの表示をカレントディレクトリのフルパス表示
```powershell
C:\Users\hirok\work\SampleJava> tree /F | ForEach-Object { $_ -replace '^C:\.$', (Get-Location).Path }
フォルダー パスの一覧
ボリューム シリアル番号は 000000FE A49C:AE88 です
C:\Users\hirok\work\SampleJava
│  .gitignore
│  SampleJava.iml
│
├─.idea
│  │  .gitignore
│  │  checkstyle-idea.xml
│  │  misc.xml
│  │  modules.xml
│  │  workspace.xml
│  │
│  └─codeStyles
│          codeStyleConfig.xml
│          Project.xml
│
├─out
│  └─production
│      └─SampleJava
│          │  ItemTest.class
│          │  Main.class
│          │
│          └─sample
│                  Factory.class
│                  Item.class
│                  Sample.class
│                  Test.class
│
└─src
    │  ItemTest.java
    │  Main.java
    │
    └─sample
            Factory.java
            Item.java
            Sample.java
            Test.java

C:\Users\hirok\work\SampleJava>
```

`tree /F .`でもできるがルートディレクトリの表示が全て大文字になる。

## 補足
不要なディレクトリの非表示オプションなどはない