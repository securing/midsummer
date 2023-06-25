# SFI18_CTF

The following is a comprehensive (I hope) writeup of **sfi18_ctf**, based on personal experience and challenges. 

## Table of Contents
- [hello_world](#hello_world)<br/>
- [cryptic_website](#cryptic_website)<br/>
- [crawlers](#crawlers)<br/>
- [la_bouche](#la_bouche)<br/>
- [vpong_game](#vpong_game)<br/>
- [wild_west](#wild_west)<br/>
- [images](#images)<br/>
- [post_office](#post_office)<br/>
- [unknown_file](#unknown_file)<br/>
- [photo](#photo)<br/>
- [lost_bits](#lost_bits)<br/>
- [camera_model](#camera_model)<br/>
- [company_website](#company_website) (incomplete)<br/>
- [image_resizer](#image_resizer) (no solution)<br/>
- [bad_random](#bad_random)<br/>
- [nucleotides](#nucleotides) (no solution)<br/>
- [binary_code](#binary_code)<br/>
- [memory](#memory)

## Challenges

![Challenges](./media/challenges.png)

## hello_world
> In the bustling city of Krakow, you were out exploring the Old Town when stumbled upon a strange vehicle parked behind a building. It was unlike anything you have had ever seen before. The car was sleek and modern, with a metallic sheen that glinted in the sun.
As you got closer, you noticed something even more peculiar. The car was covered in strange markings and symbols, as if it had been through some kind of intense journey. When you reached out to touch the vehicle, a sudden electric shock shoot up your arm.
That sounds ridiculous but it looks like the car from Back to the future. Maybe inside it you will find your answer.
To open the door you have to input the code:

> You’ve got a message!<br/>
> The flag is:
>
> sfi18_ctf{hello_world!}

<br/>

<details>
<summary>Solution</summary>

If you really need detailed guide through this challenge you should reconsider if events like this are your cup of tea
</details>

## cryptic_website

> In the car, you found a note written in some weird letters. Maybe it is from the creator of this machine?
You will probably find some instructions on how to start the machine...

![Cryptic message](./media/cryptic_website_1.png)

> [cryptic link](https://fontlibrary.org/en/font/samys-keys-n-keys)

<br/>

<details>
<summary>Solution</summary>

Clicking the link brings us to a font file, in which all the characters have been replaced by key symbols. Challenge is obvious - deobfuscate the cryptic text to get the flag.

The most straight-forward way hovewer is to simply copy the cryptic text and paste it somewhere else (since copying stores the plaintext and doesn't care about fontfaces), yielding us the flag:

> sfi18_ctf{HelloThere!}
</details>

## crawlers

> “Dear time traveler,<br/>
You HAVE TO destroy the time machine! It is dangerous for DeLorean to exist. Don’t try to find me in the past, or future, or any other dimension. As a scientist, I tried to explore time and space, but it only led to dangerous paradoxes. The future is not meant for people just yet. Please, don't let anything stop you. There may be some people or robots who would love to take over this technology.
Doc Emmet Brown”
>
> Try to find what robots Doc was talking about! Maybe they have a message for you that will clarify this further.

<br/>

<details>
<summary>Solution</summary>

Challenge is called 'crawlers' and we are heavily hinted towards 'robots' - /robots.txt is the file we're looking for. Upon inspecting the file we get our flag, nice and simple:

> sfi18_ctf{LQbvJJc1Ulj8}
</details>

## la_bouche

> “Dear time traveller,<br/>
if you found this car it means something went wrong. It is not an easy quest and information you will need are confidential, it is not a job for some pirate(or is it?)! If you will solve the riddle, this machine will take you to the timeline where serious problems occurred.<br/>
Doc Emmet Brown”

![Attached image](./files/cipher.png)

<br/>

<details>
<summary>Solution</summary>

Challenge is called 'la_bouche' and we're given a cipher - quick Google search brings us to [Olivier Levasseur](https://en.wikipedia.org/wiki/Olivier_Levasseur) (nicknamed "La Bouche") who inveted his own alphabet, looking exactly the same as our ciphertext. Deciphering the text gives us the following string:

> QUAVOUSSERERLA

Don't be deceived by French-looking words in this string (considering Olivier was French), they sadly have nothing to do with the puzzle - the flag turns out to be simply:

> sfi18_ctf{QUAVOUSSERERLA}

(would be really funny if someone wasted a lot of time persuing this lead :) )
</details>

## vpong_game

> Timeline: 1985<br/>
> You have travelled back to the year 1985, but now the engine starts to smoke purple haze. You try asking some teenagers with skateboards if they can help you find someone. They are willing to help, but want something in exchange. Right now, their biggest problem is a game they are unable to win - they have been playing it forever.
>
> [download game file](./files/V-Pong.exe)
>
> Sha256 checksum of your downloaded file: 69E270BD8DB221E4F9AFBF6AB4085343F1747C62C63DD34F7FAF12BFCA947823

<br/>

<details>
<summary>Solution</summary>

Provided file is a simple PONG game with one twist - AI is made in such a way that it will always hit the ball with the pad. I guess you get the flag by winning, so how can we achieve that?

Let's pass the executable file through `strings` utility - maybe they left is as a plaintext:

```bash
strings V-PONG.exe | grep "sfi18_ctf"
```

Well, would you look at that!

> sfi18_ctf{LetMe(W)In}
</details>

## wild_west

> Timeline: 1855<br/>
> You thought it was the year where Doc Brown might be found, and maybe you could persuade him not to destroy the time machine. Now it's just a matter of how to find him. Not sure how to locate your scientist, you wandered to a local school where Miss Clara was teaching. She agreed to talk with you after her lessons were over, so now you might want to learn something about geography... but who the hell is adding states to each other?

> Hints:<br/>
> The value of every variable is unique.<br/>
> The flag is uppercase and sorted in ascending order.

![Oregon = Kansas + Ohio ???](./files/map.svg)

<br/>

<details>
<summary>Solution</summary>

Based on the last sentence and given hints we have an equation to solve. We can contruct it using the provided image:

      KANSAS
    +   OHIO
    --------
      OREGON

Solving for each letter, then putting them in ascending order (according to their solved value) gives us the string and the flag:

> RGSEKOINHA
>
> sfi18_ctf{RGSEKOINHA}
</details>

## images

> Timeline: 2015<br/>
You think you went back to the past but why there are flaying cars? Maybe you hoped to another timeline by accident? They still got internet though, this is not what you used to see in our phones, it has weird platforms like instacamera, FindBesties (fb), shoutter and there is one ad, It looks like this organization has to have a very overgrown ego to put two logos on one page.

![SFI logo A](./files/a.png)
![SFI logo B](./files/b.png)

<br/>

<details>
<summary>Solution</summary>

Two "similar" looking images - looks like it's stegonography time!

After a long time spent with various tools and websites one of them finally allowed me to crack the puzzle - [stegonline](https://stegonline.georgeom.net/upload)

All you need to do is to upload the `b.png` image, go into `Extract Files/Data`, select all LSB and voila; the flag is here:

> SHOULD_Y  OU_SEE_M  E?......

Remove spaces and dots, wrap it in the usual flag format and you get

> sfi18_ctf{SHOULD_YOU_SEE_ME?}

I have no idea whether `a.png` is somehow useful for this challenge
</details>

## post_office

> On the envelope you have noticed an address, but not just city and street, there is also the exact delivery time. Which company delivers with such accuracy? There is a website address of this POSToffice on the back, GETting to it might be hard... but what about sending a different REQUEST?
>
> [POST Office website](https://ctf.sfi.pl/infra/post_office/index.php)

<br/>

<details>
<summary>Solution</summary>

Navigating to the website we're greeted by some generic template filled with filler information. Every single button on the page, every single link displays a message that it's "currently out of order" and there's nothing hidden in the source code.

Challenge name, as well as the description hint heavily towards different HTTP request types, so let's see what happens when we send one that's not GET or POST:

```HTML
(...)

<!--
login: admin
password: sfisfisfi
-->

(...)
```

We got admin credentials, let's try sending them with the good ol' POST request

> sfi18_ctf{idonthavetheflagandwebsiteisdonwwillfixinthefuture}
</details>

## unknown_file

> Timeline: 1985<br/>
After asking them about Marty McFly (he might know how to fix your vehicle), it turns out that one of your new friends is Marty's brother. As you walk together to McFly's house, he tells you about his father's problem with new technology at work. You have with you a piece of computer technology from the future that might help with that. Unfortunately, no software existing at those times could understand that format. You will have to work on that on your own.
>
> [download file](./files/unknown_file)
>
> Hint: the flag is all lowercase.

<br/>

<details>
<summary>Solution</summary>

First of all, since the file doesn't have an extension, let's see what `file` utility thinks it is:

```
[spookyless ~]$ file unknown_file
unknown_file: data
```

File seems corrupted - let's open in in hex editor and maybe we can extract something useful out of it

![PNG chunks 1](./media/unknown_file_1.png)
![PNG chunks 2](./media/unknown_file_2.png)

Looking at the highlighed chunks we can take a guess that the file is just a corrupted PNG image - let's fix it then!

We can start by making sure the file has a proper header - according to [PNG specification](http://www.libpng.org/pub/png/spec/1.2/PNG-Structure.html), first 8 bytes are always the same, followed by IHDR header

Adjusting the first 8 bytes is straigh forward - just replace the values with correct ones

![Magic bytes](./media/unknown_file_3.png)

IHDR header is a bit more complicated though. First 4 bytes contain chunk data length (which for IHDR is always 13), next 4 bytes are header name (literally IHDR), next `length` (in this case, 13) bytes are chunk data and finally, last 4 are reserved for CRC.

For chunk data, we mostly care about width and height - since we don't know the amount of pixels in the image, let's put in really big numbers for now, we can always adjust it later

![Magic bytes](./media/unknown_file_4.png)

Great! Now the image has a proper header, let's try opening it

> Erorr: Out of memory

Oh, looks like the dimensions we put in are a bit too much for casual software to handle. Since we can't easily know the dimensions of the image, let's put in into another online tool - [Compress or Die PNG repair](https://compress-or-die.com/repair)

![The flag](./media/unknown_file_5.png)

And here's our flag:

> sfi18_ctf{corrupted_89504e47}
</details>

## photo

> Timeline: 1855<br/>
Clara took you to her and Doc’s house, where he was working on another science project of his. Doc Brown was really happy to see you’ve managed to find him. Despite his previous intention now the plan is to make time travels better. He needs you to find some old code of his, but all you have is photography…
>
> Hint: the response is all lowercase with no spaces.

![street view photo](./files/photo.jpg)

<br/>

<details>
<summary>Solution</summary>

So we have a street view photo with an arrow pointing to some random house - let's find the exact location from which this photo was taken, maybe this will give us more clues.

Looking at the yellow banner on the left, we can extract a phone number; quick google search reveals the [location](https://www.google.com/maps/@50.0963612,19.8418705,3a,75y,292.39h,83.3t/data=!3m6!1e1!3m4!1s3QmgVB8Xkr4EECnBPTOnMQ!2e0!7i16384!8i8192).

Arrow was pointing out at the address plate, which turns out is our flag:

> sfi18_ctf{krakowska50}
</details>

## lost_bits

> Timeline: 2015<br/>
Weird marketing idea, you thought to yourself. When all of the sudden men looking like Emmet Brown, creator of your vehicle approached you. After being accused of robbing local bank he had to hide so went to this year and waited for Marty to find him but you showed up first. He can prove that car is his, in the car there is device sending encoded message in case DeLorean and its passengers were about to meet DeLorean from different time. To prove if that’s him try to encode this:
> 
> 100<u>0</u>1<u>1</u><u>0</u> 111<u>1</u>1<u>0</u><u>0</u> 101<u>1</u>0<u>1</u><u>0</u><br/>
> 111<u>0</u>1<u>1</u><u>0</u> 001<u>0</u>1<u>0</u><u>1</u> 111<u>1</u>0<u>0</u><u>1</u><br/>
> 111<u>1</u>0<u>1</u><u>0</u> 101<u>1</u>1<u>1</u><u>1</u> 010<u>0</u>0<u>0</u><u>0</u><br/>
> 001<u>0</u>0<u>0</u><u>0</u> 000<u>0</u>1<u>0</u><u>0</u> 010<u>0</u>1<u>1</u><u>1</u><br/>
>
> This message had to travel a lot of space.
> Let's hope the sender used some redundancy to prevent transmission errors.

<br/>

<details>
<summary>Solution</summary>

We're hinted towards redundancy error detection and by looking at those marked bits, we can deduce we're going to need [Huffman Coding](https://en.wikipedia.org/wiki/Huffman_coding) here (more specifically, huffman(7,4) - for each 7 bits in a message, we have 4 bits of actual data and `7 - 4 = 3` for error correction)

Most of the online tools however use left-most bits for redundancy, whereas here we need to use the right ones. Solution is simple - reverse each string, put it in the tool and reverse the answer. This yields the following binary strings:

```
1101 1110 1010
1101 1011 1110
1110 1111 0000
0000 0000 0001
```

Encoding them into hex values yields us the flag:

> deadbeef0001
>
> sfi18_ctf{deadbeef0001}
</details>

## camera_model

> Timeline: 1985 (1955)<br/>
You landed in Hill Valley as Doc and Marty were testing the DeLorean for the first time. After Einstein hopped out of the other machine, your vehicle caused a second blast. As you got out of your car and try explaining the situation Doc Brawn said:
“In few minutes we will be in great danger! You have to prevent this, Marty knows what to do, go now!”
>
> He gives you the camera and tell you to view the recordings on the way...
>
> [download me](./files/downloader.zip)
>
> Hint: We meant the Product Name, not the Model Number. Also, do not use spaces.

<br/>

<details>
<summary>Solution</summary>

Extracting the archive we can see another archive, `deep.zip`, this time encrypted, a picture and a note reading

> Pass: the model of the phone that took the picture

Because of the attached image it becomes pretty clear we have to extract the product name (look at hint, they made a mistake in the note) from EXIT ancillary data of the photo.

Passing the photo into one of many tools available online yields us the answer:

> Make: samsung<br/>
> Model: SM-S901B

Quick Google search reveals that the phone is in fact called `Samsung Galaxy S22 SM-S901B`. After fiddling with what works and what doesn't, password for the `deep.zip` archive turns out to be

> SamsungS22

Great! We have another encrypted archive, `deeper.zip` (this time containing the flag) and another note which reads

> Pass: 440a2432cc29c4838d5574b0a38061ebcaa63f78
>
> Buuuut, black -> emerald

Password is exactly 40 characters long, and since there are only 0-f characters included we can guess it's a SHA-1 hash - whatever was the input will be our next clue.

Reversing a hashing function like this would take thousands of years, thankfully this hash is already "well-known": [link](https://hashtoolkit.com/decrypt-hash/?hash=440a2432cc29c4838d5574b0a38061ebcaa63f78)

Using the second part of the note, we replace `black` with `emerald` to get the password for `deeper.zip`:

> emerald_pearl261981

Extracting the final archive gives us the flag:

> sfi18_ctf{C0ngR4tUl4t10n5}
</details>

## company_website

> Timeline: 1985<br/>
Marty will be able to help you with the time machine but he has as assignment due tomorrow, it is about this new technology called “internet” - “you are from the future after all maybe you could tell how it will develop”<br/>
Show him how internet looks like and how source code for websites work, in just few years HTTP and PHP will be created.

https://ctf.sfi.pl/infra/company_website/

> Note: All web-related tasks are hosted under URLs in the following format: "https://ctf.sfi.pl/infra/{task_name}/". With an exception for crawlers and company_website - a part of this task is hosted under a different domain and URL than ctf.sfi.pl. The only **publicly resolvable** domain that hosts infrastructre for all SFI CTF tasks is ctf.sfi.pl.

<br/>

<details>
<summary>Solution (incomplete)</summary>

Navigating to the provided website, all we can see in the source code of the webpage is

```HTML
<!-- Reminder for designers: cms.internal/admin for CMS login. Don't forget about our internal VPN!!! -->
```

After spending two days trying all the URLs a note was added to the original challenge - with emphasis on publicly resolvable, that got me thinking.

I noticed that there are two webpages hosted on the same IP address but under different domain names: `eowyn.sfi.pl` and `ctf.sfi.pl`

```
> nslookup ctf.sfi.pl
Addresses: 149.156.65.199

> nslookup eowyn.sfi.pl
Addresses: 149.156.65.199
```

What if, by any chance, this server returned another website if we enter `cms.internal` as the URL?

I edited `/etc/hosts` file in such a way that will allow me to connect to the above server using `cms.internal` as URL

```shell
# /etc/hosts

149.156.65.199 cms.internal
```

And voila! We got a different website, this time a login form - no matter what we enter though we're greeted with error 403, reading

> Forbidden! You are not an admin!

Well, my guess is we somehow need to get access to admin's credentials to get the flag.

I noticed one more thing - every time you connected to this website it was giving you a cookie:

> internal_cms_session=eyJ1c2VybmFtZSI6ImFub255bW91cyJ9.Z02zZw.jmRRtPMNNWFWVW1PpU8C-8Plh6-NeJrCH9vAviT_j4s

Cookie looks like a JWT token, albeit wrongly formatted - we can decode it though:

```shell
# header
{ "username": "anonymous" }

# payload
gM³g
```

Yeee, it's questionable - all I could notice is that the payload changed every second - turns out it was simply `timestamp in seconds * 16`.

<br/>
<br/>

*This is where I left this challenge*
</details>

## image_resizer

> Timeline: 1985
Your hacking skills may come in useful. Marty said he could help you but has an assignment due tomorrow. It is about some photos. As you look closely at his software, you notice something strange - it should be used for resizing images, but it's not working as intended.
>
> https://ctf.sfi.pl/infra/image_resizer/

<br/>

No solution this time, I couldn't tackle this challenge in any way.

## bad_random

> Timeline: 1855<br/>
You found what you needed and went back to Doc's. When you were gone, he was almost able to finish his new time travel vehicle using pluton from DeLorean, but there's a problem with some old software secrets that he doesn't remember. If you want to go back, you need to help him break the code to get access to some of the vehicle's modules.
>
> You have in front of you a set of pairs of documents. They all contain the same secret. We are so sure you can't break the algorithm that we decided to give you multiple trials and encrypted them 5 different times...randomly.
>
> [Click here](./files/secrets.7z) in order to download files.
> Sha256 checksum of your downloaded file: 247B646E1C890ABE54592EDE10926C7D8610FE221C7B15C820298DCDBD5D4BD4

> Hint:<br/>
> Doc was able to remember some of the details of the code that generated the secret files. One specific fragment looked something like this:<br/>
> ```Python
> ...
> ciphertext = public_key.encrypt(
>  secret,
>  padding.OAEP(
>    mgf=padding.MGF1(algorithm=hashes.SHA256()),
>    algorithm=hashes.SHA256(),
>    label=None
>  )
> )
> ...
> ```

<br/>

<details>
<summary>Solution</summary>

*Oh boi, cryptography challenge, here we go*

We are provided five pairs of files - one being a PEM certificate, other one being a ciphertext encrypted with a private key, certificate containing the associated public key.

Honestly, I had no idea what to do with this challenge - all I knew was to exploit the RNG used to enrypt those secrets.

I used [RsaCtfTool](https://github.com/RsaCtfTool/RsaCtfTool) to do all the heavy lifting for me. Using common factor attack on multiple public keys we got two private keys in a fraction of a second:

```shell
> ./RsaCtfTool.py --publickey "*.pem" --private

-----BEGIN RSA PRIVATE KEY-----
...
```

Those keys correspond to pair `1` and `4` - we can now reverse the encryption of those secrets. I had no luck with `openssl` utility, so this is where the provided hint with Python code comes in - all you need is a simple script to get the contents of the file:

```Python
from cryptography.hazmat.primitives import serialization, hashes
from cryptography.hazmat.primitives.asymmetric import padding

key = serialization.load_pem_private_key(open("./4.key", "rb").read(), None)
ciphertext = open("./secret4", "rb").read()

decoded = key.decrypt(
    ciphertext,
    padding.OAEP(
        mgf=padding.MGF1(algorithm=hashes.SHA256()),
        algorithm=hashes.SHA256(),
        label=None
    )
)

print(decoded)
```

```shell
> ./enc.py

b'sfi18_ctf{BadRandGenerat0r}'
```

And that's the flag!

> sfi18_ctf{BadRandGenerat0r}
</details>

## nucleotides

> Timeline: 2015<br/>
It is exactly as he said, now he needs you to check this kinda messy DNA sequence for him to prove his innocence before court, then he will help you came back to your year and will reward you for your help.<br/>
You looked at that part of DNA code, but something is wrong.<br/>
It looks messy.
>
> CCACGCCGACCACGCCCCCCTGACAACCACCTATACCCCTTCCTATCCGAGGCT$ATTCTCTGTCCCACGC

<br/>

> [Run script] executes the following function:
> ```javascript
> function transform() {
>     let word = document.getElementById('transform_input').value + "$";
>     let shifts = [];
>     for (let i = 0; i < word.length; ++i) {
>         shifts.push(word.substring(i) + word.substring(0, i));
>     }
>     shifts.sort();
>     document.getElementById("transform_output").innerText = shifts.map(x => x.at(-1)).join('');
> }
> ```

<br/>

No solution this time, although I came up with few ideas, in the end I needed to check `4^35 * 3^13 * 2^9 * 1^9` possibilities and couldn't optimize it further.

## binary_code

> Timeline: 1985 (1955)<br/>
Now that you are in 1955, you should be careful not to meet other versions of yourself. Doc said it may cause some trouble with time and space, so you just have to be really careful. You and Marty now have only one job: to write the letter and give it to Doc to open a few days before the accident in 1985. However, Emmett Brown doesn't trust you. He thinks that if you are able to time travel, somebody must have already solved this problem. You have to solve it to gain his trust.
>
> [download binary code](./files/Binary%20Code.pdf)

<br/>

<details>
<summary>Solution</summary>

Taking look at the document it's clear we have an algorithm to execute - let's first decrypt the binary instuctions:

```
 1 | LOAD @ 12
 2 | STORE @ 14
 3 | LOAD @ 13
 4 | SUB $ 1
 5 | JZERO $ 11
 6 | STORE @ 13
 7 | LOAD @ 14
 8 | MULT @ 12
 9 | STORE @ 14
10 | JUMP $ 3
11 | END $ 0
12 | 5
13 | 3
14 | 0
```

We can see "adresses" 1 - 11 containing instructions, while 12 - 14 contain values we will operate on. Let's quickly go through what each instruction does:

```
LOAD @ %1 - loads value from address %1 into accumulator
STORE @ %1 - stores value from the accumulator into address %1

SUB $ %1 - substracts literal value %1 from the accumulator
MULT @ %1 - multiply accumulator by value under address %1

JUMP $ %1 - jump to address %1
JZERO $ %1 - jump to address %1 if accumulator is zero

END $ 0 - end the program
```

Executing the program yields us the answer (value in address 14), thus the flag:

> 125
> 
> sfi18_ctf{125}
</details>

## memory

> Timeline: 1985 (1955)<br/>
Now everything makes sense! - Doc is really happy with the answer, “- now go back to the future!”<br/>
As you and Marty were going to your vehicle things go wrong. there happenedto be two Marty’s in the same time and place, they looked at each other and disappear in sudden blast. In their place something new is forming.<br/>
You are looking at this weird thing, It seems to be alive, but why?<br/>
Maybe because it looks like it want to say you something important, but it can't.<br/>
Indeed, it doesn't have a mouth.

![Graph](./files/network_white.svg)

> Maybe when you inspect it deeply, you will find out what it has to say. [download memory](./files/weights.npy)

<br/>

<details>
<summary>Solution</summary>

The "memory" you can download turns out to be a 32x32 numpy array of 0s and 1s - we can visualize it like this:

![memory visualized](./media/memory_1.png)

where white represents a 1 and black - a 0.

At this point I had some ideas what those `weights` could be referring to:
- it might be an adjacency matrix for a graph, since it's diagonally symmetrical - but why name it `weights` then? And only 11 vertices out of 32 would be connected somewhere.
- it might be part of a layer of some neural network - even if we assume that all the biases are 0, we don't know the output one and its respective weights/biases

Looks like we need to dig deeper.

> inspect it deeply

Sure, let's see if we can find something interesting in the `.svg` file provided:

```xml
<svg (...) sodipodi:docname="hopfield_network_white.svg">
```

That's interesting - the file itself is named `network_white.svg`, what's the deal with `hopfield` then?

[Hopfield network](https://en.wikipedia.org/wiki/Hopfield_network)

Turns out it's neural network after all.

After reading on what a hopfield network is and how it operates, I wrote a simple Python script to simulate it:

```Python
import random
import numpy as np

weights = np.load("weights.npy")
dim = len(weights)

input_data = ???


def calculate_output(input_values: np.ndarray):
    values = np.copy(input_values)

    changed = True

    while changed is True:
        old_values = np.copy(values)

        new_values = [sum(weights[r][c] * old_values[c] for c in range(dim)) for r in range(dim)]
        values = [1 if i > 0 else 0 if i == 0 else -1 for i in new_values]

        changed = not all([values[i] == old_values[i] for i in range(dim)])

    return list([0 if i <= 0 else 1 for i in values])


result = "".join([str(i) for i in calculate_output(input_data)])

print(result)
```

There is one problem though - we need to provide the network with some input, and yet we have none. Out of curiosity, I decided to feed the network with random array of 1s and 0s - maybe it will stabilize at some value.

```Python
input_data = np.random.randint(2, size=dim)
```

```shell
> ./memory.py

01000110010011000100000101000111
```

Maybe because of luck (although I'm certain it's pure skill 😎) we got some output - since it's a multiple of 8 let's try converting it into ASCII characters:

```Python
print([chr(int(result[8 * i:8 * (i + 1)], 2)) for i in range(dim // 8)])
```

```shell
> ./memory.py

['F', 'L', 'A', 'G']
```

Okay, so where's the flag? Turns out, FLAG is the flag:

> sfi18_ctf{FLAG}
</details>