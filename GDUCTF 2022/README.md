Dưới đây là 1 số challenge mình đã giải được ở giải này, và vì server thi chỉ tồn tại đến hết giải nên mình không có hình ảnh cụ thể về 
challenge description mà chỉ có text.
Ok, bắt đầu nào:
<details>
     <summary><h3> FORENSIC: </h3></summary>
        <details>
             <summary><h3> Sky </h3></summary>

**Description: where is my sky???**

Challenge này cho mình file Sky.wav. Khi nhìn thấy file có đuôi wav, cái mình nghĩ tới đầu tiên là Spectrogram của nó.

Thế là mình dùng Sonic Visualiser để mở:

![image](https://user-images.githubusercontent.com/94149390/185822665-59615b81-8563-487c-b080-1271bcf33c47.png)

Đúng như dự đoán, flag nằm trong spectrogram của file wav

![image](https://user-images.githubusercontent.com/94149390/185822683-17b04a95-d8a2-4e12-941a-b8f084874b84.png)

Và mình nhận được flag:

>Flag: GDUCTF{cl0ud5_b1rd_cl0ud5_pl4n3_cl0ud5}
    </details>
        <details>
            <summary><h3> Conversation </h3></summary>

**Description: they are talking about something, can you find their secret?**

Challenge cho mình file Conversation.pcap.

Mở lên và giao diện của nó như sau: 

![image](https://user-images.githubusercontent.com/94149390/185823072-59bdd4f5-f79b-475f-8d85-eed14ece81fb.png)

Kéo xuống mình thấy protocol "TCP", và như một thói quen, mình follow TCP Stream:

![image](https://user-images.githubusercontent.com/94149390/185823140-091c13a8-c5e1-40e1-8d72-32989233b1dd.png)

Ok, stream đầu tiên là đoạn chat giữa 2 người với nhau kèm theo đó là link rick roll (nhìn đuôi là biết, làm sao lừa được :v)

![image](https://user-images.githubusercontent.com/94149390/185823174-d896a1be-0e8b-41e7-b081-ce66f8cc6465.png)

Các stream tiếp theo là đoạn giải thích về protocol, ip, mình cũng lười đọc nên next 

![image](https://user-images.githubusercontent.com/94149390/185823355-684c13b6-f05d-4aa1-9fe4-9a2f2365e9e3.png)

![image](https://user-images.githubusercontent.com/94149390/185823429-080ac4fd-86b7-46f6-ab87-52c1591f1539.png)

Ok, stream tiếp theo là strings của file 

![image](https://user-images.githubusercontent.com/94149390/185823458-80cabb5a-1feb-4dc7-9f34-8fdf937448a1.png)

Nhìn đoạn đầu là biết  file này là file jpg

https://en.wikipedia.org/wiki/List_of_file_signatures

![image](https://user-images.githubusercontent.com/94149390/185823501-2f0a0999-e6ec-45b9-9484-03ab7097193b.png)

Show data as raw

![image](https://user-images.githubusercontent.com/94149390/185823557-09403198-32b4-4b3f-920d-a6c9eae7d247.png)

Copy đống raw này về 

![image](https://user-images.githubusercontent.com/94149390/185823641-cec2643e-e436-40f7-aa7f-6f872181c91a.png)

Dán vào hex editor:

![image](https://user-images.githubusercontent.com/94149390/185823710-ccac7cb9-65ae-4378-9502-5abbf4679aca.png)

Lưu lại với tên gì đó tuỳ bạn, ở đây mình lưu là flag.jpg

Mở lên và ta có flag......khoan, đó là mình nghĩ thế 

Mở lên và đó là 1 file QR, mình scan và nhận được cái link quen thuộc

![image](https://user-images.githubusercontent.com/94149390/185823864-d240ebf3-d633-40df-b4f0-aa5866bf11c6.png)

Ok, quay lại, mở stream tiếp theo, cũng không có gì hot.

![image](https://user-images.githubusercontent.com/94149390/185823944-36b5fd4b-78a5-4f68-8748-0d7da48e4648.png)

Stream tiếp theo cũng là strings của file jpg nhưng cái này khác với cái trước đó 

![image](https://user-images.githubusercontent.com/94149390/185823992-437d49ef-1bb2-42a3-a40a-202acf840de7.png)

Như các bước hồi nãy, lần này mình lưu file với tên là test.jpg 

Mở lên và có flag

![image](https://user-images.githubusercontent.com/94149390/185824173-79cc9275-9413-4c81-be0f-8e0c69238b3f.png)

>Flag: GDUCTF{hmmm_0nly_TCP_15_n0t_53cur3}
    </details>
        <details>
            <summary><h3> Message from the universe </h3></summary>

**Description: The stars are just small dots in the long long night**

Challenge này cho mình file message.png

Mở file lên và điều đầu tiên mình nghĩ là file này nhỏ quá.

![image](https://user-images.githubusercontent.com/94149390/185824732-9b8c4946-cb25-4dd1-ae9f-6b3034d48950.png)

Ban đầu mình nghĩ phóng to hình ảnh lên là ra flag nên dùng vài tool phóng to hình ảnh nhưng thất bại...

Thế là mình quay lại đọc description, mình chú ý từ "dots" và mở file lên thì nhận thấy nó giống mã morse,
thế là mình ngồi decode bằng tay và mắt :v, mắt muốn lòi ra nhưng kết quả decode submit mãi không đúng, quá cay!

Một hồi sau có hint là dùng script để quét, thế là mình google mấy cái script quét màu trong ảnh rồi viết thêm ra script:

```
import numpy 
from PIL import Image


def get_image(image_path):
    image = Image.open(image_path, "r")
    width, height = image.size
    pixel_values = list(image.getdata())
    if image.mode == "RGB":
        channels = 3
    elif image.mode == "L":
        channels = 1
    else:
        print("Unknown mode: %s" % image.mode)
        return None
    pixel_values = numpy.array(pixel_values).reshape((width, height, channels))
    return pixel_values


image = get_image("message.png")
grey = image[5]
black = image[14]
white = image[0]
words =""
for i in range (0, 193):
    if (image[i] == white).all():
        words += "."
    elif (image[i] == grey).all():
        words += " "
    elif (image[i] == black).all():
        words += "-"
print(words)
```
Quét và mình nhận được:

```
.....  -  ....-  .-.  .....  -....-  -.-.  ....-  -.  -  -....-  .....  ....  .----  -.  ...--  -....-  .--  .----  -  ....  -----  ..-  -  -....-  -..  ....-  .-.  -.-  -.  ...--  .....  .....
```

Ném vào tool decode và mình nhận được:

``` 5T4R5-C4NT-5H1N3-W1TH0UT-D4RKN355 ```
>Flag: GDUCTF{5T4R5-C4NT-5H1N3-W1TH0UT-D4RKN355}
    </details>
        <details>
            <summary><h3> Before The Dead </h3></summary>

**Description: I find playing guessing game is interesting so I took one on google. After finishing the first round, my computer suddenly show errors get crashed. Luckily, I was able to dump memory before the crash happen. Could you analyze what was happening in my computer at that time?
Ah and I know the attacker ip address but don't know what port is opening, maybe you can try with the bind port he used when he attacked my computer.
P/s: The game file contains not a real virus so feel free to run it without any worry =)))
Attacker ip: (xong giải nên server tắt rồi :v)** 

Bài cho mình file memory.raw. Mình dùng volatility3 để phân tích memory dump này

Mình dùng plugin: windows.info (với volatility2 thì các bạn có thể dùng imageinfo cũng tương tự)

![image](https://user-images.githubusercontent.com/94149390/185831707-ec31c8fc-90db-4277-beec-133e96493c90.png)

Dựa vào những thông tin mình nhận được thì hệ điều hành của máy nạn nhân là Windows XPSP2 32 bit.

Tiếp theo mình check port, bởi vì phiên bản hệ điều hành Win XPSP2 không hỗ trợ và bị lỗi nên mình không dùng windows.netscan và windows.netstat được nên 
mình dùng volatility2 với plugin connections

![image](https://user-images.githubusercontent.com/94149390/185832624-f59db9d4-fec8-4308-9daa-10e79faaa859.png)

Chỉ có 1 connection và 1 port, vậy suy ra port của kẻ tấn công là 30002

Tới đây thì mình không biết phải làm gì tiếp nên thử hết mấy cái plugin như windows.pstree, hivelist

1 hồi sau thì challenge description thay đổi thành gì đấy mình không nhớ rõ nhưng đại khái là bảo người chơi chơi game. Kèm với 1 cái hint to bự rất hữu ích: 
"Volatility". Vâng, cảm ơn hint rất hữu ích của ông, tác giả bài này.

Thế là mình dùng pstree xem những ứng dụng nào chạy vào lúc ấy 

Xem nào, guessing_game.e... mình nghĩ đây là file game.

![image](https://user-images.githubusercontent.com/94149390/185890125-a35e53cb-472b-4982-b568-32d8f50f8b7b.png)

Thế là mình thử dumpfile PID này ra thử xem có gì không

![image](https://user-images.githubusercontent.com/94149390/185890686-7b8bd938-2540-4f69-b291-1fdcd8fe7274.png)

Mình nhận được mấy files này đây (lưu ý, tắt win defender hoặc ném vào linux, ở đây mình ném vào linux để xem file DAT chứa gì)

![image](https://user-images.githubusercontent.com/94149390/185890906-9d54b3fe-ce07-41c3-80ea-152a8412b386.png)

Mình dùng lệnh file để phân tích file DAT này và nhận thấy rằng nó là file execute

![image](https://user-images.githubusercontent.com/94149390/185891492-79626a01-2f92-4af4-9852-4a6e274274d6.png)

Mình quay lại máy chính, đổi extension của file và mở lên 

![image](https://user-images.githubusercontent.com/94149390/185892144-ff5f07e6-ffc5-4d02-aae4-9c0d08acc7be.png)

![image](https://user-images.githubusercontent.com/94149390/185892205-e2f47487-64a6-4c4f-a882-04527c236ad2.png)

Sau khi hoàn thành cái game này thì mình nhận được password là 
``` 23ssqn_9qtHuSqLUbTTWSNC8P9rw_sLX ```

Ok, còn username thôi, mình lại quay lại máy ảo, dùng lệnh strings để xem có gì không

Mình grep hết tất cả những cái liên quan tới guessing thì nhận được tên riêng có vẻ giống username 

![image](https://user-images.githubusercontent.com/94149390/185892609-ac573397-56b3-4a7f-a72b-c0959b93306e.png)

Nhập username và password thì nhận được file flag.txt, cat nó và mình nhận được flag:

![image](https://user-images.githubusercontent.com/94149390/185892820-1c14028f-0b49-49f1-8ce6-5d613875ef0f.png)

>flag: GDUCTF{y0U_sh0uLD_b4Ck_Up_d4T4_r3Gul4RlY_30b7984ff561521b99f6b3887c212386}
     </details>
         </details>
         
<details>
    <summary><h3> OSINT: </h3></summary>
        <details>
            <summary><h3> Forgotten photo </h3> </summary>

**Description:
Elios Roger is the photographer I hired to prepare the photos for the Moon exhibit. He said he would send me the photo, but the exhibition day is coming and I can't contact him.
Can you help me find the red moon photo he helped me take for the exhibition?**

Tìm tên Elios Roger trên Twitter, link twitter của đối tượng: https://twitter.com/EliosRoger.

![image](https://user-images.githubusercontent.com/94149390/185829613-d8e05ee4-90cd-49e6-a62c-e274a99b2911.png)

Lướt 1 vòng trang cá nhân thì mình cũng không thấy có gì đặc biệt, tải thử mấy cái ảnh về, dùng [Fotoforensics](https://fotoforensics.com/) để phân tích cũng
không thấy gì.

Mình vào view list:

![image](https://user-images.githubusercontent.com/94149390/185829785-91384748-30ad-4c1a-9678-5267ff310ecf.png)

Và thấy Customer's photo

![image](https://user-images.githubusercontent.com/94149390/185829810-32ad7e8d-a6ea-4a20-865f-0cf1a9e1cc14.png)

Truy cập vào [link](https://drive.google.com/drive/folders/1cRudn2snJBPHE1upCxSB6QV31sFnRYln) bên trong và nhận được 

![image](https://user-images.githubusercontent.com/94149390/185830116-444793f2-d48c-47fe-8efc-d5d014055d33.png)

Tải về, ném vào Fotoforensics, vào meta data và nhận được flag:

![image](https://user-images.githubusercontent.com/94149390/185830171-93349087-9d11-4ef5-8694-d3e60fb80238.png)

>Flag: GDUCTF{r3d_m00n_f0r_th3_3xh1b1t10n}
    </details>  
</details>

<details>
    <summary><h3> Crypto </h3></summary>
        <details>
            <summary><h3> MasterChef </h3></summary>
            
**Description: longkd719 is a MasterChef. He can bake the flag. :D???**
    
Challenge cho mình file chall.txt 
    
Nội dung như sau:
    
```
R1pSU0FOVEdFQTNHS0lCV0c0UURNWVJBR1kyQ0FNWlhFQVpUQ0lCVEhFUURFTUJBR1pRU0FOWlZFQTNUR0lCWEdRUURFTUJBR1laU0FOVEdFQTNHTUlCV01JUURNTkpBR1kyQ0FNUlFFQTNEQ0lCU0dBUURNWkpBR1kyU0FOWlhFQVpEQUlCV0dNUURNTUpBR1pSQ0FOUlZFQVpXQ0lCU0dBUURJTlpBR1EyQ0FOSlZFQTJER0lCVkdRUURJTlJBRzVSQ0FOQlVFQVpUR0lCV0dNUURHTUJBR1kyQ0FNWlJFQTNHS0lCV0c0UURLWlJBR1UzU0FNWlJFQTNUSUlCV0hBUURLWlJBR1FaU0FOWlpFQTNERUlCVEdNUURPTVJBR1FaU0FOUllFQVpUR0lCV0dZUURPWkE9
```
    
Ok, đề bài nhắc đến từ "chef", vậy thì không cần suy nghĩ nữa, ném thẳng vào [CyberChef](https://gchq.github.io/CyberChef/) thôi.
    
Bấm vào biểu tượng cái gậy phép tới khi nào ra output là flag thì thôi
    
![image](https://user-images.githubusercontent.com/94149390/185902234-2409190a-3b4e-4c0f-aa09-e068788c8f82.png)

![image](https://user-images.githubusercontent.com/94149390/185902323-27eb257e-2660-4ce1-974a-536e817b8d0b.png)

>Flag: GDUCTF{D3c0d1ng_W1th_Cyb3rCh3f}
    
 </details>
    </details>

    


