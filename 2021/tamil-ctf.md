# Tamil CTF 2021 Writeup

This is the writeup article for reversing challenges except "Fool Me" in Tamil CTF 2021. All challenges have really awesome quality, and I have learned lots of stuff from them. Thank you to everyone who have helped organizing this wonderful event. 🙏

## Digital play

Challenge that requires player to analyze the file with data of digital logic circuit. By searching with interesting strings inside the `encrypt.dig` (e.g. `visualElement`), tool named `Digital` can be found on GitHub ([Link](https://github.com/hneemann/Digital)). Opening the file using this tool will provide a visualized information like below.

By writing some sort of script / program that decodes the data inside `output.txt`, flag can be obtained.

```python
from Crypto.Util.number import long_to_bytes

enc = open('enc.txt', 'r').read().split(' ')
flag = ''

for i, e in enumerate(enc):
    if i % 2 == 0:
        t = int(e.replace('1', '2').replace('0', '1').replace('2', '0'), 2)
    else:
        t = int(e, 2)
    flag += long_to_bytes(t ^ 0x4d415253).decode()

print(flag)
# TamilCTF{D1g1T_CiRCu1T5_aRe_AwE50Me}
```

## Obscure

Byte-Compiled Python code file is distributed, so it can be reversed into Python script using tool like [uncompyle2](https://github.com/wibiti/uncompyle2). By obtaining the encoded flag inside the script, and reverse the flow that is generating the encoded data, flag can be obtained.

```
$ file reverseme
reverseme: python 2.7 byte-compiled
```

```
$ uncompyle2 reverseme
# 2021.09.27 03:13:13 JST
#Embedded file name: reverseme.py
import numpy as np
flag = 'TamilCTF{this_one_is_a_liability_dont_fall_for_it}'
np.random.seed(369)
data = np.array([ ord(c) for c in flag ])
extra = np.random.randint(1, 5, len(flag))
product = np.multiply(data, extra)
temp1 = [ x for x in data ]
temp2 = [ ord(x) for x in 'dondaVSclb' * 5 ]
c = [ temp1[i] ^ temp2[i] for i in range(len(temp1)) ]
flagdata = ''.join((hex(x)[2:].zfill(2) for x in c))
real_flag = '300e030d0d1507251700361a3a0127662120093d551c311029330c53022e1d3028541315363c5e3d063d0b250a090c52021f'
+++ okay decompyling reverseme
# decompiled 1 files: 1 okay, 0 failed, 0 verify failed
# 2021.09.27 03:13:13 JST
```

```python
real_flag = bytearray(b'\x30\x0e\x03\x0d\x0d\x15\x07\x25\x17\x00\x36\x1a\x3a\x01\x27\x66\x21\x20\x09\x3d\x55\x1c\x31\x10\x29\x33\x0c\x53\x02\x2e\x1d\x30\x28\x54\x13\x15\x36\x3c\x5e\x3d\x06\x3d\x0b\x25\x0a\x09\x0c\x52\x02\x1f')
key = [ ord(x) for x in 'dondaVSclb' * 5 ]
data = [ real_flag[i] ^ key[i] for i in range(len(real_flag)) ]
for i in data: print(chr(i), end='')
# TamilCTF{bRuTeF0rCe_1s_tHe_0nLy_F0rCe_2_bReAk__1n}
```

## eezy

Binary patching challenge. Well, I have solved it with static analysis because I am not that familiar with debugger like `gdb`, `radare2`. Algorithm of distributed flag-checker program is not that hard, so it can be reversed easily.

```
# On Ghidra Python Console

>>> addr = toAddr(0x10123b)
>>> enc = []
>>> inst = getInstructionAt(addr)
>>> for _ in range(0x1e):
... 	enc.append(int(inst.getDefaultOperandRepresentation(1), 16))
... 	inst = inst.getNext()
... 
>>> enc
[72, 115, 118, 4, 116, 106, 97, 6, 5, 89, 98, 115, 118, 92, 84, 20, 65, 89, 88, 65, 5, 106, 88, 118, 6, 78, 97, 89, 88, 97]
```

```python
flag = ['A'] * 0x1e
enc = [chr(e ^ 0x35) for e in [72, 115, 118, 4, 116, 106, 97, 6, 5, 89, 98, 115, 118, 92, 84, 20, 65, 89, 88, 65, 5, 106, 88, 118, 6, 78, 97, 89, 88, 97]]
enc.reverse()

for i, c in enumerate(enc[:15]):
    flag[i*2] = c

for i, c in enumerate(enc[15:]):
    flag[i*2+1] = c

flag = ''.join(flag)
print(flag)
# TamilCTF{W3lC0m3_T0_tAm1lCtF!}
```

## Gold Digger

Reverse the flow of program (using tool like Ghidra, IDA) to obtain the flag. I have used Ghidra to solve and here is [Ghidra Zip File](https://drive.google.com/file/d/1zUpUVYuGhSTFweneFBhs_2H2YIwOz3-S/view?usp=sharing) with some modification (e.g. renaming/retyping varibale) done by me, so it is much more human-friendly to read compared to initial state.

```python
f = bytearray(b'\x16\x0a\x1f\x10\x18\x1b\x09\x0b\x1e\x03\x08\x14\x1d\x00\x07\x11\x17\x13\x15\x1c\x12\x02\x06\x04\x19\x05\x1a\x01\x00')
enc_flag = bytearray(b'\x70\x53\x6a\x71\x34\x7d\x81\x50\x63\x48\x68\x58\x59\x63\x49\x6d\x47\x65\x4a\x73\x58\x72\x4c\x63\x79\x4b\x7f\x78')

def foo():
    res = [0] * 0x1c
    for i in range(0x1c):
        res[i] = f[i] ^ 0x12
    return res

buf = []
flag = [''] * 0x1c
a = foo()
for i in range(0x1c):
    flag[a[i]] += chr(enc_flag[i] - 4)

print(''.join(flag))
```

## Guesser

Distributed file is Windows executable written with .NET Framework, so it can be decompiled using tool like dnSpy, ILSpy. Code where validates username and password is reverse-able with simple logic, so I have wrote two line script that gets the username, password.  

```
print('Username: ' + chr(114 ^ 0x39) + chr(127 - (114 ^ 0x39)) + chr(182 - (114 ^ 0x39)) + chr((182 - (114 ^ 0x39)) - 10) + 'R' + chr(ord('R') - 34) + chr(116))
# Username: K4kaR0t
print('Password: ' + chr(71) + chr(119 ^ 71) + chr(124 ^ 0x17) + chr(117) + chr(192 - (124 ^ 0x17)))
# Password: G0kuU
```

From the overall information, flag is `TamilCTF{K4kaR0t:G0kuU}`.

## HeyImAB

Distributed file is Android Backup (AB) file. It can be extracted easily, and by grepping the files with `TamilCTF{`, flag can be obtained.

```
class Homepage extends StatelessWidget {
  const Homepage({Key? key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('TamilCTF{1_l0v3_y0u_AB}'),
      ),
    );
  }
}
```

## Unknown

Distributed file is written in Python (packed to executable using PyInstaller), so it can be reversed to format of script using `pyinstxtractor.py` and `pycdc`. Reversed script is bit obfuscated using eval function, so I have cleaned up the script and reversed the logic to obtain the flag.

```python
def idfc():
    print("Wrong flag")
    sys.exit(1)

def udkncdi(cringe):
    print("Wait for few second")
    time.sleep(0) if cringe[3] == '_' else idfc()
    time.sleep(0) if cringe[11] == '_' else idfc()
    time.sleep(0) if cringe[21] == '_' else idfc()
    time.sleep(0) if cringe[26] == '_' else idfc()
    time.sleep(0) if cringe[-1] == '?' else idfc()
    time.sleep(0) if base64.b64encode(cringe[4:11].encode('utf-8')).decode() == "cjN2M3JTZQ==" else idfc()
    time.sleep(1) if base64.b16encode(cringe[27:].encode('utf-8')).decode()  == '74416C336E54346E543F' else idfc()
    time.sleep(1) if cringe[:3] + cringe[22:26] == 'aRel3s5' else idfc()
    enigd = ''.join([ chr(ord(i)^2) for i in cringe[12:21] ])
    time.sleep(1) if enigd == 'gLeKlgGp7' else idfc()
    print("Correct password!!!!\n")

print("\n----- Welcome to TamilCTF ------\n")
dfjic = input("Enter the password : ")
udkncdi(dfjic) if len(dfjic) == 37 else print("\nWrong flag\n")
```

```
$ ./challenge

----- Welcome to TamilCTF ------

Enter the password : aRe_r3v3rSe_eNgIneEr5_l3s5_tAl3nT4nT?
Wait for few second
Correct password!!!!
```

`TamilCTF{aRe_r3v3rSe_eNgIneEr5_l3s5_tAl3nT4nT?}`

## Hybrid Reptile

As same as `Unknown` challenge, distributed file is written in Python. It can be reversed to format of script using `pyinstxtractor.py` and `pycdc`. There was unused variable inside the script that seems to be Base64 encoded data, so I have decoded it and knew that its ELF file. I have decompiled it with Ghidra and obtained the key that can decrypt the flag.

```
$ ./hybrid_reptile
[1] encrypt
[2] decrypt
[3] check the key for flag decryption
>>> 2
Enter the encrypted message: gAAAAABhJSCLStIQtSxtB9oh2CVvq1sKTYCgcry-8MX5cXKCQv4SSf0z-7KS55HHRU2qohHmSpYfqrn6KJ14D_LQqqtNH7eYVJc7tnY0MheqcZUSMjku71AubNk9ijbCH6sOnxVWE7_YxVG94Zo2W5htXVLlky6CIA==
Enter the key: Qe1gFEtUXwLjuhoktF4e8ENICPJJn6hCi16C1uBWA7s=
b"Baaabu!! Snake Baaaaaaaabu!!! 'TamilCTF{imaaaa_Snakeeeee!#$%^}'"
```
