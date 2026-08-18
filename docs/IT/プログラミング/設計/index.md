# 設計

## 最低限の基本的な検討するべき設計

### 共通
  - プリミティブ型執着をやめる
### クラス
  - 値オブジェクト（カプセル化、不変）
  - staticメソッドの使いどころをちゅいする（横断的関心事）

### コンストラクタ
  - 完全コンストラクタ（生焼けオブジェクト、不正値の混入を弾く）
  - 生成ロジックをカプセル化する（Factory）

### メソッド・関数
  - 出力引数を避ける（副作用）
  - デメテルの法則を守る（尋ねるな命じろ）

### 変数・引数
  - 再代入しない、させない（final）


```mermaid
treeView-beta
├── src/
│   ├── App.tsx :::highlight icon(logos:react) ## main component
│   └── index.ts ## entry point
├── .env ## environment variables
├── Dockerfile
└── package.json
```

```mermaid
mindmap
  root((mindmap))
    Origins
      Long history
      ::icon(fa fa-book)
      Popularisation
        British popular psychology author Tony Buzan
    Research
      On effectiveness<br/>and features
      On Automatic creation
        Uses
            Creative techniques
            Strategic planning
            Argument mapping
    Tools
      Pen and paper
      Mermaid



```
```mermaid
graph LR
    subgraph T[Try]
        subgraph PT[問題の改善のTry]
            pt1[いいい]
            pt2[ううう]
            pt3[えええ]
            pt1 ~~~ pt2
        end
        subgraph KT[続けるTry]
            kt1[甲乙]
            kt2[丙丁]
        end
    end
    subgraph P[Problem]
        s1[ddd]
        s2[eee]
        s3[ggg]
        s1 ~~~ s2
        s1 ~~~ s3
    end

    subgraph K[Keep]
        e1[CCC]
        e2[あああ]
    end
    
    P --> PT
    K --> KT
```
