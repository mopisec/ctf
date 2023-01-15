# TsukuCTF 2022 Writeup

チーム `mopisec` としてソロで参加して7258pts (23位)でした。解けなかった問題も含め、試したこと・解法・反省点などをまとめています。

## Tsukushi

### Welcome (378 solves)

>Please join our Discord!

Discordの `#🚩-welcome-to-tsukuctf ` のトピック欄にFlagが書かれていました。

`TsukuCTF22{Welcome_to_TsukuCTF_2022!!!!}`

## OSINT

### Attack of Tsukushi (215 solves)

>つくしくんはある観光地を調査した際に訪れた駅で写真を撮影した。果たしてこの写真が撮られた駅はどこだろうか？

Google Lensで画像を検索することで、写真がJR日田駅で撮影されたものだとわかります。

`TsukuCTF22{8770013}`

### Money (199 solves)

>どこ？

Google Lensで画像を検索することで、写真が金閣寺で撮影されたものだとわかります。

`TsukuCTF22{6038361}`

### FlyMeToTheTsukushi (169 solves)

>つくし君は、はるばる飛行機で愛するパートナーのもとへやってきました。  
ここはどこの空港かわかりますか？

A350という機体名が見えるので、その機体を運用している空港を総当たりすることで解けます。

`TsukuCTF22{福岡}`

### inuyama082 (142 solves)

>つくし君は愛知県犬山市にデートに来た時の思い出の写真を見返しています。 おいしそうな写真を見つけ、おやつが食べたくなりました。 写真のおやつの名前を教えてください。

Google Lensで画像ファイルを検索することで、「犬山カフェよあけや」というお店の「和チーズケーキ＆抹茶」というスイーツだとわかります。ただしそれだけでは不十分らしく、カフェの公式サイトに書いてある `ver.煎茶パウダー` を付け足すことで、正答となりました。

すごく美味しそうなので、愛知県に行った際には是非立ち寄りたいと思います！

`TsukuCTF22{和チーズケーキ ver.煎茶パウダー}`

### sky (113 solves)

>帰ってくるあなたが最高のプレゼント。つくし君は電車にガタゴト揺られています。次の停車駅で降りるようなのですが、どこかわかりますか？

座席の収納棚の中に「名鉄沿線おでかけマガジンWind」という情報誌が入っています。配布場所は名鉄各駅と空港特急ミュースカイ車内のみらしいので、空港特急ミュースカイの停車駅を総当たりすることで解けます。

`TsukuCTF22{名鉄名古屋}`

### station (110 solves)

>つくし君はとある駅で友達を待っています。さて、つくし君はどこの駅にいるでしょうか？

最初の文字が見切れていますが、`...郷7丁目` , `...郷13丁目` , `...郷18丁目` という駅名らしき文字列が写真に含まれています。

駅名を検索することで、恐らく見切れている文字は `南` であること、そしてその路線が `東西線` であることがわかります。一番上の見切れている駅名 `...前` が答えだと推測し、`南郷7丁目` など他の駅名の位置から `西11丁目` が答えであるとわかりました。

`TsukuCTF22{西11丁目}`

### douro (101 solves)

>旅行中のつくし君は迷子になってしまったようです。うつむいています。送られてきた写真から場所を特定できますか？

道路上のデザインから `よいほモール` という文字列が読み取れるので、Google Map （およびそのストリートビュー）で検索して、撮影地点を特定した。

`TsukuCTF22{34.5770_136.5309}`

### Where (98 solves)

>北海道に住んでいるつくしさんは東京旅行に行った際に高層ビルの窓から写真を撮りました。  
でも撮影した場所を忘れてしまったようです。この写真が撮影された場所について建物名を教えてあげてください。

東日本銀行など、目立つ看板から情報を集め、Google Earthで調べたところ、撮影地点が「渋谷パルコ」だとわかりました。

`TsukuCTF22{1973/06/14}`

### Gorgeous Interior Bus (83 solves)

>観光地に来たつくし君は、豪華なバスを見かけたので、それに乗って観光することにしました。 その時、つくし君のお母さんから「どこにいるの？」と連絡が着ましたが、おっちょこちょいなつくし君は、観光地の名前も、乗っているバスの路線も忘れてしまい、とっさに車内の写真を撮って、「ここ」と返信しました。 つくしくんはどこにいるのでしょうか？ つくしくんが写真を撮ったところに最も近い交差点の名前を特定してください。

正面のモニター内の情報から、バスが「...スパあたみ」へ向かっていることがわかります。また次のバス停が「...水公園」だということもわかります。

以上の情報をもとに、Google Map上で調査することで、最も近い交差点が「東海岸町」であることがわかります。

`TsukuCTF22{東海岸町}`

### Bringer_of_happpiness (75 solves)

>つくしくんは荷物を運び終えて休憩してるときに撮った写真。さて撮影場所はどこだろう？

Google Lensで画像を検索することで写っている黄色い電車が島原鉄道のものだとわかります。島原鉄道の駅を一つずつGoogle Map （およびストリートビュー）で調べると、写真に写っている駅が「島原港駅」であることもわかります。撮影地点の緯度・経度（駅から若干ずれている）を調べて回答することで、正答となりました。

`TsukuCTF22{32.7693_130.3707}`

### Desk (69 solves)

>つくし君の大好きなお姉さんのデスクを見学させてもらったよ。 さて、このデスクはどこにあるのだろうか?

机の上のクリアファイル内の資料から、写真が恐らく沖縄県で撮影されたものだとわかりました。また、席札内のロゴが「南城市」のものであるとわかりました。

南城市に関係する施設（市役所など）のうち、南城市観光協会の郵便番号を試したところ、正答となった。

`TsukuCTF22{9011511}`

### TakaiTakai (53 solves)

>日本の町は美しい。撮影地を答えてください。

>フラグはこの建物の開業日(YYYY/MM/DD)です。たとえば、東京スカイツリーの開業日は2012年5月22日なので、フラグは `TsukuCTF22{2012/05/22}` となります。

文字情報が見当たらず、解くまでにかなり時間を使ってしまった問題でした。写真右奥の建物をGoogle Lensを使って「中目黒アトラスタワー」だと突き止めた後、Google Earthを使って周辺の建物と写真内の建物を見比べて、少しずつ候補となる建物を絞っていき、最終的に撮影地点が「渋谷ソラスタ」だとわかりました。

`TsukuCTF22{2019/03/29}`

### PaperJack (51 solves)

>イケメンのつくしくんは訪れている場所の写真をSNSに投稿したところ、ストーカーに特定されてしまった。ストーカー曰く「好きなゲームと新聞がコラボしたときの広告にこの場所が映っていたのを思い出した」とのことだった。

「ゲーム　新聞　広告　観光名所」のようなキーワードで調べてみると、"Fate/Grand Order" というゲームの5周年記念でそのような企画があったとわかり、撮影場所が和歌山県の道成寺であると突き止めることができた。

`TsukuCTF22{6491331}`

### banana (44 solves)

Google Lensで画像を検索することで、撮影場所がグアムの「デデド朝市会場」のトイレであることがわかった。周辺の緯度経度を何度か提出することで正答となった。

`TsukuCTF22{13.5209_144.8287}`

### Robot (33 solves)

>つくし君がロボット見学に訪れた施設はどこ？

写真に写っているイベント名や建物の名前（中国語）を検索することで、[华南理工大学](https://www.scut.edu.cn/new/)の英語表記である "South China University of Technology" が答えであることがわかった。

`TsukuCTF22{South China University of Technology}`

### Flash (26 solves)

>つくし君からマイコンボードを借りたら、このマイコンを使って実験を行ったホテルと部屋番号がわかってしまった！！ マイコンのフラッシュメモリから読みだしたデータを渡すので、ホテル名と部屋番号を特定してください。

ESP32というマイコンのフラッシュメモリから読みだしたデータが配布されていました。データの解析手法について調べるとesp32_image_parserというツールキットを発見することができました。

[https://github.com/tenable/esp32_image_parser:embed:cite]

このツールを使って、データからNVS（Non Volatile Storage）領域をJSON形式で抽出したところ、Wi-FiのSSIDを確認することができました。

```
$ python3 esp32_image_parser.py dump_nvs Flash.bin -partition nvs -nvs_output_type json
...
{"entry_state": "Written", "entry_ns_index": 2, "entry_ns": "nvs.net80211", "entry_type": "BLOB_DATA", "entry_span": 3, "entry_chunk_index": 128, "entry_key": "sta.ssid", "entry_data_type": "BLOB_DATA", "entry_data_size": 36, "entry_data": "DAAAAGFwYS0zMTYtMjQyOAAAAAAAAAAAAAAAAAAAAAAAAAAA"},
...
$ echo "DAAAAGFwYS0zMTYtMjQyOAAAAAAAAAAAAAAAAAAAAAAAAAAA" | base64 -d

apa-316-2428
```

SSID内の `apa` および問題文にある「ホテル」という情報から、アパホテルのNo. 316、つまり「アパホテル&リゾート〈両国駅タワー〉」であり、残った2428は部屋番号であると推測することができました。

`TsukuCTF22{アパホテル&リゾート〈両国駅タワー〉_2428}`

## Web

### bughunter (86 solves)

>天才ハッカーのつくし君は、どんなサイトの脆弱性でも見つけることができます。 あなたも彼のようにこのサイトの脆弱性を見つけることができますか？ 見つけたら私たちに報告してください。

問題サーバにアクセスすると「超絶安全なサイト」というWebページを確認することができます。

ページ上の情報から、URLの末尾に `?tsukushi=<script>alert(document.domain)</script>` を付け足してアクセスしてみると、想定通りalertを発火させることができました。

どうすればFlagを得られるのか考えていると、公式のDiscordにて運営の方が

>Look at the tags.  
...  
No XSS  
its RFC...

と書き込まれていたので、スコアサーバを再度確認したところ問題に `RFC9116` というタグが付けられていました。そこで `.well-known/security.txt` にアクセスしたところ、Flagを見つけることができました。

`TsukuCTF22{y0u_c4n_c47ch_bu65_4ll_y34r_r0und_1n_7h3_1n73rn37}`

### viewer (8 solves; not solved)

>Writeups for TsukuCTF21 have been published. Check them out if you'd like!

問題サーバにアクセスすると、TsukuCTF21のWriteupを閲覧することができるWebアプリケーションが動作していることがわかります。

ソースコード (`app/app.py`) を読むと、POSTされたURLにPycURLでアクセスして、取得したデータを返すような機能が実装されていることがわかります。

```python
    if request.method == "POST":
        url = url_sanitizer(request.form.get("url"))

        buf = BytesIO()
        try:
            c = pycurl.Curl()
            c.setopt(c.URL, url)
            c.setopt(c.WRITEDATA, buf)
            c.perform()
            c.close()
    
            body = buf.getvalue().decode('utf-8')
        except Exception as e:
            traceback.print_exc()
            abort("error occurs")
        return render_template("index.html", url=url, data=response_sanitizer(body), name=name)
    return render_template("index.html", data=None, name=name)
```

処理の流れを順を追ってみていきます。まずPOSTされたURLは `url_sanitizer` 関数によってサニタイズされています。

```python
# only 'http' and 'https' should have been allowed, right?
# ref: https://everything.curl.dev/cmdline/urls/scheme#supported-schemes
blacklist_of_scheme = ['dict', 'file', 'ftp', 'gopher', 'imap', 'ldap', 'mqtt', 'pop3', 'rtmp', 'rtsp', 'scp', 'smb', 'smtp', 'telnet']

def url_sanitizer(uri: str) -> str:
    if len(uri) == 0 or any([scheme in uri for scheme in blacklist_of_scheme]):
        return "https://fans.sechack365.com"
    return uri
```

`http` , `https` 以外のスキームがURLに含まれていた場合、`https://fans.sechack365.com` が返されてしまうようです。これは `GOPHER` や `FILE` のように、スキームを大文字で表記することで回避できると思いつきました。

次にPycURLでURLにアクセスし、取得したデータを `response_sanitizer` 関数によってサニタイズしています。

```python
# a response is also sanitized just in case because the flag is super sensitive information.
blacklist_in_response = ['TsukuCTF22']

def response_sanitizer(body: str) -> str:
    if any([scheme in body for scheme in blacklist_in_response]):
        return "SANITIZED: a sensitive data is included!"
    return body
```

このため、例え `FILE:///var/www/app.py` のようなURLをPOSTしたとしても、Flagを取得することはできません。((Flagはapp.pyに含まれている))

実はFlagはWebアプリケーションが実行されたタイミングでRedisにも格納されています。

```python
# initialization
redis = redis.Redis(host='redis', port=6379, db=0)
flag = "TsukuCTF22{dummy flag}" # the flag is replaced a real flag in a production environment.
id = str(uuid.uuid4())
redis.set(id, json.dumps({"id": id, "name": flag}))
```

そこでRedisに格納されているFlagをSSRFで読み出そうと思ったのですが、名前の登録を行った時点で、Flagが消滅してしまうことがわかりました。

```
# 初期状態
127.0.0.1:6379> keys *
1) "9c513303-8a9b-4eb1-bddc-b55f877c54c4"
127.0.0.1:6379> get "9c513303-8a9b-4eb1-bddc-b55f877c54c4"
"{\"id\": \"9c513303-8a9b-4eb1-bddc-b55f877c54c4\", \"name\": \"TsukuCTF22{dummy flag}\"}"

# 名前を登録した後
127.0.0.1:6379> get "9c513303-8a9b-4eb1-bddc-b55f877c54c4"
"{\"id\": \"c74a552f-78d5-4ea8-ac2c-21a3abfb6916\", \"name\": \"taro\"}"
```

```python
@app.route("/register", methods=["POST"])
def register_post():
    name = request.form.get("name")
    # 入力した名前でFlagを上書きしている
    redis.set(id, json.dumps({"id": str(uuid.uuid4()), "name": name}))
    redis.expire(id, 100)
```

SSRF脆弱性を利用するためには名前を登録する必要があり、名前を登録するとRedis上からFlagが消滅してしまいます。よってRedisからFlagを取得することはできないと結論付け、その後も色々な解き方を考えて試していたのですが、解くことはできませんでした。

>実際の問題サーバではRedis上にもFlagが残っていたようです。理由については理解できていないので、わかる方がいれば教えていただけると嬉しいです。

**追記 (2022-10-25):**  
[公式Writeup](https://fans.sechack365.com/ctf/TsukuCTF2022/viewer)にて、なぜこのような問題が起こったのか取り上げられていました。詳しく知りたい方は是非読んでみてください。

## Reversing

### GrandpaMemory (17 solves)

>祖父からお誕生プレゼントが入った鍵付きの箱とa.outという名前のファイルをもらった。これを開けるには数字を入力すれば良いらしい。ヒントはこのファイルの計算結果が鍵であること、このファイルは1971年に冷蔵庫ほどもあるミニコンピューターで作成された実行ファイルであると言われた。

配布ファイルに対して `file` コマンドを実行すると、PDP-11という昔のミニコンピュータの実行ファイルであることがわかります。

```
$ file a.out 
a.out: PDP-11 old overlay
```

逆アセンブルできないか調べたところ、[pdp11dasm](https://github.com/caldwell/pdp11dasm)というツールを見つけることができました。

```
;
; pdp11dasm version 0.0.3
; disassembly of a.out
;
000000: 000405              	br	14			; ..
;
000002: 000064              	invalid opcode			; 4.
000004: 000000              	halt				; ..
000006: 000000              	halt				; ..
000010: 000000              	halt				; ..
000012: 000000              	halt				; ..
;
000014: 005001              	clr	r1			; ..
000016: 005002              	clr	r2			; ..
000020: 005201              	inc	r1			; ..
000022: 005202              	inc	r2			; ..
000024: 006301              	asl	r1			; A.
000026: 006301              	asl	r1			; A.
000030: 006301              	asl	r1			; A.
000032: 006301              	asl	r1			; A.
000034: 006302              	asl	r2			; B.
000036: 060102              	add	r1,r2			; B`
000040: 000777              	br	40			; ..
;
000042: 060560 071563       	add	r5,71563(r0)		; pass
000046: 062167 064440       	add	(r1)+,64512		; wd i
000052: 020163 067151       	cmp	r1,67151(r3)		; s in
000056: 051040              	bis	(r0),-(r0)		;  R
000060: 020062 005000       	cmp	r0,5000(r2)		; 2 ..
```

何らかの計算処理と `passwd is in R2` という文字列を確認することができます。
複数のWebページを参考に処理内容を読み解き、最終的にR2レジスタの値が `18` になることがわかりました。

`TsukuCTF22{18}`
