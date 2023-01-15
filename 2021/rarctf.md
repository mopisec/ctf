# RaRCTF 2021 Writeup

## verybabyrev (rev)

```c
  // わかりやすくするために一部省略しています
  flag = 0x45481d1217111313;
  local_100 = 0x95f422c260b4145;
  local_f8 = 0x541b56563d6c5f0b;
  local_f0 = 0x585c0b3c2945415f;
  local_e8 = 0x402a6c54095d5f00;
  local_e0 = 0x4b5f4248276a0606;
  local_d8 = 0x6c5e5d432c2d4256;
  local_d0 = 0x6b315e434707412d;
  local_c8 = 0x5e54491c6e3b0a5a;
  local_c0 = 0x2828475e05342b1a;
  local_b8 = 0x60450073b26111f;
  local_b0 = 0xa774803050b0d04;
  local_a8 = 0;
  printf("Enter your flag: ");
  fgets((char *)&input,0x80,stdin);
  i = 0;

  for (; i < 0x7f; i = i + 1) {
    *(byte *)((long)&input + (long)i) =
         *(byte *)((long)&input + (long)i) ^ *(byte *)((long)&input + (long)(i + 1));
  }
  iVar1 = memcmp(&flag,&input,0x61)
```

Ghidraでデコンパイル結果を読んでいると、プログラム内部で上記のような処理（XORでエンコーディング）が行われていることがわかります。解けなさそうだと最初は思いましたが、Flagの先頭が `rarctf{` であるという情報を踏まえれば、下記のスクリプトの要領で解けます。

```python
import string

enc = "\x13\x13\x11\x17\x12\x1d\x48\x45"
enc += "\x45\x41\x0b\x26\x2c\x42\x5f\x09"
enc += "\x0b\x5f\x6c\x3d\x56\x56\x1b\x54"
enc += "\x5f\x41\x45\x29\x3c\x0b\x5c\x58"
enc += "\x00\x5f\x5d\x09\x54\x6c\x2a\x40"
enc += "\x06\x06\x6a\x27\x48\x42\x5f\x4b"
enc += "\x56\x42\x2d\x2c\x43\x5d\x5e\x6c"
enc += "\x2d\x41\x07\x47\x43\x5e\x31\x6b"
enc += "\x5a\x0a\x3b\x6e\x1c\x49\x54\x5e"
enc += "\x1a\x2b\x34\x05\x5e\x47\x28\x28"
enc += "\x1f\x11\x26\x3b\x07\x50\x04\x06"
enc += "\x04\x0d\x0b\x05\x03\x48\x77\x0a"
enc += "\x00\x00\x00\x00\x00\x00\x00\x00"
flag = "rarctf{"

for i in range(7,96):
	for c1 in string.printable:
		if ord(flag[i-1]) ^ ord(enc[i-1]) == ord(c1):
			flag += c1

print(flag)
```

このスクリプトを実行すると

```
> python .\verybabyrev.py
rarctf{3v3ry_s1ngl3_b4by-r3v_ch4ll3ng3_u535_x0r-f0r_s0m3_r34s0n_4nd_1-d0nt_kn0w_why_dc37158365}

```

Flagを入手することができます。

## Dotter (rev)

問題名の時点で.NET関係だと推測しつつGhidraで解析してみると、やはり.NET関係だったのでdotPeekを用いてデコンパイルして解析しました。内部で行われている処理はとても単純ですし、最悪解析しなくても解ける分 `verybabyrev` よりも簡単かもしれませんね。

```python
check = "-|....|.|/|..-.|.-..|.-|--.|/|..|...|/|---|.---|--.-|-..-|.|-.--|...--|..-|--|--..|.....|.--|..|--|.-..|.|.-..|.....|....-|-|.-|.....|-.-|--...|---|.-|--..|-|--.|..---|..---|--...|--.|-...|--..|..-.|-....|-.|.-..|--.-|.--.|.|--...|-|-....|.--.|--..|--...|.-..|.....|-|--.|-.-.|-.|-..|-...|--|--|...--|-..|.-|-.|.-..|.....|/|-...|.-|...|.|...--|..---"
fclist = check.split('|')
flag = ""
for fc in fclist:
	if fc == "/": flag += ' '
	elif fc == ".-": flag += 'A'
	elif fc == "-...": flag += 'B'
	elif fc == "-.-.": flag += 'C'
	elif fc == "-..": flag += 'D'
	elif fc == ".": flag += 'E'
	elif fc == "..-.": flag += 'F'
	elif fc == "--.": flag += 'G'
	elif fc == "....": flag += 'H'
	elif fc == "..": flag += 'I'
	elif fc == ".---": flag += 'J'
	elif fc == "-.-": flag += 'K'
	elif fc == ".-..": flag += 'L'
	elif fc == "--": flag += 'M'
	elif fc == "-.": flag += 'N'
	elif fc == "---": flag += 'O'
	elif fc == ".--.": flag += 'P'
	elif fc == "--.-": flag += 'Q'
	elif fc == ".-.": flag += 'R'
	elif fc == "...": flag += 'S'
	elif fc == "-": flag += 'T'
	elif fc == "..-": flag += 'U'
	elif fc == "...-": flag += 'V'
	elif fc == ".--": flag += 'W'
	elif fc == "-..-": flag += 'X'
	elif fc == "-.--": flag += 'Y'
	elif fc == "--..": flag += 'Z'
	elif fc == ".----": flag += '1'
	elif fc == "..---": flag += '2'
	elif fc == "...--": flag += '3'
	elif fc == "....-": flag += '4'
	elif fc == ".....": flag += '5'
	elif fc == "-....": flag += '6'
	elif fc == "--...": flag += '7'
	elif fc == "---..": flag += '8'
	elif fc == "----.": flag += '9'
	elif fc == "-----": flag += '0'

print(flag)
```

かなり長いですが、恐らく想定解だと思います。実行すると以下のようなメッセージが出力されます。

```
> python .\dotter.py
THE FLAG IS OJQXEY3UMZ5WIMLEL54TA5K7OAZTG227GBZF6NLQPE7T6PZ7L5TGCNDBMM3DANL5 BASE32
```

Base32でデコードしてみると `rarctf{d1d_y0u_p33k_0r_5py????_fa4ac605}` というFlagが手に入ります。

## RaRPG (rev)

```
* : プレイヤーの駒
X : 別マップにジャンプするマス
W : 壁
~ : 不明
```

典型的なゲームでチートするタイプの問題です。クライアント側のバイナリにパッチを当てれば簡単に解くことができます。まずIDAなどでクライアント側のバイナリを解析して、移動周りの処理をしている部分を探します。

今回は２マスずつ移動するように調整してみました。
私の場合は何故かGhidraを使ってパッチを当てると `Segmentation Fault` などが発生してパッチされたバイナリを実行できないのでIDAを使用していますが、Ghidra単体でもパッチは可能なはずです。詳しい原因はわかっていないので、今度 `010 Editor` で両者（IDAを用いてパッチしたバイナリとGhidraを用いてパッチしたバイナリ）を比較してみようと思います。

## Infinite Free Trial (rev)

シリアルコードのようなものを見つければ良いらしいです。

Ghidraで解析してみると、CRCとXORの二段階のチェックでシリアルコードのValidationを行っていることがわかりました。  
少し悩みましたが、アルゴリズム的にBruteforceで対応できそうなので、こんなスクリプトを書いてみました。

```python
import string

xor_check = [0x09, 0x16, 0x17, 0x0f, 0x17, 0x56, 0x16, 0x44, 0x3a, 0x18, 0x53, 0x6f, 0x14, 0x03, 0x2a, 0x06, 0x6f, 0x31, 0x1c, 0x47, 0x2a, 0x06, 0x2d, 0x5f, 0x51, 0x1b, 0x00, 0x46, 0x4a, 0x00, 0x04, 0x55, 0x66, 0x50, 0x01, 0x4c]
flag = "rarctf"

for i in range(len(xor_check)):
    for fc in string.printable:
        if ord(flag[i]) ^ ord(fc) == xor_check[i]:
            flag += fc

print(flag)
```

これを実行すると

```
> python .\iftsolver.py
rarctf{welc0m3_t0_y0ur_new_tr14l_281099b9}
```

Flagを入手することができます。

## Very TriVial ReVersing (rev - 解けていない)

V言語で書かれたプログラムらしいということを掴んで、Ghidraでコードを読んでいましたが複雑すぎて理解できず。色々と試行錯誤していたら同時並行で解析していた `Jammy's Old Infra` 同様、タイムアップで終わってしまいました。他の参加者の方のWriteupを読んで勉強したいと思います。

## Jammy's Old Infra (rev - 解けていない)

APK問なので最初に `apktool` でデコンパイルしたコードを読んでいると、ユーザー名とパスワードのValidationをライブラリに投げていることがわかりました。 `libnative-lib.so` というライブラリが該当する処理を担っているようだったので、Ghidraを用いて解析することにしました。

内部でAES暗号で暗号化されたユーザー名とパスワードを保有しているようだったので、まず共通鍵を取得するためのスクリプトを作成しました。

```python
dat = [0xff3ff9cc, 0xffffffce, 0xff88b0f8, 0xffffff58, 0xdda07cdd, 0xffffff99. 0x8a320600, 0xffffffc9, 0x25b54dda, 0xffffffa8, 0xbe11c670, 0xffffff32, 0x7106bc1a, 0xfffffff0, 0x6525ce47, 0xfffffff2, 0x9a345e8d, 0xffffffc4, 0x665ef7f9, 0xffffff96, 0xa6029980, 0xffffff25, 0x686a361, 0xffffffc1, 0xf985df98, 0xffffff94, 0xd96ab842, 0xffffff7e, 0x26a0bf30, 0xffffffa1, 0x1c38dbd8, 0xffffff9e]
res = [0x30] * (0xf + 1)

# 最後まで解けていないので間違っているかもしれません
for i in range(0, 0x20, 4):
    res[i] = ~dat[i+1]
    res[i+1] = ~dat[i+2]
    res[i+1] = ~dat[i+3]
    res[i+1] = ~dat[i+4]
```

次にユーザー名を復号しようと思ったのですが、このタイミングで時間切れになってしまいました。  
こちらも他の参加者の方のWriteupを読んでどんな方針で解析していけばいいのか勉強したいと思います。

## lemonthinker (web)

Infinite Free Trialを解いた後に `lemonthinker` が簡単だという噂を聞いて、そこまで得意ではないweb問ですが手を出してみることにしました。

配布されたソースコードを読むと、明らかにOSコマンドインジェクションの脆弱性がありそうな部分が見つかるので、適当なペイロードを送信してみました。

するとネギの画像を返されるので `generate.py` を見てみると、`rarctf` が出力に入っていると弾かれることがわかります。そこで `$(cat ../flag.txt | cut -c 2-8)` のようなペイロードを投げてみると

問題なくFlagの断片を入手することができました。最初の一文字以外をすべて出力しようとしたところ、画像上のキャラクターが邪魔でうまくいかなかったので、そのまま数文字ずつFlagを集めて、つなぎ合わせればflagが `rarctf{b451c-c0mm4nd_1nj3ct10n_f0r-y0u_4nd_y0ur-l3m0nth1nk3rs_d8d21128bf}` だとわかります。
