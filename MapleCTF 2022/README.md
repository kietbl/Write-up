Đây là một giải khá thú vị vì có giao diện là tựa game quen thuộc với tuổi thơ nhiều người (trừ mình :v) là Maple Story
Ở giải này mình giải được 1 bài và 2 bài có ý tưởng nhưng không làm được :(( 
Thôi không dài dòng nữa, bắt đầu nào:

<details>
<summary><h1> MISC </h1> </summary> 

**Challenge: slightly bored hacker**

![image](https://user-images.githubusercontent.com/94149390/187189067-30dc9f53-2af7-4630-8ff8-9d3312d56291.png)

***Description:*** 
*Some hacker calling themselves "0x00FF00LeafHaxs" has hacked our Maple Story account and stolen all our Mesos. Upon doing some research, we've discovered that this hacker might have had a compatriot and they may have traveled together somewhere. Find me their friend's name too, in the format maple{firstname_lastname}. (If you think you have the flag for this but it isn't working, try submitting to bored-hacker)
Author: DontMindMe
NOTE: PLEASE STOP SUBMITTING ANYONE FROM THE DISCORD. OUR INTERNAL RESEARCH TEAM HAS CONFIRMED THEY ARE NOT PLAYING IN THIS CTF*

Phân tích đề:
Okay, challenge này có liên kết với challenge **bored hacker**. Hacker tên là "0x00FF00LeafHaxs" đã hack tài khoản Maple Story và ăn cắp hết Meso. Và sau khi làm một vài 
nghiên cứu thì họ đã tìm ra được hacker đấy có đồng bọn, việc của chúng ta là tìm ra tên của người bạn đó cũng như tên của hacker ở challenge **bored hacker**

Alright, dựa vào dữ kiện của đề bài là "0x00FF00LeafHaxs" mình đã tiến hành tìm kiếm nhân vật này trên các nền tảng mạng xã hội 

Okay, mình đã tìm thấy tài khoản twitter của đối tượng này

![image](https://user-images.githubusercontent.com/94149390/187191066-55565c3f-47f7-45d1-adaa-11e139b25602.png)

Lướt twitter của đối tượng mình tìm thấy bức hình này

![image](https://user-images.githubusercontent.com/94149390/187191252-977aa58f-1f05-4496-952e-7f16e22e03a8.png)

Thế là mình tìm kiếm toà nhà góc trái và ra 

![image](https://user-images.githubusercontent.com/94149390/187191429-732e0789-34ec-4d28-ab7f-d0e817537815.png)

Okay, thế là mình google "Saint Joseph's Oratory of Mount Royal" và dành thời gian tìm cái nơi mà tên này chụp hình nhưng không tìm thấy gì.

Mình thử tìm kiếm username của đối tượng trên twitter xem có gì không thì tìm thấy bài viết này:

![image](https://user-images.githubusercontent.com/94149390/187191652-98969b74-579f-4cb1-b04c-dce7a4bc9622.png)

Có vẻ như đây là bức hình đồng bọn của tên hacker chụp hắn trước khi hắn khởi hành đi Montreal

Nhìn kỹ ta có thể thấy trên bức hình có hiện tên của ai đó

Copy về paint, phóng to và rotate 

![image](https://user-images.githubusercontent.com/94149390/187191969-c3ca3c3a-7a48-4ac9-9474-0b1559ce4df4.png)

Tên trên đó là: Larry Acerabor

Mình thử submit flag tại challenge này và đó đúng là tên của đồng bọn của tên hacker (nếu không đúng thì submit ở challenge bored hacker :v)

>Flag: maple{larry_acerabor}

***Ý tưởng ở challenge ``bored hacker``***: 
Như challenge bên trên, thì challenge bored hacker yêu cầu ta tìm ra tên thật của tên hacker:

![image](https://user-images.githubusercontent.com/94149390/187192547-10310310-3a2f-4ed2-bb42-30d6215ffe6e.png)

Đọc hint ta thấy được rằng tên của tên hacker chỉ ở đâu đó trong bức ảnh:

![image](https://user-images.githubusercontent.com/94149390/187192661-e5da4d3b-6036-4b0b-8868-987c18623cca.png)

Thế là mình mở bức ảnh để xem lại và thấy mã barcode pdf417, đây là loại mã thường dùng trong vé máy bay, nó chứa thông tin trong vé

![image](https://user-images.githubusercontent.com/94149390/187192751-52ac0949-3595-43da-ad82-33c9f637ba9f.png)

Để ý thì thấy mã này bị che khá nhiều nên không dùng tool decode được mà phải giải mã bằng tay :((, do lười (với lúc đó đêm rồi để sáng đi học nữa) nên mình bỏ luôn :v. Lần sau sẽ quay lại làm.

***Ý tưởng ở challenge ``flag hoarding``***: 
Bài này có 1 bạn trong clb mình làm rồi nên mình chỉ chia sẻ ý tưởng bài này thôi :v

![image](https://user-images.githubusercontent.com/94149390/187193223-da22c6ca-dcbc-4309-afba-dbe1e04360d0.png)

Bài này thuộc dạng steganography, việc của mình là tìm flag trong bức hình

![image](https://user-images.githubusercontent.com/94149390/187193384-7c231153-d54f-4a20-b607-8e19ba9ab7b0.png)

Như một thói quen, mình dùng stegseek, steghide, stegsolve, binwalk để tìm data ẩn trong ảnh nhưng không có gì :v 

Ban đầu nhìn bức hình mình nghĩ flag ẩn trong cái đống đen đen, nhưng không, bức hình này chứa MIC aka Machine Identification Code

https://en.wikipedia.org/wiki/Machine_Identification_Code

Search trên mạng thì có tool Deda giải mã nhưng làm mãi không ra gì nên mình dùng GIMP để xử lý và một hồi thì ra

![6ed05fde-d294-40d3-8934-508f9914c662_LEAK_20220804_SCAN_1600_spotted](https://user-images.githubusercontent.com/94149390/187193910-f8ed3336-2be4-4e08-b4c5-54227db658a8.jpeg)

Mình tìm kiếm tool decode nhưng vô vọng nên định làm bằng tay nhưng lười quá nên thôi :v, bài này chủ yếu mình học kỹ thuật steganography mới, và nếu có thời gian thì mình sẽ quay lại làm lại bài này.

</details>
