This chall gimme 2 files, [chal.py](https://github.com/saraka1412/Write-up/blob/main/HCMUS-CTF%202022/Substitution/chal.py) and [msg_enc.txt](https://github.com/saraka1412/Write-up/blob/main/HCMUS-CTF%202022/Substitution/msg_enc.txt)

First, I opened msg_enc.txt, and I thought I didn't need to open chal.py because I could use an online tool like [this](https://www.guballa.de/substitution-solver) to decode this.
 

So, I use [this tool](https://www.guballa.de/substitution-solver) and paste msg_enc.txt content 

```
HO IFSBZXUFABDS, A QCVQZHZCZHXO IHBDKF HQ A GKZDXW XR KOIFSBZHOU HO JDHID COHZQ XR BNAHOZKYZ AFK FKBNAIKW JHZD ZDK IHBDKFZKYZ, HO A WKRHOKW GAOOKF, JHZD ZDK DKNB XR A PKS; ZDK "COHZQ" GAS VK QHOUNK NKZZKFQ (ZDK GXQZ IXGGXO), BAHFQ XR NKZZKFQ, ZFHBNKZQ XR NKZZKFQ, GHYZCFKQ XR ZDK AVXMK, AOW QX RXFZD. ZDK FKIKHMKF WKIHBDKFQ ZDK ZKYZ VS BKFRXFGHOU ZDK HOMKFQK QCVQZHZCZHXO BFXIKQQ ZX KYZFAIZ ZDK XFHUHOAN GKQQAUK. DKFK HQ ZDK RNAU: DIGCQ-IZR{ODAONCO_NHPKQ_ZX_BNAS_IFSBZXUFAG}
```

![image](https://user-images.githubusercontent.com/94149390/168524657-e3e35d2a-c510-4573-ab3c-0c2c5343a3c3.png)

Then, I get flag 
>*HCMUS-CTF{NHANLUN_LIKES_TO_PLAY_CRYPTOGRAM}*
