Dưới đây là 1 số challenge mình đã giải được ở giải này, và vì server thi chỉ tồn tại đến hết giải nên mình không có hình ảnh cụ thể về 
challenge description mà chỉ có text.
Ok, bắt đầu nào:

### FORENSIC: ###
## Sky ##

**Description: where is my sky???**

Challenge này cho mình file Sky.wav. Khi nhìn thấy file có đuôi wav, cái mình nghĩ tới đầu tiên là Spectrogram của nó.

Thế là mình dùng Sonic Visualiser để mở:

![image](https://user-images.githubusercontent.com/94149390/185822665-59615b81-8563-487c-b080-1271bcf33c47.png)

Đúng như dự đoán, flag nằm trong spectrogram của file wav

![image](https://user-images.githubusercontent.com/94149390/185822683-17b04a95-d8a2-4e12-941a-b8f084874b84.png)

Và mình nhận được flag:

>Flag: GDUCTF{cl0ud5_b1rd_cl0ud5_pl4n3_cl0ud5}

## Conversation ##

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

>Flag:GDUCTF{hmmm_0nly_TCP_15_n0t_53cur3}
