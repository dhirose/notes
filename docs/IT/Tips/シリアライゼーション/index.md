---
tags:
  - Tips
---

# シリアライゼーション
## シリアライゼーションの動作仕様
### 基本
- Serializableインターフェースを実装していないオブジェクトをシリアライゼーションさせようとするとエラーになる。
- Serializableインターフェースには定義されているメソッドがない。ただのマーカーインターフェース。

### バージョンチェック
シリアライゼーションされたデータの中には、シリアライゼーションされた時点のクラスのバージョンが含まれる。シリアライゼーションされたデータを読み込んでオブジェクトに戻す際に、クラスに変更があったか（例えば、フィールド名が変更されているとか）をチェックするため。

最新のクラスのバージョンと、シリアライゼーションされた時点ののクラスのバージョンとを比較し、バージョンが違っていたら不備のあるオブジェクトを作らないようにエラーを発生させる。

クラスのバージョンは、serialVersionUIDという名前のstaticフィールドで、任意のバージョン番号を指定することができる。

```java title="serialVersionUID"
public class OrderInput implements Serializable {
  private static final long serialVersionUID = 123456789L;
}
```

#### serialVersionUIDを記述しない場合の動作
serialVersionUIDを記述しない場合は、クラスの情報を元にJava側で自動的にバージョン番号が生成される。  
ただし、Serializableを実装しているのに、serialVersionUIDフィールドを記述しない場合にはコンパイラが警告を出す。  
警告を出さなくてよい旨をコンパイラに伝えるには`@SuppressWarnings("serial")`を付ける。


```java title='@SuppressWarnings("serial")'
@SuppressWarnings("serial")
public class OrderInput implements Serializable { ... }
```

## Javaのセッションスコープのシリアライゼーション
セッションスコープの中のオブジェクトはAPサーバのメモリ上に保持されるが、APサーバのメモリがひっ迫すると、セッションスコープの中のオブジェクトをいったんファイルに対比させメモリを解放させようとする。（対比させたデータは、必要に応じて読み込んで、Javaのオブジェクトに戻す）  

```java title="Javaのセッションスコープのシリアライゼーションの例"
@Component
@SessionScope
@suppressWarnings("serial")
public class ReservationSession implements Serializable {
    private ReservationInput reservationInput;
    public ReservationInput getReservationInput() { return reservationInput; }
    public ReservationInput setReservationInput(ReservationInput reservationInput) { return this.reservationInput = reservationInput; }
    ...
}
```