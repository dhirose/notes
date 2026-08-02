# 基本文法
## 数値型

- 符号あり整数型はi、符号なし整数型はu、浮動小数点型はfで先頭を表す。
- 8ビット、16ビット、32ビット、64ビット、128ビットが用意されている。
- 例えば、符号あり整数型で、32ビットはi32となる
- 浮動小数点型は32ビット、64ビットだけ。
- isizeとusizeはアーキテクチャのメモリ空間に依存してサイズが変わる。32ビット環境は32ビット、64ビット環境は64ビット。
- usize型は主に、配列やベクタの要素にアクセスしたり、サイズを表現したりする際に使用する

## 文字列型

### str型

コアライブラリで定義されている文字列型。

文字列そのものの変更はできない。

### String型

標準ライブラリで定義されている文字列型。文字列データの変更や、長さの変更が可能

str型とString型はお互いに型変換が可能。

String→&strに変換するときは、母音たと文字列長をコピーしてスライスと作る。だから文字列自体のコピーは発生せず、メモリを圧迫しない。

&str→Stringに変換するときは、メモリの確保が行われる。

```rust
fn main() {
    let s1: String = String::from("Hello, World!");
    let s2: &str = &s1; //String --> &str
    let s3: String = s2.to_string(); // &str --> String
}

```

## タプル

- 異なる型を収めることができる集合。
- 関数から複数の値を返す時にタプルでまとめて返すことができる。
- タプルに格納された型の全てを含めて1つの型を構成するから、後からタプル内の一部の型を変更することはできない
- 内部の値のアクセスは、.0、.1のようにドットと数値で指定する

```rust
fn main() {
    let mut t = (1, "2");
    t.0 = 2;
    t.1 = "3";
}
```

## 配列

- 特定の型の値を連続に収めた集合
- 配列のサイズは固定でコンパイル時に決まっている必要がある。
- 内部の値にアクセスするときは、[]を使って指定する
- 配列を参照するときは、自動的にスライスとして扱われる。[start..end]のような範囲指定ができて便利

```rust
fn main() {
    let mut a: [i32; 3] = [0, 1, 2];
    let b: [i32; 3] = [0; 3];
    a[1] = b[1];
    a[2] = b[2];
    println!("{:?}", a);
    println!("{:?}", &a[1..3]);
}
```

## ユーザー定義型

- 構造体と列挙型がユーザー定義できる方になる
- 構造体はstructを用いて定義する。
- 列挙型はenumを用いて定義する。
    - それぞれの列挙子にさらにデータを付与することができる。付与するデータの方や構造は全く違うものでも問題ない。

```rust
fn main() {
    let p = Person {
        name: String::from("John"),
        age: 8,
    };

    let e1 = Event::Quit;
    let e2 = Event::MouseDown { x: 10, y: 10 };
}

struct Person {
    name: String,
    age: u32,
}

enum Event {
    Quit,
    KeyDown(u8),
    MouseDown { x: i32, y: i32 },
}

```

## Option型
データが存在する場合と存在しない場合が表現できる列挙型

標準ライブラリでは以下のように定義されている

データがなければNone,あればSome
```rust
pub enum Option<T> {
    None,
    Some(T),
}
```

## Result型
処理結果が成功かエラーかを表現できる列挙型

処理に成功の場合Ok、失敗の場合Errを取得できる。

```rust
pub enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Result型を取得して、matchやif、letを使ってパターンマッチで処理するのが一般的
```rust
fn main() {
    let result: Result<i32, String> = Ok(200);
    match result {
        Ok(code) => println!("code: {}", code),
        Err(err) => println!("Err: {}", err),
    }
}
```
```rust
fn main() {
    let result: Result<i32, String> = Ok(200);
    if let Ok(code) = result {
        println!("code: {}", code);
    }
}
```

### unwrap_or
unwrap_or()を使うとOk()だった場合はそのまま展開して、Err()だった場合は引数で与えた値を返す
```rust
fn main() {
    let result: Result<i32, String> = Ok(200);
    println!("code: {}", result.unwrap_or(-1));

    let result: Result<i32, String> = Err("error".to_string());
    println!("code: {}", result.unwrap_or(-1));
}
```

### end_then
end_then()はOk()だった場合にだけ、指定した関数を実行することができる
```rust
fn main() {
    let result: Result<i32, String> = Ok(200);
    let next_result = result.and_then(func);

    let result: Result<i32, String> = Err("error".to_string());
    let next_result = result.and_then(func);
}

fn func(code: i32) -> Result<i32, String> {
    println!("code: {}", code);
    Ok(100)
}
```

### ?演算子
Okだった場合に値を展開、Errだった場合はそのErrをそのままreturnする

主に関数の中で使われる。

エラーが起きた時に、そのエラーを関数内で処理せず、呼び出し元に委譲する場合に有効

このような手法をシンタックスシュガーと呼ぶ[^1]
[^1]: 複雑でわかりにくい書き方を、意味はそのままに、より簡単に書けるようにする構文

```rust
fn error_handling(result: Result<i32, String>) -> Result<i32, String> {
    let code = result?; // エラーの場合はここでreturn result;
    println!("code: {}", code);
    Ok(100)
}
```

### let-else構文
マッチしなかった場合にelseの処理を実行できる

break,return,panic!などで処理を中断させる必要がある。

```rust
fn error_handling(result: Result<i32, String>) -> Result<i32, String> {
    let Ok(code) = result else {
        let err = result.unwrap_err();
        println!("Error occurred: {}", err);
        return Err(err);
    };
    println!("code: {}", code);
    Ok(100)
}
```

### Vec型
