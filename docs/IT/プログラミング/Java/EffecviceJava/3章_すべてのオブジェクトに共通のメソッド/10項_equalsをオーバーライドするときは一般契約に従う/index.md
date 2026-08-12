# equalsをオーバーライドするときは一般契約に従う-finallyよりもtry-with-resourcesを選ぶ

## equalsの間違ったオーバーライド
以下のケースに当てはまる場合は、オーバーライドしないのが正しい

- クラスの個々のインスタンスが本質的に一意である。
- クラスが「論理的等価性」の検査を提供する必要がない。
- 親クラスが既にequalsをoverrideしており、親クラスの振る舞いがこのクラスに対して適切である。
- クラスがprivateあるいはパッケージプライベートであり、そのequalsが呼び出されないことが明確である。

# equalsのオーバーライドが必要なケース
- クラスが単なる同一性とは異なる論理的等価性という概念を持つ、かつ、親クラスがequalsをオーバーライドしていない。つまり値クラス（ValueObject）


try-with-resourcesを使わず、try-finallyを使う場合、以下の弊害がある

- closeに失敗する可能性がある
    - closeの失敗をハンドリングにまたtryで囲わなければいけない
- 長い
- `out.close()`、`in.close()`の両方で例外が発生した場合は、最初の例外がスタックトレースから隠されてしまう。

```java title="try-finally"
    static void copy(String src, String dst) throws IOException {
        InputStream in = new FileInputStream(src);
        try {
            OutputStream out = new FileOutputStream(dst);
            try {
                byte[] buf = new byte[BUFFER_SIZE];
                int n;
                while ((n = in.read(buf)) >= 0)
                    out.write(buf, 0, n);
            } finally {
                out.close();
            }
        } finally {
            in.close();
        }
    }
```

try-with-resourcesであれば、上記弊害を解消できる
```java title="try-with-resources"
    static void copy(String src, String dst) throws IOException {
        try (InputStream in = new FileInputStream(src);
             OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0)
                out.write(buf, 0, n);
        }
    }
```