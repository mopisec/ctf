## はじめに

ソロで参加して700チーム((Welcome問を解いたチーム))中145位でした。他参加者のWriteupを読むのが楽しくて、公開が遅れてしまいましたが、今回解けたreversingの2問のWriteupを共有できればと思います。  
運営の皆さま、素晴らしいCTFを開催いただき、ありがとうございます！

## babycmp (176 solves)

Crackme系の問題でよく見る、渡したコマンドライン引数を検証してFlagかどうか判定するプログラムが配布されていました。  
angrでシンボリック実行を適用することでFlagを得ることができました

```python
import angr
import claripy

EXEC_NAME = './chall.baby'
FLAG_ADDR = 0x4012CC # puts("Correct!");

p = angr.Project(EXEC_NAME, load_options={"auto_load_libs": False})

argv1 = claripy.BVS("argv1", 100*8)
state = p.factory.entry_state(args=[EXEC_NAME, argv1])

simgr = p.factory.simulation_manager(state)
simgr.explore(find=FLAG_ADDR)

try:
    found = simgr.found[0]
    solution = found.solver.eval(argv1, cast_to=bytes)
    flag = solution[:solution.find(b"\x00")].decode()
    print(flag)
except IndexError:
    print("Something went wrong :(")

```

`SECCON{y0u_f0und_7h3_baby_flag_YaY}`

## eguite (86 solves)

Rustで書かれたGUIアプリケーションが配布されていました。IDAで静的解析したところ、 `eguite::Crackme::onclick::ha26112793d42c9d8` という興味深い関数を見つけることができます。処理を読んでみると、まず入力が `SECCON{` で始まること、そして入力の 0x2b 文字目が `}` であるか検証していることがわかります。

ここでgdbをアタッチした状態でアプリケーションを実行し、先述した関数の適当な場所にブレークポイントを設置したうえで、適当な入力（例: `SECCON{0123456789abcdefghijklmnopqrstuvwxy}` を与えてみます。するとブレークポイントを設置した場所で実行が停止するので、そこからステップ実行していき、正しいと思われる（戻り値が0にならない）処理が実行されるよう入力を調整していくことで、以下の情報を得ることができます

1. Flagは `SECCON{AAAAAAAAAAAA-BBBBBB-CCCCCC-DDDDDDDD}` のような形式
2. `AAA...` や `BBB...` は数値 (16進数で表記)
3. Flagが正しいか判定するために、以下のチェックが行われる
    - `AAA...` + `BBB...` == `0x8B228BF35F6A`
    - `CCC...` + `BBB...` == `0xE78241`
    - `DDD...` + `CCC...` == `0xFA4C1A9F`
    - `AAA...` + `DDD...` == `0x8B238557F7C8`
    - `BBB...` ^ `CCC...` ^ `DDD...` == `0xF9686F4D`

以上の情報をもとに、各値を求めることでFlagが得られます。

```python
from z3 import *

s = Solver()

p1 = BitVec("p1", 8 * 6)
p2 = BitVec("p2", 8 * 6)
p3 = BitVec("p3", 8 * 6)
p4 = BitVec("p4", 8 * 6)

s.add(p1 + p2 == 0x8B228BF35F6A)
s.add(p3 + p2 == 0xE78241)
s.add(p4 + p3 == 0xFA4C1A9F)
s.add(p1 + p4 == 0x8B238557F7C8)
s.add(p2 ^ p3 ^ p4 == 0xF9686F4D)

if s.check() == z3.sat:
    flag1 = hex(s.model()[p1].as_long())[2:].zfill(12)
    flag2 = hex(s.model()[p2].as_long())[2:].zfill(6)
    flag3 = hex(s.model()[p3].as_long())[2:].zfill(6)
    flag4 = hex(s.model()[p4].as_long())[2:].zfill(8)
    print('[+] SECCON{' + flag1 + '-' + flag2 + '-' + flag3 + '-' + flag4 + '}')
else:
    print('[-] Something went wrong :(')
```

`SECCON{8b228b98e458-5a7b12-8d072f-f9bf1370}`

## さいごに

残念ながら解けていないのですが((秘密鍵をメモリダンプから復元する方法が思いつかず、10時間以上バイナリエディタと格闘していました))、今回特に "DoroboH" という問題から色々な学びを得ることができました。検証してわかったことも色々あるので、何らかの形でアウトプットすることができればと思っています。
