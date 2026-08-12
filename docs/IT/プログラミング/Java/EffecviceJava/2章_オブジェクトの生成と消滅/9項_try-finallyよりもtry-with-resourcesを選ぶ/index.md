# try-finallyよりもtry-with-resourcesを選ぶ

## try-with-resourcesを使う理由


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