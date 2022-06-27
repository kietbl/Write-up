**Challenge:**

![image](https://user-images.githubusercontent.com/94149390/175924042-bd68513d-667f-4413-a31c-7347280599ee.png)

**Content:**

*It seems that this file is corrupt. Pls fix it for me!*

**Analyze:*

Because this challenge does not hard, so I will go to solve without analyze!!

**Solve:**

The challenge gives me [for05-Corrupt.zip](https://github.com/kietbl/Write-up/blob/main/WhiteHat%20Play%2011/for05-Corrupt/for05-Corrupt.zip)

Extract it, I get a PNG file 

![image](https://user-images.githubusercontent.com/94149390/175924735-6dc5c621-fa96-4235-b2d3-eb4580537590.png)

But I can't open it.

So, I use Hex Editor to fix it. 

![image](https://user-images.githubusercontent.com/94149390/175925625-419c8387-4c8a-4cbf-9d09-b663fab21f24.png)

![image](https://user-images.githubusercontent.com/94149390/175925774-de2462b7-507a-4e0d-b917-a12899624ca0.png)

Ctrl S and I have an image. 

![image](https://user-images.githubusercontent.com/94149390/175925896-abf59891-4412-4b2e-abe1-5cb4dc9c344d.png)

It's QR code but it has been corrupted, so I use paint copy the square and paste in removed part. 

![image](https://user-images.githubusercontent.com/94149390/175926392-bfdcd772-62fe-4860-be0d-f5e1482244f1.png)

Then I have the complete image. 

I use [QR scan](https://qrcodescan.in/)

Open the image and get the flag 

![image](https://user-images.githubusercontent.com/94149390/175926642-453a1547-28f9-4988-9635-013c76c41de2.png)

>Flag: WhiteHat{4a5y_W4rmup_ch4lleng3_f0r_SUMMER_RACEEE}

