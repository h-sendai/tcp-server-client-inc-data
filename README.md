# TCP server and client

サーバー側が送るデータとして
4バイト整数を順次インクリメントしたデータを送る。
2^32-1を超えたら0にもどる。

サーバーから送るデータについてはバッファはsizeof(int)の
倍数バイト分を確保することにした。

クライアント側は-vオプションをつけるとデータの検証を行う。
データの検証関数ではsizeof(int)の倍数にならないバイトがあった場合には
static変数を使って前の検証時にあまったバイト長、バイトデータを保存して
いる。
今のコードではreadn()を使ってソケットを読んでいるので-b 16kなどと指定
すれば中途半端なreadはおきないはずだが将来read()で読む場合にも
使えるようにしてある。

## サーバーオプション

```
Usage: server [-b bufsize (16k)] [-s sleep_usec (0)] [-q] [-E do_error_at] [-S so_sndbuf]
-b bufsize:     one send size (may add k for kilo, m for mega)
-s sleep_usec:  sleep useconds after write
-q:             enable quick ack
-E do_error_at: send error data at do_error_at times write()
-S: so_sndbuf:  set socket send buffer size
```

-E 1024 とすると、1024回目(0から数えて)のwrite()で先頭4バイトが
0xffffffff というデータを送る。クライアント側ではこれを検出できる
はずである。-E 1k のようにk, mのサフィックスを使うことが可能。

