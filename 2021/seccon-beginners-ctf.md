SECCON Beginners CTF 2021にチームr0bu5tの一員として参加させていただきました。今回はReversing全完を目標にして取り組んで、開始から三時間三十分くらいで目標達成しました。  

https://twitter.com/mopisec/status/1396023706439147524

ありがたいことに一緒に参加してくれたprprhyt氏、kawasin73氏がWeb, Crypto問などに取り組んでくれていたので、残りの時間は色々なカテゴリーの問題を嗜む程度にやってました。本当にありがとうございました（and お疲れさまでした）。 :bow:  
また運営の皆様もこのような素晴らしいCTFを開催していただきありがとうございました。とても楽しい二日間でした。  
本記事には自分が解いた問題に関するWriteupのようなものが書かれているので、参考程度に見ていっていただけたら嬉しいです。

## only_read (rev)

Flag: `ctf4b{c0n5t4nt_f01d1ng}`

## children (rev)

Flag: `ctf4b{p0werfu1_tr4sing_t0015_15_usefu1}`

## please_not_trace_me (rev)

Flag: `ctf4b{d1d_y0u_d3crypt_rc4?}`

## be_angry (rev)

```python
>>> p = angr.Project("./chall.3", load_options = {'main_opts':{'base_addr': 0x0000555555555000}})
>>> e = p.factory.entry_state()
>>> simgr = p.factory.simulation_manager(e)
>>> s = simgr.explore(find=0x555555557532)
WARNING | 2021-05-22 16:02:02,887 | angr.storage.memory_mixins.default_filler_mixin | Filling register id with 8 unconstrained bytes referenced from 0x5555555578f9 (_1_main_flag_func_4+0x1f in chall.3 (0x28f9))
WARNING | 2021-05-22 16:02:02,888 | angr.storage.memory_mixins.default_filler_mixin | Filling register ac with 8 unconstrained bytes referenced from 0x5555555578f9 (_1_main_flag_func_4+0x1f in chall.3 (0x28f9))
>>> s
<SimulationManager with 2 active, 22 deadended, 1 found>
>>> s.found[0].posix.dumps(0)
b'ctf4b{3nc0d3_4r1thm3t1c}'
```
Flag: `ctf4b{3nc0d3_4r1thm3t1c}`

## firmware (rev)

```python
>>> oridata = [0x30,0x27,0x35,0x67,0x31,0x28,0x3a,0x63,0x27,0x0c,0x37,0x36,0x25,0x62,0x30,0x36,0x0c,0x35,0x3a,0x21,0x3e,0x24,0x67,0x21,0x36,0x0c,0x32,0x3d,0x32,0x62,0x2a,0x20,0x3a,0x60,0x0c,0x21,0x36,0x25,0x60,0x32,0x62,0x20,0x0c,0x32,0x0c,0x3f,0x63,0x27,0x0c,0x3c,0x35,0x0c,0x66,0x36,0x30,0x21,0x36,0x64,0x20,0x2e,0x59]
>>> len(oridata)
61
>>> flag = ""
>>> i = 0
>>> while i < 61:
...     flag = flag + chr(oridata[i] ^ 0x53)
...     i = i + 1
...
>>> flag
'ctf4b{i0t_dev1ce_firmw4re_ana1ysi3_rev3a1s_a_l0t_of_5ecre7s}\n'
```

Flag: `ctf4b{i0t_dev1ce_firmw4re_ana1ysi3_rev3a1s_a_l0t_of_5ecre7s}`

## simple_RSA (crypto)

```
$ python3 RsaCtfTool.py -n 17686671842400393574730512034200128521336919569735972791676605056286778473230718426958508878942631584704817342304959293060507614074800553670579033399679041334863156902030934895197677543142202110781629494451453351396962137377411477899492555830982701449692561594175162623580987453151328408850116454058162370273736356068319648567105512452893736866939200297071602994288258295231751117991408160569998347640357251625243671483903597718500241970108698224998200840245865354411520826506950733058870602392209113565367230443261205476636664049066621093558272244061778795051583920491406620090704660526753969180791952189324046618283 -e 3 --uncipher 213791751530017111508691084168363024686878057337971319880256924185393737150704342725042841488547315925971960389230453332319371876092968032513149023976287158698990251640298360876589330810813199260879441426084508864252450551111064068694725939412142626401778628362399359107132506177231354040057205570428678822068599327926328920350319336256613
・
・略
・
Results for /tmp/tmpk9jnxblb:

Unciphered data :
HEX : 0x63746634627b302c312c31302c31312e2e2e497427735f736f5f616e6e6f79696e672e5f5f5f49276d5f646f6e657d
INT (big endian) : 59794831782110540824905303318706147427325410868387651570741854903973308152253277340232651694780110842176190965117
INT (little endian) : 75391578580851103181433066932790227485569227688076229032569777859612996898560666851200930122567113349338681996387
STR : b"ctf4b{0,1,10,11...It's_so_annoying.___I'm_done}"
```

## git-leak (misc)

```
> git cat-file -p 4cbb035d2ff072127b4e22919485127d2273e88e
ctf4b{0verwr1te_1s_n0t_c0mplete_1n_G1t}
```

Flag: `ctf4b{0verwr1te_1s_n0t_c0mplete_1n_G1t}`

## check-url (web)

https://check-url.quals.beginners.seccon.jp/?url=http://0x7f000001

Flag: `ctf4b{5555rf_15_53rv3r_51d3_5up3r_54n171z3d_r3qu357_f0r63ry}`

## depixelization (misc)

Flag: `ctf4b{1f_y0u_p1x_y0u_c4n_d3p1x}`

## 全体を通した感想など

prprhyt氏のWriteupもこちらで公開されています。こちらも是非お読みください。

https://atofaer.hatenablog.jp/entry/2021/05/23/152537
