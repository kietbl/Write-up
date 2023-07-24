{%hackmd @themes/dracula %}
# Forensics #

## Web ##

Bài này cho ta folder chứa profiles của Firefox, nó bao gồm lịch sử duyệt web, password, bookmarks, cookie mà người dùng lưu trữ trong trình duyệt.

Như mọi khi thì bài này chúng ta dùng Firefox Decrypt để tìm password.

Ta có thể thấy password nào đấy được lưu trong trang ``https://yoteachapp.com``. Thoạt nhìn thì mình tưởng là base64 nhưng không chuẩn, dựa vào tên bài thì mình nghĩ có lẽ phải đăng nhập vào trang này bằng cái password đấy.

![](https://hackmd.io/_uploads/H1JWo8sq2.png)

Trang này yêu cầu mình search ra phòng rồi nhập PIN, vậy nên mình nghĩ password trên là pin của room nào đấy.

![](https://hackmd.io/_uploads/HJaDi8oc3.png)

Vì không có dữ liệu nào về phòng nên mình sẽ tìm kiếm, và mình nghĩ trong lịch sử duyệt web có thể có link vào phòng này nên mình tìm kiếm trong ``places.sqlite``

Sử dụng công cụ MZHistoryView, mình đã tìm thấy link vào room.

![](https://hackmd.io/_uploads/HJkInLo92.png)

Vào đấy, nhập password

![](https://hackmd.io/_uploads/HkiFhUs93.png)

Và tìm thấy flag

![](https://hackmd.io/_uploads/B17j3Li9n.png)

## Crypto ##

![](https://hackmd.io/_uploads/SkhvaLo9n.png)

Bài này cho ta coredump, file chứa flag bị encode, 2 files python.

![](https://hackmd.io/_uploads/SkNJpIiq2.png)

``script.py`` lưu trữ RSA Key nhập từ ``private.pem``

![](https://hackmd.io/_uploads/S13qfPi9n.png)

``gen.py`` đọc nội dung trong ``private.pem`` và ``core.raw`` (coredump của ``script.py``), sau đó nó thay thế nội dung của ``private.pem`` nằm trong ``core.raw`` bằng chuỗi ký tự "A". Rồi sau đó viết nội dung đó vào file ``core``. Cuối cùng là mã hoá ``flag.txt``

![](https://hackmd.io/_uploads/Bko6pUo93.png)

Đây là nội dung của ``private.pem`` đã bị sửa đổi:

![](https://hackmd.io/_uploads/BJ8HEws93.png)

Về bản chất thì ``core`` chứa toàn bộ nội dung của ``core.raw`` vậy nên nó vẫn là một file coredump, và ta có công cụ ``rsakeyfind`` có thể dùng để tìm kiếm ``rsa key`` nằm trong memory dump.

```
rsakeyfind
USAGE: rsakeyfind MEMORY-IMAGE [MODULUS-FILE]
Locates BER-encoded RSA private keys in MEMORY-IMAGE.
If MODULUS-FILE is specified, it will locate private and public keysmatching the hex-encoded modulus read from this file.
```

Sử dụng công cụ vip pro này, chưa tới 2s chúng ta đã có RSA Key nằm trong coredump.

```
rsakeyfind core
FOUND PRIVATE KEY AT 3b4110
version =
00
modulus =
00 b7 08 18 ad 85 12 1d 67 36 0f 49 71 3b 6c 06
50 e3 d9 88 b7 76 27 07 b2 9f b2 32 33 c3 2e 77
ae e3 bb 62 d7 05 74 8b 4a ff d2 57 06 eb 48 58
b8 eb 73 06 78 f2 11 15 2b 07 bf 24 40 61 ab 24
2a 38 ea 40 8c 31 b9 4f 63 90 d5 17 e2 b3 d6 6e
d1 71 af 64 69 63 f8 0d 6a 0e 72 81 94 31 a1 e2
10 92 c4 65 b8 90 b2 3c 37 eb 37 ec 9d 3d 88 31
87 26 f5 aa b5 bd b4 e9 52 0c b7 89 ee cc 60 0f
c6 28 3a 9b b1 3b 03 39 6c 27 ea ff 92 41 ba 0c
a3 a8 2d f5 a9 fa e6 b2 6d 1b c8 a0 ef 55 9a 3a
39 ff 0f f5 60 a3 af f6 f4 1f 6f fb c0 87 8a e7
15 1e b2 6b 52 ca b4 b9 c9 d4 9c 4e aa b1 3f 20
77 50 e8 a2 ed 5c aa f1 e6 4e c8 01 df ef df 91
6d 2f 7f 5f 98 d2 6d 61 04 b0 3e 10 1b be 13 83
89 cc 1b 14 0a 46 8e 05 d0 43 28 08 91 49 6f 70
7d d4 7a e7 2f 84 10 2a 90 73 f6 5d 42 18 9e e6
69 e6 bd be 1d 39 d2 1a 9d c4 1c b3 3c 00 7c d6
e4 05 a8 4a 9b 3c a6 db 52 e4 e0 1c 5a 61 b0 7b
5c ee 53 47 0d 57 15 71 fe bc 21 25 bd ad e1 18
e5 36 3e 72 01 40 71 dc af 41 26 82 0c 57 3d b1
a2 5a c8 34 79 12 0e 1a 23 bb 99 49 d5 ef 62 0f
c0 dc 2c 37 48 10 55 6e fb f3 78 10 e0 14 ed 40
3e 34 2a dc 58 dc 3d 08 a0 72 4a 03 4a a0 5f 04
dd 72 98 f5 c2 6e c4 a9 a2 6a 0b 65 06 eb a7 f2
77 d4 d1 28 2e 19 b8 ae 69 b9 7c 30 0c 66 54 42
47 7a c6 78 71 ec bd 4b 4c fc ea de bb 4f 8d 89
a7 ee ae a1 55 9d 0f ee 23 99 76 7d b3 3d 72 0e
33 96 a7 04 ca 7d 13 60 e9 09 5f 40 1d 7a 2d 25
7d 9f 34 82 b2 dc 36 a0 49 95 8f 1f a2 d1 81 62
bd e8 95 99 37 b4 0f 19 dc 9c b2 4d 33 79 7f 9d
ac b4 47 e7 8a 54 ee 4f 0b d7 8d 3e a9 e7 8b 91
d7 7c 7f fe 6a 6d 01 73 1b 9f ab 0b 69 03 bb d7
c9
publicExponent =
01 00 01
privateExponent =
4e d4 b7 60 40 e0 98 f6 63 93 b8 f3 82 7e d7 c7
8e 23 1a 9d bd cf 38 a3 07 e2 05 7a 42 d5 e8 29
54 9f 58 06 39 2c c5 73 a3 74 32 14 62 50 d2 19
af 1a e7 0e 3f 5f f9 28 83 d9 20 e1 73 cf 74 af
94 bc db d9 25 ef 64 d0 82 c8 2c d5 f0 d3 e9 fd
de 43 86 f1 57 63 b6 4f 57 f2 9c 79 b9 d8 b9 d7
9e 86 e5 32 d5 6b 56 c1 54 d7 4c db 3a 6d c6 bb
29 9e 04 8c eb 2c 98 93 44 b3 e6 3e 0a 1b 53 9f
ea a2 b7 7c e1 68 55 9a 8e 24 ca 37 71 84 92 be
08 08 e7 76 73 00 5a 97 e0 b0 0c 14 98 df 68 b5
a2 f3 fa 20 c9 cf 57 83 34 69 14 5a 8b 66 59 79
c0 b3 62 c3 11 58 3e 6e bd da 26 8d a0 f2 89 e6
a9 db d0 94 7f e5 86 73 45 ba 22 08 93 b5 4c 5a
e4 ea ac bf 52 de 56 3b 61 37 11 d4 09 0d 39 89
a7 75 80 2f 9e cc bb 97 43 dd 61 b7 45 94 30 34
99 c7 e7 7b 91 1b 2f 61 f0 f8 0a 99 95 a6 16 a1
8a 58 27 c6 94 25 aa f9 d7 04 d9 f1 7b 29 32 c8
32 a7 4a 0c 15 a9 eb 5f 58 23 e6 fd b0 04 dd d5
e8 d7 ee 2b 15 35 37 1a fe 27 7e 4c 61 96 35 1c
fa 58 82 c8 37 13 51 6c eb 56 05 b0 9e 7d 39 9d
6a a2 c7 56 5a 0b 55 b4 0c e9 dc 54 6d f1 06 23
f0 2c f2 20 95 15 bf 70 b9 f1 26 b9 6b 4c 3c fa
6c 8e 48 80 45 3d e9 47 63 26 ff cb 56 59 99 f5
79 c4 10 fe d8 6f f4 d5 36 ff e7 63 b2 a5 4a 38
eb 49 ad c7 8b 5f 0c 68 58 b9 5f 41 ad fc 56 c0
10 e2 0f df e5 fc e5 9d 56 09 19 f3 4e 25 fa 82
63 18 51 5d 2a 0e dc a5 ee 56 b4 8d d8 26 3a fd
cf f4 0d 0d c6 be 74 4e b9 46 e6 34 e7 61 a8 5f
ea 6f 8a ca b6 e3 9a 25 ea 47 64 39 ee 63 f4 37
b5 84 a4 a7 f8 26 8e c6 c3 6d 81 b6 98 76 a6 59
de df 53 0b 45 9b 61 25 11 12 49 f7 63 64 7d c8
b5 1c 15 84 2a 73 da 3b 9b b1 96 ed f1 a4 e6 73
prime1 =
00 e3 35 02 0b d3 81 a7 42 7b 52 f8 6e 43 a8 90
1e 5e 13 94 6f 05 01 94 79 8c a7 4f 58 d9 c8 4b
b4 30 f9 39 8d ac a2 ac aa c1 ad ba a3 c8 ba 19
4e 1b 7f 21 ab 1f 6e e2 3f 6c 0b 22 19 4d 49 86
97 d8 b6 56 e7 5a 26 d4 b2 78 d0 41 e5 80 bf 22
ea d8 ab 24 89 e2 5b fa c5 55 46 46 4c 1e dd 5d
47 77 b9 15 35 83 3e a7 d0 b9 21 b4 41 d6 c2 1c
a7 45 31 ef be 1d 2a 38 05 0a 71 43 d1 7e ce c9
c6 6d 1d 53 08 e2 10 c1 27 ac 31 d9 f8 29 6f 54
a4 d0 cf 52 f9 c1 d9 ff 30 62 84 cc 35 8f 92 42
df 49 78 13 4a 3c 2a fd 78 fb 45 46 59 6b 74 f4
38 77 34 65 4b 8a c0 ff c2 1b b5 09 7d 81 36 ca
13 00 64 89 63 b5 f5 ec bc 66 30 de 71 16 6a f2
4b b7 50 4d 6c 88 2d 62 69 cc b1 b7 f3 8e 61 ed
1d 3a 20 80 1c a5 3e ea de c8 ab 65 8c 60 4c b2
46 f1 7e 56 49 10 3a 22 9c 53 73 2f 71 23 01 90
9f
prime2 =
00 ce 39 f5 e2 44 26 db 57 bf 4d 46 21 76 30 99
90 5a 92 6c 9d a2 4c 5a c6 dd 8b b3 05 64 f7 5d
83 11 61 ce 9a 05 db 65 ed bf cb 46 63 94 cc e5
4e 20 8f 9b 6c dc e8 94 dd 8e ac 7d af dc 0a 78
14 9f 71 b0 80 71 2b 33 12 bd fb 44 ed cb 86 cd
ef c3 7e 26 6c 65 e7 2a 43 f6 fb 3c a0 4d f7 78
10 95 77 67 ad 39 70 73 13 1d 60 59 7f 5a 09 3a
f3 af d6 e8 2b 83 5f b9 b8 5d a3 2f c5 fb c6 45
df 47 4a b0 7a dd 20 c4 23 4c f4 cb 00 a1 c8 79
a2 08 4a 9f c9 13 e4 df dd 80 32 91 1c 59 12 6d
f2 72 19 a1 f3 bf a6 f0 6e bb 71 d5 30 f9 56 03
52 08 20 75 39 7c 34 03 ae 26 31 a4 ca 0c b3 96
f7 52 56 bd 6a 9d 5f 83 be fd a5 7d ae 28 9c a3
b8 0d 78 5f b5 8f 81 5a 99 e7 9a 69 f5 c7 3c 1e
98 8b 6c cc cc 1b 7c fe 91 02 d3 5d 21 16 e0 2d
58 e9 65 01 e2 60 05 4f d2 6a 6e 71 5f cd f2 36
97
exponent1 =
00 a6 d9 63 72 49 d5 19 7e 11 d0 61 4d 9f f3 e6
39 d6 be 2c 2f c7 0e 5c f9 51 d7 ff c5 f0 d0 4c
ff bb 43 1a 07 b7 32 12 f8 a6 98 2c ca 5f fa 81
e0 c1 02 80 38 09 32 45 85 6b 93 55 6c 1d 3c 94
51 68 3c a6 de e8 6e 41 9d fa 68 7d 2b cb 7b 11
6a 42 c8 93 61 8a 50 1f 7c d0 5d 75 fc 2a 62 47
40 d7 63 9c 76 9a b5 e5 48 ba db 4c 63 59 11 5d
37 5c f2 09 eb a3 c1 40 38 3b 6d e7 ee ad 5f af
ff 3e 5b dd 81 55 7a 16 4e 4a fe a4 e1 bf 93 d9
25 db 8d 94 92 82 cf d2 bd b6 6b 6d 07 1c 32 bd
56 f9 bd ea f4 85 f0 d0 09 0b 04 d9 5f 8b 30 ec
b3 96 9e 6c 90 a8 09 89 08 03 8e d3 b4 4d b2 6d
7c bc ba 17 c6 98 76 4b 6e 22 e1 5a 48 d3 bc 9f
c8 08 8b 39 00 2d 94 9e 83 b1 65 05 00 1f 9c 3c
23 4d 6f 56 85 32 b8 5c 84 53 fa c1 ec ef 3f 03
60 63 b1 72 2d df 73 c1 bc e8 ef 5a 74 8b cf 77
63
exponent2 =
08 7a fb b0 85 4a ef c4 28 fa bd 82 8c c0 ac df
f1 c9 89 ab e5 cb ac 03 f7 82 52 27 4b a2 9c 76
d2 55 2a 50 cd 22 db 8b 2e 52 e6 9f 1b b4 5d 4d
9c 2b 2b a2 0b 67 89 0f 9f 59 5a 85 cb cf a6 66
7a b4 0b 25 0d 02 50 c4 59 89 a6 a8 fc b0 9b 71
5b a1 a9 64 d5 bf ee ce 02 af 0c 2c 2f 68 98 c8
5d cd 1e 37 52 60 5c 6b 11 cf 2f 7e fc 2d da fd
c0 62 5f 03 c3 10 41 ab e2 35 de b6 0e 87 75 c2
1b c5 67 82 75 eb 6e 05 61 99 c3 6b 50 3b d5 3c
a3 88 a6 0f 77 c0 f6 af be 20 d6 34 cb 38 e5 ca
8d 84 9e 53 4e b3 47 b9 d5 fd 75 5d e1 32 f7 49
fd 49 90 68 7f c2 07 d0 b6 83 5e 1a 6c f1 49 a8
fb fa d3 29 3a 35 7e 4e 71 51 c7 dc 4c 91 3c 6e
4a 27 84 10 0c 49 ae 77 1b be 0e d3 6b 90 fe 55
19 8e ac d1 4b e4 14 f7 d3 9c eb 7c 7b db 56 22
0c de f9 77 dc a8 fb 42 54 dc 1b cf 6b c1 a5 1b
coefficient =
00 9f 7d 85 a4 54 59 cc df 30 d0 11 a5 b7 5f b1
6f e4 7a 80 b7 1a 1b 5f 26 7e ff e1 3e c5 8f 00
5b ca 25 a8 86 e2 d0 89 ee 74 b5 17 91 dc 27 83
92 e3 00 14 dd 78 60 e1 6e 51 74 ac ac 41 19 34
a3 26 ec fc 63 0f 1f c3 ed c6 92 aa 18 80 bd 21
9f f1 59 34 46 fe b1 7f ec fc fd 5a f5 04 a3 11
cd 2a e1 35 c2 5e ff 0e e9 a8 f9 7d 3a 6c 52 39
7b 12 e6 df ee 48 b9 02 a5 09 1b 20 d8 bc a9 27
3c 1b cf 74 46 42 4c 5a dc 00 ed 0e 59 53 06 cc
1e 39 91 8c 00 dc e5 dd 22 fd b8 d0 eb 40 84 8d
66 ff d8 7d 5f 83 4d c8 5e c4 8c 79 c3 05 69 34
a6 81 f8 b4 57 8a b3 8b c7 23 8d 5d e8 f2 08 21
3f 4f 77 04 69 b6 d8 c4 a1 40 37 94 f7 c6 21 79
ce 44 d7 53 dd 8c e0 94 d0 66 9e 36 93 7e dd 1c
30 3e 25 dd 07 38 00 ee d7 71 c9 ce c3 c7 22 45
0b 32 de 93 05 6a b3 9c a6 1a c0 26 3e cf b5 32
7a
```

Tuy nhiên thì nó in ra key ở dạng BER-encoded, mình không thể dùng cái này để decrypt được. 

Ban đầu mình viết script python để giải mã dựa trên thông tin trong key như c, d, n, p, q, e invq nhưng mà không ra. Sau khi dò la thì mình biết được trong thư viện Cryptodome có class RSA.construct, mình sẽ viết script để tạo file PEM.
https://pycryptodome.readthedocs.io/en/latest/src/public_key/rsa.html


```python 
from Crypto.PublicKey import RSA


n = int(0xb70818ad85121d67360f49713b6c0650e3d988b7762707b29fb23233c32e77aee3bb62d705748b4affd25706eb4858b8eb730678f211152b07bf244061ab242a38ea408c31b94f6390d517e2b3d66ed171af646963f80d6a0e72819431a1e21092c465b890b23c37eb37ec9d3d88318726f5aab5bdb4e9520cb789eecc600fc6283a9bb13b03396c27eaff9241ba0ca3a82df5a9fae6b26d1bc8a0ef559a3a39ff0ff560a3aff6f41f6ffbc0878ae7151eb26b52cab4b9c9d49c4eaab13f207750e8a2ed5caaf1e64ec801dfefdf916d2f7f5f98d26d6104b03e101bbe138389cc1b140a468e05d043280891496f707dd47ae72f84102a9073f65d42189ee669e6bdbe1d39d21a9dc41cb33c007cd6e405a84a9b3ca6db52e4e01c5a61b07b5cee53470d571571febc2125bdade118e5363e72014071dcaf4126820c573db1a25ac83479120e1a23bb9949d5ef620fc0dc2c374810556efbf37810e014ed403e342adc58dc3d08a0724a034aa05f04dd7298f5c26ec4a9a26a0b6506eba7f277d4d1282e19b8ae69b97c300c665442477ac67871ecbd4b4cfceadebb4f8d89a7eeaea1559d0fee2399767db33d720e3396a704ca7d1360e9095f401d7a2d257d9f3482b2dc36a049958f1fa2d18162bde8959937b40f19dc9cb24d33797f9dacb447e78a54ee4f0bd78d3ea9e78b91d77c7ffe6a6d01731b9fab0b6903bbd7c9)
e = int(0x010001)
d = int(0x4ed4b76040e098f66393b8f3827ed7c78e231a9dbdcf38a307e2057a42d5e829549f5806392cc573a37432146250d219af1ae70e3f5ff92883d920e173cf74af94bcdbd925ef64d082c82cd5f0d3e9fdde4386f15763b64f57f29c79b9d8b9d79e86e532d56b56c154d74cdb3a6dc6bb299e048ceb2c989344b3e63e0a1b539feaa2b77ce168559a8e24ca37718492be0808e77673005a97e0b00c1498df68b5a2f3fa20c9cf57833469145a8b665979c0b362c311583e6ebdda268da0f289e6a9dbd0947fe5867345ba220893b54c5ae4eaacbf52de563b613711d4090d3989a775802f9eccbb9743dd61b74594303499c7e77b911b2f61f0f80a9995a616a18a5827c69425aaf9d704d9f17b2932c832a74a0c15a9eb5f5823e6fdb004ddd5e8d7ee2b1535371afe277e4c6196351cfa5882c83713516ceb5605b09e7d399d6aa2c7565a0b55b40ce9dc546df10623f02cf2209515bf70b9f126b96b4c3cfa6c8e4880453de9476326ffcb565999f579c410fed86ff4d536ffe763b2a54a38eb49adc78b5f0c6858b95f41adfc56c010e20fdfe5fce59d560919f34e25fa826318515d2a0edca5ee56b48dd8263afdcff40d0dc6be744eb946e634e761a85fea6f8acab6e39a25ea476439ee63f437b584a4a7f8268ec6c36d81b69876a659dedf530b459b6125111249f763647dc8b51c15842a73da3b9bb196edf1a4e673)
p = int(0x00e335020bd381a7427b52f86e43a8901e5e13946f050194798ca74f58d9c84bb430f9398daca2acaac1adbaa3c8ba194e1b7f21ab1f6ee23f6c0b22194d498697d8b656e75a26d4b278d041e580bf22ead8ab2489e25bfac55546464c1edd5d4777b91535833ea7d0b921b441d6c21ca74531efbe1d2a38050a7143d17ecec9c66d1d5308e210c127ac31d9f8296f54a4d0cf52f9c1d9ff306284cc358f9242df4978134a3c2afd78fb4546596b74f4387734654b8ac0ffc21bb5097d8136ca1300648963b5f5ecbc6630de71166af24bb7504d6c882d6269ccb1b7f38e61ed1d3a20801ca53eeadec8ab658c604cb246f17e5649103a229c53732f712301909f)
q = int(0x00ce39f5e24426db57bf4d4621763099905a926c9da24c5ac6dd8bb30564f75d831161ce9a05db65edbfcb466394cce54e208f9b6cdce894dd8eac7dafdc0a78149f71b080712b3312bdfb44edcb86cdefc37e266c65e72a43f6fb3ca04df77810957767ad397073131d60597f5a093af3afd6e82b835fb9b85da32fc5fbc645df474ab07add20c4234cf4cb00a1c879a2084a9fc913e4dfdd8032911c59126df27219a1f3bfa6f06ebb71d530f9560352082075397c3403ae2631a4ca0cb396f75256bd6a9d5f83befda57dae289ca3b80d785fb58f815a99e79a69f5c73c1e988b6ccccc1b7cfe9102d35d2116e02d58e96501e260054fd26a6e715fcdf23697)
invq = int(0x009f7d85a45459ccdf30d011a5b75fb16fe47a80b71a1b5f267effe13ec58f005bca25a886e2d089ee74b51791dc278392e30014dd7860e16e5174acac411934a326ecfc630f1fc3edc692aa1880bd219ff1593446feb17fecfcfd5af504a311cd2ae135c25eff0ee9a8f97d3a6c52397b12e6dfee48b902a5091b20d8bca9273c1bcf7446424c5adc00ed0e595306cc1e39918c00dce5dd22fdb8d0eb40848d66ffd87d5f834dc85ec48c79c3056934a681f8b4578ab38bc7238d5de8f208213f4f770469b6d8c4a1403794f7c62179ce44d753dd8ce094d0669e36937edd1c303e25dd073800eed771c9cec3c722450b32de93056ab39ca61ac0263ecfb5327a)

private_key = RSA.construct((n, e, d, p, q))

open("private.pem", "wb").write(private_key.exportKey())
```

Sau đó dùng lệnh ``openssl pkeyutl -decrypt -in flag.enc -out flag.txt -inkey private.pem`` để giải mã sử dụng ``private.pem`` đã tạo ra bên trên.

Mở file ``flag.txt`` và lấy flag

Flag: ``ictf{oops_looks_l1ke_i_didn't_scrub_the_key_completely_out_of_memory}
``

## Forensics ##

Bài này cho ta file ``forensics.pcap`` và ``sslkeylog.log`` có thể dùng để decrypt ``tls``.

Tuy nhiên, khi mở file pcap lên thì nó bị lỗi

![](https://hackmd.io/_uploads/rk61dDi9n.png)

Sử dụng công cụ để đọc bytes của file pcap, ta có thể dễ dàng thấy header đã bị sửa đổi

![](https://hackmd.io/_uploads/HkdQOwoc3.png)

Dựa vào bài [này](https://www.netresec.com/?page=Blog&month=2022-10&post=What-is-a-PCAP-file), mình sẽ sửa đổi lại header cho đúng.

![](https://hackmd.io/_uploads/ByXeFDoqn.png)

Nhưng mà lần này lại bị sai chiều dài packet.

![](https://hackmd.io/_uploads/By9bKwiq2.png)

Ban đầu mình sử dụng công cụ ``pcapfix`` nhưng sau khi fix xong thì nó vẫn bị lỗi (hiện 1 packet).

Thế nên mình quyết định sửa tay.

Đây là cấu trúc cơ bản của 1 packet.

![](https://hackmd.io/_uploads/r1Q85vi92.png)

Ở frame đầu tiên, capture length đã bị thay đổi và khác với original length, vậy nên mình phải sửa capture length bằng original length.

![](https://hackmd.io/_uploads/BkhFoPo9n.png)
![](https://hackmd.io/_uploads/r1TnjPj9h.png)

![](https://hackmd.io/_uploads/H1bsoDic2.png)
![](https://hackmd.io/_uploads/SJTTjPs9h.png)

Ở các layer sau thì tổng size của nó đúng bằng 110 bytes.

Với các frame sau cũng tương tự, và có rất nhiều frame như thế này nên làm tay rất hại cột sống. Vậy nên mình viết script cho nó tự động làm.

![](https://hackmd.io/_uploads/rJe7nwo52.png)

Script:

```python 
def process_bytes(data, i):

    data[i:i+4] = [0x23, 0x58, 0xAB, 0x64]
    
    data[i+8:i+12] = data[i+12:i+16]
    
    new_index = int.from_bytes(data[i+12:i+16], byteorder='little') + 15 + i + 1
    
    return new_index


with open('forensics.pcap', 'rb') as file1:
    data = bytearray(file1.read())

i = 24

while i < len(data):
    new_index = process_bytes(data, i)
    i = new_index

with open('forensics2.pcap', 'wb') as file2:
    file2.write(data)
```

Chạy script, và ta nhận được file pcap xịn

![](https://hackmd.io/_uploads/rkf4aDo9n.png)

Thêm sslkey

![](https://hackmd.io/_uploads/SJPw6Diqn.png)

Export HTTP Object và lấy flag

![](https://hackmd.io/_uploads/HyOqTvoqh.png)

## Rev ##

Bài này cho ta 2 files:

![](https://hackmd.io/_uploads/B1ijRvoq2.png)

https://www.thomas-krenn.com/en/wiki/Linux_Software_RAID_Information

Như thường lệ thì mình chạy các dòng lệnh như bên dưới để tạo 1 partition chứa dữ liệu của 2 raid devices này. 

```
losetup /dev/loop2 img1
losetup /dev/loop3 img2
mdadm --create /dev/md1 --assume-clean --level=5 --verbose --chunk=512 --raid-devices=3 /dev/loop2 /dev/loop3 missing
mount /dev/md1 /mnt
```
Trong này chứa 1 file elf được viết bằng Go Lang và 3 file nằm trong ``home``

```
# ls /mnt
home  lost+found  ransomware
# file /mnt/ransomware
/mnt/ransomware: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, Go BuildID=DOPhn9Tv31AcI0Va35oK/Cv0N-CfAjtfGGBnafKKh/oDwgo_vgMXZbuvyfQiWs/4kNwoFX7AoxwBMKpZ1Fa, not stripped
# ls /mnt/home
dummy.pdf  flag.png  index.html
```
Copy mấy file đó về máy chính để phân tích thì thấy có vẻ 3 file đó đã bị mã hoá bởi file thực thi ``ransomware`` kia.

![](https://hackmd.io/_uploads/SyaeeOi5n.png)

Mình có thử reverse ``ransomware`` thì thấy không khả thi lắm vì nếu reverse cái file khó như này thì sao bài này lắm solve thế :))).

Thế là mình thử 1 cách tiếp cận khác bài này đó là xor thử 2 cái img kia với nhau thì mình thấy byte của PNG file nằm trong này.

![](https://hackmd.io/_uploads/SyAOgOicn.png)

Mình extract nó ra và lấy flag.

![](https://hackmd.io/_uploads/r1GZZ_oqh.png)

## Blurry ##

Dùng GIMP -> Enhance -> Sharpen, dùng quét QR của Zalo để quét, done.

![](https://hackmd.io/_uploads/SkXlFXh93.png)
