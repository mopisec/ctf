# GrabCON CTF Writeup

皆様お疲れ様でした。Revを4問解いたのでWriteup公開します。  

## Baby Rev

Ghidraなどのツールが正常に逆コンパイルできないバイナリを渡されて、そこからFlagを求める問題。多分 `gdb` など使えばスムーズに解けますが、泥臭く静的解析のみで解くのであれば以下のような手順を辿ります。

```python
# 逆アセンブル結果を読むと、明らかに怪しいXOR処理があるので試す
>>> hex(0x99dfdf8d ^ 0xdeadbeef)
'0x47726162'
>>> flag_p1 = bytearray(b'\x47\x72\x61\x62')
>>> for ech in flag_p1:
...     print(chr(ech),end='')
...
Grab
# Flagの形式は "GrabCON{xxx}" なので、同じようにFlagを求めていく
# XORのkeyは全て "0xdeadbeef" である
```

Flag: `GrabCON{x0rr3d_4way_3ff10}`

## easy_rev

```
$ ./baby_re_2
Looking for the flag?
Enter the key: 1312389
GrabCON{y0u_g0t_it_8bb31}
```

Flag: `GrabCON{y0u_g0t_it_8bb31}`

## Unknown

`strings` に投げるとUPXパッカーでパックされていることがわかるので、まずは実行バイナリをアンパックする。

```sh
# オリジナルのバイナリは消滅 (上書き) されるので注意
$ ./upx -d med_re_1
```

Ghidraで開いてみて、`Access Granted` が出力された後に呼ばれる関数を中心に静的解析する。

[f:id:m0pisec:20210905093853p:plain]

ここでFlagをデコードしていることがわかるので (`param_1` は定数で `1`) 以下のように解いた。

Solver:

```python
>>> enc_flag = bytearray(b'\x48\x73\x62\x63\x44\x50\x4f\x7c\x74\x75\x73\x32\x6f\x68\x74\x60\x6e\x35\x68\x6a\x64\x60\x35\x35\x31\x67\x32\x7e')
>>> for i in range(len(enc_flag)):
...     print(chr((enc_flag[i] & 0xFF) - 1),end='')
...
GrabCON{str1ngs_m4gic_440f1}
```

Flag: `GrabCON{str1ngs_m4gic_440f1}`

## Unknown 2

Unknownと同じくUPXパッカーでパックされているので、まずアンパックします。

```sh
# オリジナルのバイナリは消滅 (上書き) されるので注意
$ ./upx -d med_re_2
```

Ghidraで開くとGo言語で書かれていそうだったので[gotools](https://github.com/felberj/gotools)を解析環境に導入して解析を進めることにしました。最新版のGhidra (`v10.x`) には対応していないようだったので、古いバージョンのGhidra (`v9.0.4`) も併せて導入しました。

`main.main` を解析すると、何をしても絶対に次の処理 (`main.one` 関数) に進まないことがわかります。`BaseAddress+0x3f96` 辺りにあるCMP命令とJNZ命令をNOPにパッチしておきましょう。

`main.one` 関数を見ていくと文字列を比較していると思われる部分を見つけることができます。細かく解析するのも面倒だったので、NOPで埋めて何を入力してもFlagを出力するようパッチを当てました。

これを実行すればFlagを入手することができます。

```
$ ./med_re_2

___.          .__
\_ |__ _____  |  | _____    ____   ____  ____
 | __ \\__  \ |  | \__  \  /    \_/ ___\/ __ \
 | \_\ \/ __ \|  |__/ __ \|   |  \  \__\  ___/
 |___  (____  /____(____  /___|  /\___  >___  >
     \/     \/          \/     \/     \/    \/


Enter the password:

Here ya go -> GrabCON{ 626c61636b647261676f6e }
```

Flag: `GrabCON{626c61636b647261676f6e}`
