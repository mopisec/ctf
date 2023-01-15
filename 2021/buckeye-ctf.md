# Buckeye CTF 2021 Writeup

I have participated Buckeye CTF 2021, and solved most of Misc challenges (I was slept when "Survey" challenge has been opened. :sob:) & 3 Rev challenges. They were really fun, and I am excited to study about hard Rev challenges that I could not solve after the competition. Thanks to all of the organizers & sponsors for this CTF!!!

[:contents]

## Rev

### BASIC

Program file for `TI83F` is distributed. It can be decoded into human readable code using certain tool like [8xpdump](https://github.com/PhillipAnd/8xpdump), and flag can be obtained from it.

https://hackmd.io/@mopi/SJTmjzW8K

### Buttons

By decompiling the given jar file into java code, we can figure out that its something like maze game. Grid (with information that where player should not step in) is hardcoded, so player can reach out flag using it.

https://hackmd.io/@mopi/BJQRdlWLt

### The Legend of the Headless Horseman

By looking into given executable, I knew that head-part of ELF file (for multiple architecture) is hardcoded, so I have extracted from it. Challenge description, text inside executable gave me an idea that distributed files under `body_bag` directory can be combined with these headers. I used QEMU for each architecture to discover which combination of ELF header and body part is working. As a result of analyzing the working ELF file obtained, I got a flag for this challenge.

https://hackmd.io/@mopi/HyAeZbWIK

## Misc

### sanity_check

C&P flag from Discord.

### replay

Send same input as pcap file is showing.

https://hackmd.io/@mopi/SkxH3uZUY

### layers

Save docker image as a file, and extract image file with the flag from it. At first, I though that this is sort of forensics challenge XD

https://hackmd.io/@mopi/H1a26_WUF

### USB Exfiltration

Pcap file with USB packets is distributed. There is nothing complicated here, just extract & combine & decompress. Btw, I was the 3rd person who have solved this challenge!!! 

https://hackmd.io/@mopi/SkL-j3ZUK

### Don't Talk to Blue Birds

Path to flag can be found on following post at Twitter. Searching phrase `Witch Security` will reach out this account.

https://twitter.com/witch_security/status/1450563494802345990

### Open Source In(sta)telligence

Path to flag can be found on following post at Instagram. Searching phrase `Witch Security` will reach out this account.

https://www.instagram.com/p/CVOF4oGLkgU/
