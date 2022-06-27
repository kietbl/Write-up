**Challenge:**

![image](https://user-images.githubusercontent.com/94149390/175916617-7af7202b-af9f-4e02-8a40-43439538a765.png)

**Content:**

*The information I got after investigating was that his mistress was blind. In addition, he posted on his secondary social media account a secret message to inform his 
mistress where they would be dating. So I need you to investigate further and help my client answer the following questions:
1. Date and time he posted on social media (*Format: DDMMYYYY-HH:MM:SS)
2. The coordinates of the location where they will meet (The coordinates precision is rounded to three decimal places. Format: latitude,longitude)
3. What is the secret message?

Flag Format: WhiteHat{PostingDate_Coordinates_SecretNotice}

Example:
1. PostingDate: 19:06:24 30/04/2022
2. Coordinates: 12.3452,98.7657
3. SecretNotice: S3cret-n0tic3-l00k-lik3-th1s
Flag is: WhiteHat{30042022-19:06:24_12.345,98.766_S3cret-n0tic3-l00k-lik3-th1s}*

**Analyze:**

It's a next part of series Adultery, like [previous challenge](https://github.com/kietbl/Write-up/tree/main/WhiteHat%20Play%2011/osint04-Adultery%20I), in this challenge, I will find the husband's secondary social media account and find a secret message to 
inform his mistress. What I have to do?
- Find out the date and time of his post on social media
- Find out the coordinates of a place he post in his social media account
- Find out the secret message

**Solve:**

![image](https://user-images.githubusercontent.com/94149390/175917764-c2b2a983-ff1f-4374-a12b-1c61728a5b85.png)

Analyze his image, I found "Brian Cuming|Linkedin" tab.
So, I search for this name on Linkedin and found his secondary account.

![image](https://user-images.githubusercontent.com/94149390/175918158-56fb8027-074c-481e-967d-b1404a912ca9.png)

Open the first post, I found the place but I don't know where is it,

![image](https://user-images.githubusercontent.com/94149390/175918227-106896ee-d4ff-4496-9936-c4f8ca37d02b.png)

So, I cut the tower in an image 

![image](https://user-images.githubusercontent.com/94149390/175918512-7087ed6d-2490-4848-920b-b16c5537c9f1.png)

using images.google.com to search it and I found its name is "Berliner Fernsehturm"

![image](https://user-images.githubusercontent.com/94149390/175918799-f16699df-8656-466d-be73-baf4a07fc0e2.png)

I find any grey building near it 

![image](https://user-images.githubusercontent.com/94149390/175919092-69b3ce13-f22f-4d90-8760-b80dfe3d03db.png)

And I found this one. 

![image](https://user-images.githubusercontent.com/94149390/175919364-7395ae63-bafe-47d3-a20c-13310726ae52.png)

Its location is left-side the "Berliner Fernsehturm" so, I use "street view image" mode. 
Oh, it's the same the image.

![image](https://user-images.githubusercontent.com/94149390/175919994-e1fbde84-5f2e-4c10-b615-7de3a4f6cb10.png)
![image](https://user-images.githubusercontent.com/94149390/175920139-656ce13a-1de0-4452-839d-7eb690a36ee8.png)

So I go backwards and found the place

![image](https://user-images.githubusercontent.com/94149390/175920562-5ff7801c-6001-410e-a2fc-cbe7dde71a88.png)

Its coordinates is "52.5183741,13.4134415"

I go back to the post, copy its link and paste into [this tool](https://ollie-boyd.github.io/Linkedin-post-timestamp-extractor/)

I found out it updated on Sun, 17 Apr 2022 17:01:10 GMT (UTC).

Okay, the last is secret message, how can I found it?

I inspect the image and found alt text: Summ3r-m34ns-p3rfect-blue-sk1e5

![image](https://user-images.githubusercontent.com/94149390/175921355-f6475618-c326-48bd-a30b-6d2f71125df9.png)

If you want to know what is alt text, press [here](https://www.google.com/search?q=alt+text&oq=alt+text+&aqs=edge..69i57j0i512l8.3150j0j1&sourceid=chrome&ie=UTF-8)

Okay, we have enough essential datum, and the flag is:
>Flag: WhiteHat{17042022-17:01:10_52.518,13.413_Summ3r-m34ns-p3rfect-blue-sk1e5}


