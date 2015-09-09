# 使ったコマンド

asciiのみを利用したファイルはUTF8と同じバイト列だが、utf16とは別のバイト列。
```
$ iconv -f UTF-8 -t ASCII text-utf8-containing-only-ascii.txt
aiueo
$ iconv -f UTF-8 -t ASCII text-utf8-containing-only-ascii.txt > text-ascii-containing-only-ascii.txt
$ iconv -f UTF-8 -t UTF-16 text-utf8-containing-only-ascii.txt > text-utf16-containing-only-ascii.txt
$ od -c -t x1 text-utf8-containing-only-ascii.txt
0000000    a   i   u   e   o  \n
           61  69  75  65  6f  0a
0000006
$ od -c -t x1 text-ascii-containing-only-ascii.txt
0000000    a   i   u   e   o  \n
           61  69  75  65  6f  0a
0000006
$ od -c -t x1 text-utf16-containing-only-ascii.txt
0000000  376 377  \0   a  \0   i  \0   u  \0   e  \0   o  \0  \n
           fe  ff  00  61  00  69  00  75  00  65  00  6f  00  0a
0000016
$ echo "aiueo\nあいうえお" > text-utf8-containing-japanese.txt
$ cat text-utf8-containing-japanese.txt
aiueo
あいうえお
$ od -c -t x1 text-utf8-containing-japanese.txt
0000000    a   i   u   e   o  \n  あ  **  **  い  **  **  う  **  **  え
           61  69  75  65  6f  0a  e3  81  82  e3  81  84  e3  81  86  e3
0000020   **  **  お  **  **  \n
           81  88  e3  81  8a  0a
0000026
```

UnicodeにあるがASCIIにはない文字列が存在している場合、ASCIIに変換できない。UTF-8はUnicodeの符号化方式なので、ASCIIのバイト列には出来ない。
```
$ iconv -f UTF-8 -t ASCII text-utf8-containing-japanese.txt
aiueo
iconv: text-utf8-containing-japanese.txt:2:0: cannot convert
```

Unicodeの第1面以上にあるものはutf8では4バイトで表現
```
$ od -c -t x1 text-utf8-containing-over-bmp.txt 0000000    a   i   u   e   o  \n  あ  **  **  い  **  **  う  **  **  え
           61  69  75  65  6f  0a  e3  81  82  e3  81  84  e3  81  86  e3
0000020   **  **  お  **  **  \n  𠀀  **  **  **  \n
           81  88  e3  81  8a  0a  f0  a0  80  80  0a
0000033
```

latin1が符号化方式と一致することを確認
```
od -c -t x1 text-latin1-containing-all-latin1-characters.txt
```

# 気づいたこと
ASCIIは符号化文字集合であり符号化方式。だけどUnicodeは符号化文字集合であり、UTF-8は符号化方式。

# 参考
https://ja.wikipedia.org/wiki/Unicode%E4%B8%80%E8%A6%A7%E8%A1%A8 これから文字セットを取得できる
