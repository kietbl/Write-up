**Challenge:**

![image](https://user-images.githubusercontent.com/94149390/175799591-c89c8345-2e97-4024-8988-137886c8ccd1.png)

**Content:**

*Hacker threatens me. I dont have enough money. Help me, what is leaked by hacker?*

**Analyze:**

This challenge gives me a screen shot. What I have to do is find out what is leaked by hacker.

![image](https://user-images.githubusercontent.com/94149390/175799635-9de8f36f-246f-4d11-856e-97c1f4d7d976.png)

**Solve:**

Access to [link](http://controlc.com/52402d70) 

![image](https://user-images.githubusercontent.com/94149390/175799678-f4f5aed5-dcaf-41ee-be77-f824645270a2.png)

I see the mega link, I access to it and download file 

![image](https://user-images.githubusercontent.com/94149390/175799699-e30584b6-6e17-48ef-8fdb-b7bfcfcb8647.png)

Extract [leak_data.zip](https://github.com/kietbl/Write-up/blob/main/WhiteHat%20Play%2011/osint05-leak/leak_data.zip) I have 2 files database.zip and stylesuxx.png

![image](https://user-images.githubusercontent.com/94149390/175799793-04d5b99b-5d37-4c0a-b81b-53d134193d01.png)

Open database.zip I saw 2 files, but it seems locked

![image](https://user-images.githubusercontent.com/94149390/175799845-34c2b6d7-319c-4c16-a8cf-be2f7c7c194c.png)

I am unable to extract this file because I don't know its password.

![image](https://user-images.githubusercontent.com/94149390/175799872-6fb77024-9ae5-4d80-8825-ad744b48cf8f.png)

Then, I open stylesuxx.png 

![image](https://user-images.githubusercontent.com/94149390/175799896-9aa82b74-70b9-4cc1-9598-1f6408cc0fdb.png)

But it doesn't have anything I need. I notice its name and search that on google and found [this](https://stylesuxx.github.io/steganography/)

![image](https://user-images.githubusercontent.com/94149390/175799940-189344c4-2275-4cad-afc2-63e4059ebd64.png)

I decode stylesuxx.png and found a hidden message:

![image](https://user-images.githubusercontent.com/94149390/175799982-f5b9ea6f-2014-4311-a666-51964fabc9ba.png)

I access to https://springaz.blogspot.com/ 

![image](https://user-images.githubusercontent.com/94149390/175800029-b90f6dfd-fc64-4e37-a5c0-578ebf04f127.png)

The first comment reply leads me to https://www.youtube.com/watch?v=_S0CXWTjBas 

![image](https://user-images.githubusercontent.com/94149390/175800052-3f8d7f8b-6d80-4d5d-929a-2ab49fff2eba.png)

Its sound is morse code, so I download it = mp3 file, [link](https://ymate.app/en/youtube/_S0CXWTjBas)

I use [this tool](https://morsecode.world/international/decoder/audio-decoder-adaptive.html) to decode morse code audio file.

I get the message:

>C O N N E C T F A C E B O O K W I T H M E P O U N D K E Y S P R I N G A Z P R I V A T E C O

![image](https://user-images.githubusercontent.com/94149390/175800162-167c1f11-c96d-4446-99a5-ac89c9c4465f.png)

Connect Facebook with me #Springazprivate

I search facebook for this hashtag and found the only post:

![image](https://user-images.githubusercontent.com/94149390/175800229-11463b69-9d45-4871-8342-af20aa824d8b.png)

I go to poster's facebook :

![image](https://user-images.githubusercontent.com/94149390/175800243-a8af30f3-a259-4b35-bb4e-5644eb7cf4ee.png)

And found the database.zip's password: tyvdzl8Mpzw

I extract it and open file flag.txt to get flag

![image](https://user-images.githubusercontent.com/94149390/175800270-0823b71c-fe13-438b-9711-e74f7e9193c7.png)

>Flag: WhiteHat{https://www.youtube.com/watch?v=Flh95aLaGB8}
