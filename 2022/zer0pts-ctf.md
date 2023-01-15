# zer0pts CTF 2022 Writeup

主催者およびスポンサーの皆様、素晴らしいCTFを開催してくださりありがとうございました。

## service (Rev)

入力した文字列から2文字ずつSHA256ハッシュを求めて、ハードコードしてあるものと比較している。IATが書き換えられており、IDA等では正しい逆アセンブル結果が表示されないが、デバッガー (x64dbg, WinDbgなど) を用いることで Cryptographic API (Win32) を呼び出していることがわかる。

以下はその内容をもとに記述したソルバである。

```python
import string
import hashlib

flag = ""

with open('chall.exe', 'rb') as cf:
    cf.seek(0x2220)
    dat = cf.read(0x261f-0x2220+1)

for i in range(0, 32*32, 32):
    for c1 in string.printable:
        for c2 in string.printable:
            pos = c1 + c2
            if dat[i:i+32].hex() == hashlib.sha256(pos.encode()).hexdigest():
                flag += pos

print(flag)
```
