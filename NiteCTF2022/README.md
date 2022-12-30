# NiteCTF 2022 writeup
## Misc
### Boys

![](https://i.imgur.com/kqZ5ZgY.png)

Challenge gives us a Github user name sk1nnywh1t3k1d 

![](https://i.imgur.com/WQvJirg.png)

Overview I see this user has a project in the repositories, its name is chat-app. And it has nothing special

![](https://i.imgur.com/xjDSHmz.png)

After spending a while looking for sure, I click commit hash, then add ".patch" to the end of the url bar.
I found this way from https://www.nymeria.io/blog/how-to-manually-find-email-addresses-for-github-users

![](https://i.imgur.com/ZcS3Uad.png)


![](https://i.imgur.com/QJZX4nz.png)

And I can see Github user's email

![](https://i.imgur.com/r4MtIAs.png)

By using epieos tool, I can retrieve information linked to this email. 
Other useful tools you can try if epieos does not have information you need are Ghunt and Holehe.

![](https://i.imgur.com/OKBOS5K.png)

I accessed google calendar and found the flag

![](https://i.imgur.com/Q4GUdAr.png)

```flag: niteCTF{v0ught_n33ds_t0_g0_d0wn}```

### Travel

![](https://i.imgur.com/YfnTesR.png)

This challenge gives us an image file.

![](https://i.imgur.com/vjTg8or.jpg)

Out of habit, I use https://www.aperisolve.com/ to analyze this image.

I found the link in Exiftool

![](https://i.imgur.com/zu7zIzm.png)

Then I accessed the link, it leads me to google jamboard

![](https://i.imgur.com/eEJwsJ6.png)

But I can only read it, so I made a copy of it to modify this file

![](https://i.imgur.com/7QmpHSx.png)

I pulled out the first box and saw a link

![](https://i.imgur.com/9tsho9L.png)

I accessed to this link and saw nothing special

![](https://i.imgur.com/cj8QMHO.png)

I viewed its source, then I saw a comment encoded base64, decoded it and I received string "flag.txt". Keep reading source, I saw the comment ``<!-- <p> <b>Go Back in time and get the previous month's menu!!!</b></p> -->``

![](https://i.imgur.com/LesA5ys.png)

"go back in time", it looks like "Travel back in time for me please" in the description. And the first thing I thought is "Way back machine"

![](https://i.imgur.com/ltCWzpB.png)

Go to URLs tab and see urls that being captured for https://tr4v3l1.netlify.app/

![](https://i.imgur.com/n8v00mJ.png)

Clicked https://tr4v3l1.netlify.app/flag.txt, then found the flag

![](https://i.imgur.com/UOdtAza.png)

``flag: nitectf{y0u_w3nt_b4ck_1n_t1m3}``

## Forensics
### Wonka-Bar

![](https://i.imgur.com/t5gVIIb.png)

This challenge gives us a pdf file.

The first file I downloaded is different from the current file. 

![](https://i.imgur.com/5xQSZnW.png)

I did not realise it until I stuck and sought Discord for hints.

So, first, I will go through the first file, and then I will go through the current file.

The``CandyStore.pdf`` is protected by password. So I bruteforce by rockyou.txt wordlist to find its password.

![](https://i.imgur.com/9vzcup3.png)

Firstly I used ``hashcat`` and ``john``, but it does not work :), I think I used it the wrong way because other people use them normally.

So I used another tool, its name ``pdfcrack``
https://www.kali.org/tools/pdfcrack/

I did following command ``pdfcrack -f CandyStore.pdf -w rockyou.txt`` and waited.

```
$ pdfcrack -f CandyStore.pdf -w rockyou.txt
PDF version 1.4
Security Handler: Standard
V: 2
R: 3
P: -4
Length: 128
Encrypted Metadata: True
FileID: b16bf5c8a66dac3e924ff5753045861a
U: 6f9bfed42e7c2bf34d93820c2727371028bf4e5e4e758a4164004e56fffa0108
O: 7f444aa3498f9f55c4b118120c8a2e3ae7b4666b6a90514b09285bfaee7a8703
Average Speed: 38346.3 w/s. Current Word: 'total123'
Average Speed: 39363.3 w/s. Current Word: 'lowkill94'
Average Speed: 38104.3 w/s. Current Word: '23607965'
Average Speed: 38856.4 w/s. Current Word: 'trazia'
Average Speed: 37222.7 w/s. Current Word: 'shannon219'
Average Speed: 37599.6 w/s. Current Word: 'poloko_117'
Average Speed: 38023.2 w/s. Current Word: 'mrho71'
Average Speed: 37319.4 w/s. Current Word: 'longwild'
Average Speed: 36971.6 w/s. Current Word: 'juggaloninja'
Average Speed: 37242.3 w/s. Current Word: 'hml880724'
Average Speed: 38856.9 w/s. Current Word: 'ellenrb10'
Average Speed: 38641.4 w/s. Current Word: 'chimchuck'
Average Speed: 38287.2 w/s. Current Word: 'axeltheboy'
Average Speed: 38185.9 w/s. Current Word: 'SALWANI'
Average Speed: 37943.2 w/s. Current Word: 'Apple9876'
Average Speed: 37586.3 w/s. Current Word: '5542090820'
Average Speed: 39081.1 w/s. Current Word: '2014196'
found user-password: '13euro'
```

Okay, its password is ``13euro``. Using this password, I opened the pdf file

![content](https://i.imgur.com/YPXpxAu.png)

It has a link and ciphertext. Click the link and it lead me to the Meganz link

![](https://i.imgur.com/Rw76TKu.png)

I downloaded this zip file but couldn't open it because of the password.

![](https://i.imgur.com/hpzTXfu.png)

Firstly I did not think the ciphertext after being encrypted is the zip file's password :). So I used Zydra (https://github.com/hamedA2/Zydra) to crack it.

```
Oh, after reading README.md from Zydra github, I have just known it can be used to crack pdf, okay, I will try it next time.
```
Using the rockyou wordlist again.
``Zydra.py -f <file> -d rockyou.txt``

It took me a long time

![](https://i.imgur.com/Jr21O3X.png)

And error
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
                ####### #     # ######  ######     #
                     #   #   #  #     # #     #   # #
                    #     # #   #     # #     #  #   #
                   #       #    #     # ######  #     #
                  #        #    #     # #   #   #######
                 #         #    #     # #    #  #     #
                #######    #    ######  #     # #     #

                Author : Hamed Hosseini
        @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@

Start time ==> Mon Dec 26 19:53:58 2022

Starting password cracking for /mnt/c/Users/kietbui/tool/Zydra/wonka-bar-golden-ticket.zip /

 [*] Count of possible passwords: 14344391
        Progress : [#                                         ] 3.907 %Traceback (most recent call last):
  File "Zydra.py", line 680, in <module>
        Progress : [#                                         ] 3.907 %    cracker.main()
  File "Zydra.py", line 638, in main
        Progress : [#                                         ] 3.907 %    self.dict_guess_password(dictfile, file)
  File "Zydra.py", line 359, in dict_guess_password
        Progress : [#                                         ] 3.907 %    for word in wordlist:
  File "/usr/lib/python3.8/codecs.py", line 322, in decode
    (result, consumed) = self._buffer_decode(data, self.errors, final)
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xf1 in position 923: invalid continuation byte
        Progress : [#                                         ] 4.183 %
```

I thought it's corrupted. So I sought Discord for any support or hints.
And as I mentioned above, they replaced new file.
So I downloaded new file and used the password I found to open it.

Its content has a little change.
The link is different and they emphasize "chance".

![](https://i.imgur.com/ccOnEFi.png)

The link also leads me to Meganz.

![](https://i.imgur.com/S6mtNS2.png)

Back to CandyStore.pdf, as you can see, the Ciphertext and the word "chance" have been emphasized. Ciphertext and key, what type of cipher do you think?
``It's Vigenere cipher.``
https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher

After decrypting the ciphertext with the key, I receive the plain text, and it is the zip password (I also use this password with the old zip file above, and it works).

![](https://i.imgur.com/0r8rDN5.png)

After extracting i get a folder.

![](https://i.imgur.com/N5XbRpm.png)

I open obj file, it's 3D object.

![](https://i.imgur.com/ZwcAEr2.png)

After a while analyze it, I discovered a ticket with the link inside

![](https://i.imgur.com/COppqXB.png)

The link is http://bit.ly/g01d3nt1ck3t, and it leads me to the Meganz again.

![](https://i.imgur.com/i2NXetK.jpg)

Downloaded it and put it into the Aperi'Solve tool to analyse.
Using exiftool, I saw in the comment that is the cipher text. 

![](https://i.imgur.com/9Fp5Ota.png)

I solved the Quadratic equation and x = 13 and x = 5. Then, I try to identify what kind of this cipher and https://www.dcode.fr/cipher-identifier is quite useful (sometimes it sucks :) )

![](https://i.imgur.com/BCxOCAz.png)

I try Keyboard Shift Cipher but it does not work, then I try ASCII Shift Cipher and found the flag. 

![](https://i.imgur.com/KoTp9sr.png)

And this time, I realise what x = 5 means, I just need to subtract 5 ASCII values from each character of the ciphertext to get the original string.

![](https://i.imgur.com/LTXogRq.png)

I removed ``&quot;``, it is HTML Entities then I got the flag.

![](https://i.imgur.com/Rl42RCk.png)

``flag: niteCTF{3arth_says_h3ll0}``





