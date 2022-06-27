**Challenge:**

![image](https://user-images.githubusercontent.com/94149390/175928719-b67116ff-89c3-420e-92fe-29f9c2d162f6.png)

**Content:**
*Summer is hiding in the steg*

**Solve:**

The challenge gives me [misc01-Summer.zip](https://github.com/kietbl/Write-up/blob/main/WhiteHat%20Play%2011/misc01-Summer/misc01-Summer.zip)
Extract it and I get an jpg file 

![image](https://user-images.githubusercontent.com/94149390/175929610-dabb967e-e2c5-4fed-817c-16e17ae23f25.png)

I use **Stegseek** to bruteforce an image steg's password:

Use this command: ```stegseek --seed misc01-Summer.jpg```

![image](https://user-images.githubusercontent.com/94149390/175930702-206d1ac4-dd47-4de4-b3f6-e71c02c86e03.png)

I get the file: ```misc01-Summer.jpg.out```

Open it and I get the flag:

![image](https://user-images.githubusercontent.com/94149390/175931110-94d1a669-ee51-48f3-bee3-80fb593324e0.png)

>Flag: WhiteHat{summ3r_1s_gr3at}


