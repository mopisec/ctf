# はじめに

3819点で9位（88人参加((WELCOME問を解いた人数))）でした。普段解いていないジャンルの問題も沢山解けて楽しかったです。運営の皆さん、ありがとうございました。

# MISC

## caesar (68 solves)

```
ガイウス・ユリウス・カエサル Gaius Iulius Caesar

[caesar_source.py]    [caesar_output.txt]
```

問題名から、シーザー暗号でFlagが暗号化されているのだろうと推測できました。`caesar_source.py` の処理を確認してみると、通常のシーザー暗号とは異なるテーブルで変換していることがわかります。

```python
ascii_all = ''
for i in range(len(ascii_uppercase)):
    ascii_all = ascii_all + ascii_uppercase[i] + ascii_lowercase[i]

letter = ascii_all + digits + punctuation
```

処理自体は複雑なものではないので、`caesar_source.py` と逆の処理をするようなスクリプトを書いて実行することで、Flagが得られました。

```python
from string import ascii_uppercase, ascii_lowercase, digits,punctuation

def decode(ciphertext):
    plaintext = ''
    for i in ciphertext:
        index = letter.index(i)
        plaintext = plaintext + letter[(index - 14) % len(letter)]
    return plaintext

ascii_all = ''
for i in range(len(ascii_uppercase)):
    ascii_all = ascii_all + ascii_uppercase[i] + ascii_lowercase[i]

letter = ascii_all + digits + punctuation
with open('caesar_output.txt', 'r') as cipher_file:
    ciphertext = cipher_file.read()

plaintext = decode(ciphertext[:-1])
print(plaintext)
```

`UECTF{Th15_1s_a_b1t_Diff1Cult_c43seR}`

## redaction gone wrong 1 (71 solves)

```
NOBODY SHOULD JUST COPY AND PASTE MY FILES!

何人もコピペすべからず！

[challenge.pdf]
```

LibreOffice Drawで `challenge.pdf` を開き、PDF内のFlagを隠している黒い四角形を移動もしくは削除することでFlagが得られました。他にも解き方はたくさんあり...

- PDFをテキスト形式に変換するツールを使用する
- 問題文にて示唆されている通り、適当な場所をC&Pして内容をテキストファイルに貼り付ける

といった方法でも解けると思います。

`UECTF{PDFs_AR3_D1ffiCulT_74d21e8}`

## redaction gone wrong 2 (54 solves)

```
We have found this image floating on the internet. Can you tell us what is the redacted text?

インターネット上でこの画像を見つけた。隠されたテキストは何だろうか？

[flag.png]
```

[StegOnline](https://stegonline.georgeom.net/upload)に `flag.png` をアップロードして、"LSB Half" オプションを選択することでFlagが薄く見える状態になりました。

`UECTF{N3ver_ever_use_A_p3n_rofl}`

## GIF1 (59 solves)

```
GIFアニメの中にフラグを隠したよ。え？隠れてないって？そんなぁ…

I tried to hide the flag with GIF animation. Huh? Not hidden...? Oh no...

[UEC_Anime.gif]
```

ffmpeg等のツールで `UEC_Anime.gif` をフレームごとに画像ファイルに分割することで、Flagが含まれている画像ファイルが得られました。

```
ffmpeg -i UEC_Anime.gif -vsync 0 frame%d.png
```

`UECTF{G1F_4N1M4T10NS_4R3_GR34T!!}`

## GIF2 (30 solves)

```
今度こそGIFアニメにフラグを隠したよ。人の目で見えるものだけが全てじゃないよ。

I tried to hide the flag in a GIF animation. It's not all about what people can see.

[UECTF.gif]
```

[StegOnline](https://stegonline.georgeom.net/upload)に `UECTF.gif` をアップロードして、"LSB Half" オプションを選択することでFlagが薄く見える状態になりました。

`UECTF{TH1S_1S_TH3_3NTR4NC3_T0_ST3G4N0GR4PHY}`

## OSINT (13 solves)

```
There is this link to a Twitter account. However, Twitter says that "This account doesn’t exist." Could you somehow use your magic to find this person? I'm pretty sure he's still using Twitter. Thanks!!

あるTwitterアカウントへのリンクがありました。アクセスすると"このアカウントは存在しません"と表示されて困っているんだ...😖 他の情報源によるとTwitterをまだやっているはずなんだけどなぁ🤔

https://twitter.com/__yata_nano__
```

[Wayback Machine](https://archive.org/web/)でURLを検索してみると、10月の時点のキャプチャが存在することがわかります。HTMLソースを確認してみると、アカウントIDを見つけることができました。

```
    "additionalName": "__yata_nano__",
    "description": "",
    "givenName": "name",
    "homeLocation": {
      "@type": "Place",
      "name": ""
    },
    "identifier": "1585261641125416961",
```

これを[idtwiというサービスで検索する](https://idtwi.com/1585261641125416961)ことで現在のアカウントを特定することができました。

11月19日の午後7時59分のツイートに書かれているPastebinのURLにアクセスすることでFlagが得られます。

[https://twitter.com/ftceu/status/1593921778417532928?s=20&t=9u8YM1psQs0vO4mgb-7pfA:embed]

`UECTF{ur_a_tw1tter_mast3r__arent_y0u}`

## WHEREAMI (16 solves)

```
あなたの元に友人から「私はどこにいるでしょう？」という件名の謎の文字列が書かれたメールが送られてきました。 さて、これは何を示しているのでしょうか？

You receive an email from your friend with a mysterious string of text with the subject line "Where am I?" Now, what does this indicate?

[mail.txt]
```

```
ヒント:
彼はこの文字列はPlus codeだと言っていましたがよく分かりません。
He said it was a "plus code", but I have no idea what plus code is. Is any string with "+" character in it a "plus code"???
```

ヒントを確認することで、配布された `mail.txt` 内の文字列がPlus codeという場所を表すコードであることがわかります。それが約550個あり、かつ各コードの示す地点があまり離れていないことから、地図上にコードが示す地点をプロットしていくことで、Flagが得られると考えました。以下は配布ファイルを読み込み、それから得られた緯度・経度を `folium` というライブラリを用いて地図上にプロットするスクリプトです。

```python
from openlocationcode import openlocationcode as olc
import folium

PLUS_CODE_FILE = 'mail.txt'

with open(PLUS_CODE_FILE, 'r') as pcfile:
    plus_code = pcfile.readlines()

flag_map = folium.Map(tiles='OpenStreetMap')
for pc in plus_code:
    decoded = olc.decode(pc[:-1])
    folium.Marker(location=[decoded.latitudeLo, decoded.longitudeLo]).add_to(flag_map)

flag_map
```

これをJupyter Notebookで実行することで、Flagが得られます。

[f:id:m0pisec:20221120145801p:plain]

`UECTF{D1d_y0u_Kn0w_aB0ut_Km1?}`

# FORENSICS

## Deleted (53 solves)

```
USBメモリに保存してたフラグの情報消しちゃった。このイメージファイルからどうにか取り出せないものか…

I have deleted the flag information I saved on my USB stick. I wonder if there is any way to retrieve it from this image file...

[image.raw]
```

FTK Imagerで配布されたイメージファイルを読み込むと、`flag.png` というFlagが書かれたファイルを確認することができます。

[f:id:m0pisec:20221119010452p:plain]

`UECTF{TH1S_1M4G3_H4S_N0T_B33N_D3L3T3D}`

## compare (33 solves)

```
新しくUECTFのロゴを作ったよ。え？元々あったロゴと同じじゃないかって？君はまだまだ甘いなぁ。

I made a new logo for UECTF. What, do you think it's the same as the original logo? You are still a bit naive.

[UECTF_org.bmp]    [UECTF_new.bmp]
```

2つのビットマップ画像が配布されていたので、とりあえず `xxd` で16進数ダンプの状態にしたうえで、`diff` で差分を見てみました。`UECTF_new.bmp` にはFlagが1文字ずつ、数バイトごとに含まれていることがわかり、これを繋ぎ合わせるとFlagになります。

```
$ cat UECTF_org.bmp | xxd > org.txt
$ cat UECTF_new.bmp | xxd > new.txt
$ diff org.txt new.txt
6366c6366
< 00018dd0: ffff ffff ffff ffff ffff ffff ffff ffff  ................
---
> 00018dd0: ffff ffff 55ff ffff ffff ffff ffff ffff  ....U...........
6427c6427
< 000191a0: ffff ffff ffff ffff ffff ffff ffff ffff  ................
---
> 000191a0: ff45 ffff ffff ffff ffff ffff ffff ffff  .E..............
6490c6490
< 00019590: ffff ffff ffff ffff ffff ffff ffff ffff  ................
---
> 00019590: ffff ffff ffff ffff ffff ffff ffff 43ff  ..............C.
6547c6547
< 00019920: ffff ffff ffff ffff ffff ffff ffff ffff  ................
---
> 00019920: ffff ffff ffff ffff ffff ffff ffff ff54  ...............T
6588c6588
< 00019bb0: ffff ffff ffff ffff ffff ffff ffff ffff  ................
---
> 00019bb0: ffff ffff ffff ffff 46ff ffff ffff ffff  ........F.......
6628c6628
< 00019e30: ffff ffff ffff ffff ffff ffff ffff ffff  ................
---
> 00019e30: ffff ffff ff7b ffff ffff ffff ffff ffff  .....{..........
```

`UECTF{compare_two_files_byte_by_byte}`

## discord1 (30 solves)

```
数日前、CTFの作問をやっている友達が送ってきたフラグの書かれた画像がいつの間にか消されていた。あれがあればこの問題にも正解できるはず… 調べたらDiscordのデータはこのフォルダに色々保存されているらしい。何とかして消された画像を見つけられないだろうか…

A few days ago, a friend of mine who is doing a CTF composition question sent me an image with the flag written on it, which was deleted. If I had that one, I should be able to answer this question correctly... I checked and it seems that Discord data is stored in this folder. I wonder if there is any way to find the deleted image...

[discord1.zip]
```

問題文から画像形式のFlagであること、`Cache` というフォルダが怪しいことを踏まえて、以下のように `strings` および `grep` コマンドで画像ファイルのURLを探してみました。

```
$ strings Cache/* | grep media.discordapp.net/attach
...
Fehttps://media.discordapp.net/attachments/1039034703644205099/1043039290101350401/flag.png?width=400&height=297
...
```

Flagらしきファイルは他にもありましたが、上記のURLにアクセスすることでFlagが得られました。

`UECTF{D1SC0RD_1S_V3RY_US3FUL!!}`

## discord2 (21 solves)

```
前に思いついたフラグ送信しようとして止めたんだけど、やっぱりあれが良かったなぁ… でもちゃんと思い出せないなぁ。このフォルダにはキャッシュとかも残ってるし、どこかに編集履歴みたいなの残ってないかなぁ…

I tried to send to a friend the flag I thought of before and stopped, but I still liked that one... But I can't remember it properly. I'm sure there's a cache or something in this folder, and I'm wondering if there's some kind of edit history somewhere...

[discord2.zip]
```

`Local Storage/leveldb` 内のファイルにFlagが含まれていました。総当たりで全てのディレクトリに `strings <directory_name>/* | grep UECTF` していたのですが、Discordの編集中の内容をここから確認できることは初耳でした。勉強になります。

```
$ strings Local\ Storage/leveldb/* | grep UECTF
```

`UECTF{Y0U_C4N_S33_Y0UR_DR4FT}`

# REV

## A file (81 solves)

```
誰かがファイルの拡張子を消してしまった。どのような中身のファイルなのか？

Someone erased a file extension. What contents is the file?

[chall]
```

`file` コマンドで配布ファイルを調べると、`xz` アーカイブ形式であることがわかったので、ファイルの名前を変更した上で解凍しました。解凍後のELFファイルにFlagの文字列が含まれていました。

```
$ mv ./chall ./chall.xz
$ unxz ./chall.xz
$ strings ./chall | grep UECTF
UECTF{Linux_c0mm4nDs_ar3_50_h3LPFU1!}
```

`UECTF{Linux_c0mm4nDs_ar3_50_h3LPFU1!}`

## revPython (20 solves)

```
What does this pyc file do?

これは?

[a.cpython-39.pyc]    [flag.jpg]
```

pycdcなどのツールを使用することで、（不完全ですが）配布されたPythonのバイトコードファイルをデコンパイルすることができます。

デコンパイル後のコードを読むと、ファイルを `UECTF{` という鍵でXORエンコードしているような処理が読み取れるので、以下のようなデコードスクリプトを作成してみました。

実行後に生成される `flag_out.jpg` からFlagが得られます。

```python
FLAG_INPUT_FILE = 'flag.jpg'
FLAG_OUTPUT_FILE = 'flag_out.jpg'

prefix = b'UECTF{'
with open(FLAG_INPUT_FILE, 'rb') as flag_file:
    flag_dat = bytearray(flag_file.read())

for i in range(len(flag_dat)):
    flag_dat[i] ^= prefix[i % len(prefix)]

with open(FLAG_OUTPUT_FILE, 'wb') as decoded_file:
    decoded_file.write(flag_dat)
```

`UECTF{oh..did1s0meh0wscr3wup??}`

## captain-hook (21 solves)

```
haha, good luck solving this

運も実力のうち！

[captainhook]
```

IDAで `captainhook` を静的解析すると、XORデコードしていそうな処理が見つかったので、以下のようなデコードスクリプトを書いて解きました。

```python
encoded = bytes.fromhex('58960A710308090860BE247A2D1C163A69BA2D7A3C1C143A7EBC2553202C150D64A0765845')
key = 0x656173452549D30D
flag = ''

for i in range(0x25):
    a = ((i & 7) << 3) & 0xff
    flag += chr((encoded[i] ^ (key >> a)) & 0xff)

print(flag)
```

`UECTF{hmmmm_how_did_you_solve_this?}`

## dotnet (11 solves)

```
簡単にデコンパイルできるフレームワークを使って書いたので、難読化を施しました。 なので、難読化が正しく行われていれば秘密情報にはアクセスできないはずです・・・ （アプリケーションはLinux-x64で動作させることを想定しています）

I obfuscated this because I made this using an easily decompilable framework. So, if the obfuscation is done correctly, the secret information should not be accessible... (The application is intended to run on Linux-x64)

[chall_x86_64_linux]
```

まず `binwalk` コマンドで `chall_x86_64` に含まれているDLLファイルを全て抽出しました。

```
$ binwalk --dd="microsoft executable" chall_x86_64_linux --rm
```

たくさんのDLLファイルが抽出できますが、"Original Filename" が "UECTF2022_dotnet.dll" であるファイルが解析対象であると推測することができます。

問題文から、難読化が施されていると予想できたので、[de4dot](https://github.com/de4dot/de4dot) というツールを使用して難読化を解除しました。

```
> .\de4dot.exe .\A48B70

de4dot v3.1.41592.3405

Detected Unknown Obfuscator (C:\REDACTED\A48B70)
Cleaning C:\REDACTED\A48B70
Renaming all obfuscated symbols
Saving C:\REDACTED\A48B70-cleaned
```

dnSpy等のツールに生成されたDLLファイルを読み込んで解析すると、XORエンコードらしき処理が読み取れるので、デコードスクリプトを書いて実行したところ、Flagが得られました。

```python
encoded = [255, 238, 235, 253, 232, 212, 237, 221, 210, 207, 201, 194, 199, 211, 205, 202, 212, 200, 149, 218, 204, 218, 221, 201, 215, 215, 157, 198, 223, 195, 220, 152, 206, 228, 252, 231, 235, 251, 161, 227, 231, 230, 228, 172, 242, 232, 169, 231, 255, 182, 254, 236, 242, 243, 229, 176, 226, 225, 255, 229, 243, 244, 224, 240, 142, 202, 149]
flag = ''

for i in range(len(encoded)):
	flag += chr(encoded[i] ^ i ^ 170)

print(flag)
```

`UECTF{Applications-created-with-Dotnet-need-to-be-fully-protected!}`

## discrete (16 solves)

```
Jumping around in memory

記憶の中でジャンプする

[chall]
```

angrでシンボリック実行を適用することでFlagを得ることができました。

```python
import angr

EXEC_NAME = './chall'
FLAG_ADDR = 0x402144 # puts("Correct!");

p = angr.Project(EXEC_NAME, load_options={"auto_load_libs": False})
state = p.factory.entry_state()
simgr = p.factory.simulation_manager(state)
simgr.explore(find=FLAG_ADDR)

try:
    flag = simgr.found[0].posix.dumps(0)
    print(flag[:flag.find(b'\x00')].decode())
except IndexError:
    print("Something went wrong :(")
```

`UECTF{dynamic_static_strings_2022}`

# WEB

## webapi (42 solves)

```
サーバーからフラグを取ってきて表示する web ページを作ったけど、上手く動かないのはなんでだろう？

I created a web page that fetches flags from the server and displays them, but why doesn't it work?

http://uectf.uec.tokyo:4447
```

`FLAG_URL` という定数に代入されているURLを開くことでFlagが得られました。

```
  </div>
</body>

<script>
  const FLAG_URL = 'https://i5omltk3rg2vbwbymc73hnpey40eowfq.lambda-url.ap-northeast-1.on.aws/';
  fetch(FLAG_URL)
    .then(data => {
      document.getElementsByClassName('flag-data')[0].innerText = data;
```

`UECTF{cors_is_browser_feature}`

## request-validation (21 solves)

```
GET リクエストでオブジェクトを送ることはできますか？ ※ まずは、自分の環境でフラグ取得を確認してください。

Can you request a object?

First, please check the flag acquisition in your environment.
http://uectf.uec.tokyo:4446

[request-validation.tar.gz]
```

GETリクエストのパラメータ `q` の型が `object` の場合のみ、Flagを返すようなWebアプリケーションが題材となった問題でした。

```javascript
if (req.query.q && typeof req.query.q === 'object') {
  res.send(FLAG)
} else {
  res.send('invalid request')
}
```

全く事前知識がなかったので、とりあえず `javascript object` と検索すると、以下のページを見つけることができました。

[https://pisuke-code.com/javascript-get-exact-data-type/:embed:cite]

なんと、JavaScriptの `typeof` では配列等を渡した場合、 `object` としか返されないらしいです。これを利用すれば良いと直感的に理解したので、以下のようなスクリプトを書いて実行してみたところ、Flagが得られました。

```python
import requests

TARGET_URL = 'http://uectf.uec.tokyo:4446'
PAYLOAD = {'q': [1, 2, 3, 4, 5]}

req = requests.get(TARGET_URL, PAYLOAD)
print(req.text)
```

`UECTF{javascript_is_difficult_dee36611556508c702805b45289d0f65}`

# CRYPTO

## RSA (57 solves)

```
RSA暗号でフラグを暗号化してみました！解読してみてください。
I encrypted the flag with the RSA cipher! Please try to decode it.

[output.txt]    [rsa_source.py]
```

`output.txt` に含まれている `p` , `q` , `e` , `cipher text` を [RsaCtfTool](https://github.com/RsaCtfTool/RsaCtfTool) に渡して実行すると、Flagが得られました。

```
$ python3 RsaCtfTool.py --uncipher 40407051770242960331089168574985439308267920244282326945397 -p 1023912815644413192823405424909 -q 996359224633488278278270361951 -e 65537
private argument is not set, the private key will not be displayed, even if recovered.

Results for /tmp/tmpyznhht1g:

Unciphered data :
HEX : 0x55454354467b5253412d69532d566552792d35314d7031657d
INT (big endian) : 535251971441201547690579709091650584058216076608475543725437
INT (little endian) : 787118965001959771733939931024213066609133944657257179596117
utf-8 : UECTF{RSA-iS-VeRy-51Mp1e}
STR : b'UECTF{RSA-iS-VeRy-51Mp1e}'
HEX : 0x55454354467b5253412d69532d566552792d35314d7031657d
INT (big endian) : 535251971441201547690579709091650584058216076608475543725437
INT (little endian) : 787118965001959771733939931024213066609133944657257179596117
utf-8 : UECTF{RSA-iS-VeRy-51Mp1e}
STR : b'UECTF{RSA-iS-VeRy-51Mp1e}'
```

`UECTF{RSA-iS-VeRy-51Mp1e}`

# さいごに

pwnが全然解けなかったし、頭の回転も遅いので、もっと勉強しないといけないと思いました。頑張ります。 :innocent:
