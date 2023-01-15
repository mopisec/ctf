# Tamil CTF 2021 Writeup

This is the writeup article for reversing challenges except "Fool Me" in Tamil CTF 2021. All challenges have really awesome quality, and I have learned lots of stuff from them. Thank you to everyone who have helped organizing this wonderful event. 🙏

## Digital play

Challenge that requires player to analyze the file with data of digital logic circuit. By searching with interesting strings inside the `encrypt.dig` (e.g. `visualElement`), tool named `Digital` can be found on GitHub ([Link](https://github.com/hneemann/Digital)). Opening the file using this tool will provide a visualized information like below.

<figure class="figure-image figure-image-fotolife" title="Screenshot from Digital (opening encrypt.dig)">[f:id:m0pisec:20210927073245p:plain]<figcaption>Screenshot from Digital (opening encrypt.dig)</figcaption></figure>

By writing some sort of script / program that decodes the data inside `output.txt`, flag can be obtained.

[https://hackmd.io/@mopi/H1MPww07F

## Obscure

Byte-Compiled Python code file is distributed, so it can be reversed into Python script using tool like [uncompyle2](https://github.com/wibiti/uncompyle2). By obtaining the encoded flag inside the script, and reverse the flow that is generating the encoded data, flag can be obtained.

[https://hackmd.io/@mopi/ryutqERQY

## eezy

Binary patching challenge. Well, I have solved it with static analysis because I am not that familiar with debugger like `gdb`, `radare2`. Algorithm of distributed flag-checker program is not that hard, so it can be reversed easily.

https://hackmd.io/@mopi/r1llkzyEK

## Gold Digger

Reverse the flow of program (using tool like Ghidra, IDA) to obtain the flag. I have used Ghidra to solve and here is [Ghidra Zip File](https://drive.google.com/file/d/1zUpUVYuGhSTFweneFBhs_2H2YIwOz3-S/view?usp=sharing) with some modification (e.g. renaming/retyping varibale) done by me, so it is much more human-friendly to read compared to initial state.

https://hackmd.io/@mopi/BkanMwR7Y

## Guesser

Distributed file is Windows executable written with .NET Framework, so it can be decompiled using tool like dnSpy, ILSpy. Code where validates username and password is reverse-able with simple logic, so I have wrote two line script that gets the username, password.  

https://hackmd.io/@mopi/HJSRJd1EY

## HeyImAB

Distributed file is Android Backup (AB) file. It can be extracted easily, and by grepping the files with `TamilCTF{`, flag can be obtained.

https://hackmd.io/@mopi/ryT8zu14F

## Unknown

Distributed file is written in Python (packed to executable using PyInstaller), so it can be reversed to format of script using `pyinstxtractor.py` and `pycdc`. Reversed script is bit obfuscated using eval function, so I have cleaned up the script and reversed the logic to obtain the flag.

https://hackmd.io/@mopi/SkyRJtJ4t

## Hybrid Reptile

As same as `Unknown` challenge, distributed file is written in Python. It can be reversed to format of script using `pyinstxtractor.py` and `pycdc`. There was unused variable inside the script that seems to be Base64 encoded data, so I have decoded it and knew that its ELF file. I have decompiled it with Ghidra and obtained the key that can decrypt the flag.

https://hackmd.io/@mopi/H1QpDYJNt
