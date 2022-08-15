Một số challenge mà mình làm được trong giải ShellCTF:

**FORENSICS:**

***Alien Communication:***

![image](https://user-images.githubusercontent.com/94149390/184559789-46e77ff9-32da-48f9-a4a2-068988fcd883.png)

*Phân tích:*

Bài này cho ta file [Alien_voice.wav](https://github.com/kietbl/Write-up/blob/main/ShellCTF/Alien_voice.wav).

Đuôi file là wav và cái đầu tiên mình nghĩ tới là dùng Sonic Visualiser.

*Giải:*

Mình dùng phần mềm Sonic Visualiser để mở file wav -> Pane -> Add Spectrogram và nhận được flag:

![image](https://user-images.githubusercontent.com/94149390/184559960-37dda6f8-35cf-468a-a4c8-9ecfe5ccf7d9.png)

>Flag: shell{y0u_g07_7h3_f1ag}

***Go Deep***:
Bài nay mình làm với team :v 

![image](https://user-images.githubusercontent.com/94149390/184560087-afcc099b-e2b3-40cd-b116-1e57ee9bbafc.png)

*Phân tích:*

Bài này cho chúng ta file [Agent.zip](https://github.com/kietbl/Write-up/blob/main/ShellCTF/Agent.zip).

Giải nén và ta nhận được: 

![image](https://user-images.githubusercontent.com/94149390/184560335-a5e42da7-47e1-4a98-ada8-9f07fdcb1d54.png)

Lại là file wav, và việc đầu tiên mình làm là ném vào Sonic Visualiser :v.

Và bởi vì nó là 1 file âm thanh bình thường nên không có gì cả :v.

Thế là mình quay lại challenge và để ý chữ "GO" viết hoa, vậy là challenge này bảo mình làm cái gì đó "deep".

Đầu tiên bọn mình nghĩ là dùng binwalk giải nén tới khi nào có flag thì thôi nhưng càng giải nén thì nó càng ra nhiều file :v, dí đến chết, giờ mà mỗi file đều giải nén thì tới mùa quýt mình mới tìm được flag :v.

Thế nên bọn mình nghĩ sang hướng khác, bọn mình bắt đầu chú ý tới từ "deep" và googlge search "deep sound stego tool" và tìm ra được tool: DeepSound.

![image](https://user-images.githubusercontent.com/94149390/184560817-939d4ec7-0bd5-4174-aebc-c13e81bbcce9.png)

File đã bị gắn mật khẩu, ban đầu mình nghĩ mật khẩu là "godeep" hay đại loại vậy nhưng không đúng.

Thế là mình dùng exiftool để phân tích file:

![image](https://user-images.githubusercontent.com/94149390/184560904-cbcbdc8f-23ea-475e-8066-191bce0acc42.png)

Ra password = "shell", mình nhập vào và nhận được file:

![image](https://user-images.githubusercontent.com/94149390/184560931-66e9ce67-f9cf-4efb-8716-f2ea6f064371.png)

Mở file và nhận được flag:

![image](https://user-images.githubusercontent.com/94149390/184560948-692b76af-f74a-4636-a9df-a4ae49bc3154.png)

>Flag: SHELL{y0u_w3r3_7h1nk1ng_R3ally_D33p}


