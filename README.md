# AffinityCTF2019-Quals writeup

## 1. BreakingBad (crypto, 150)

HoRfSbMtInMcLvFlAcAmInMcAmTeErFmInHoLvDbRnMd

Note: put flag into AFFCTF{} format.

### Solution:

From the task name it's quite obvious that the given string contains the chemical elements from the Mendeleev's table. And the only thing that can represent some hidden data is the atomic number.
Lets decode it:

```
Ho - 67
Rf - 104
Sb - 51
Mt - 109
In - 49
Mc - 115
Lv - 116
Fl - 114
Ac - 89
Am - 95
In - 49
Mc - 115
Am - 95
Te - 52
Er - 68
Fm - 100
In - 49
Ho - 67
Lv - 116
Db - 105
Rn - 86
Md - 101
```
Converted to ASCII: ```Ch3m1strY_1s_4Dd1CtiVe```

Flag: ```AFFCTF{Ch3m1strY_1s_4Dd1CtiVe}```

## 2. Stegoego (stego, 150)

![stegoego.png](files/stegoego.png)

### Solution:

The most common tool for this type of challenges is Stegsolve, but this time, I simply saw the stereogram, trust me :D

The flag is ```AFFCTF{Defence123}```

## 3. Alphinity (stego, 300)

Welcome!

![alphinity.png](files/alphinity.png)

### Solution:

First I always try to change brightness and contrast, but it didn't help here.
The challenge name sounds close to ALPHinity.. alpha.. alpha-channel.
Ok, let's try to extract the data from it with Stegsolve.

After a few attempts I got this:

![alphinity_extracted.png](files/alphinity_extracted.png)

```be b9 b9 bc ab b9 84 97 cc 93 93 cf a0 cb 99 99 ce 91 ce c8 86 82```

Looks structured but still unreadable. We know that the flag format is AFFCTF{...}. The second and the third bytes are equal, so it should be some kind of constant shift (not constant as appeared to be).
Maybe there is another, more smart solution, but I counted shift like this:

```
def getChr(byte):
  return chr(int('be',16) + (ord('A') - int(byte,16)))
```

I simply passed every byte to this function and recovered the flag symbol by symbol: ```AFFCTF{h3ll0_4ff1n1n17y}```

## 4. XY_FUN (stego, 300)

Note: put flag into AFFCTF{} format

![xy_fun.wav](files/xy_fun.wav)

### Solution:

Nothing interesting in Audacity:

![xy_audacity.png](files/xy_audacity.png)

But the task name sounds like xy_fun... function... y(x)..
Ok, what if we try to plot the image considering audio samples as coordinates?
WAV is an uncompressed format, so the audio samples performed simply as numbers:

```wav file = [44 byte wav header][raw audio samples...]```

where

```[raw audio samples] = [LRLRLRLRLRLR...]```.

L and R are numbers for left and right channel.

Let's write python script to extract the samples:

```
import struct
b = open('xy_fun.wav', 'rb').read()[44:] #omit first 44 bytes
raw_bytes = [struct.unpack('h', ''.join(b[i : i+2]))[0] for i in xrange(0,len(b), 2)]
with open('wav_to_draw.txt', 'w') as file:
	i = 0
	while i < len(raw_bytes):
		file.write(str(raw_bytes[i]) + ' ' + str(raw_bytes[i+1]) + '\n')
		i+=2
```

Now ```wav_to_draw.txt``` file looks like this:

```
6333 31004
25467 30273
25873 29579
...   ...
```

Let's plot it! I used gnuplot for this.

```
$ gnuplot

  G N U P L O T
  Version 5.2 patchlevel 2    last modified 2017-11-01 

  Copyright (C) 1986-1993, 1998, 2004, 2007-2017
  Thomas Williams, Colin Kelley and many others

  gnuplot home:     http://www.gnuplot.info
  faq, bugs, etc:   type "help FAQ"
  immediate help:   type "help"  (plot window: hit 'h')

gnuplot> plot "wav_to_draw.txt"
```
The output image was mirrored. Afted mirroring it back I finally got this:

![xy_flag.png](files/xy_flag.png)

And the flag is: ```AFFCTF{XY_1S_FUN}```

## 5. MIDI 1 (misc, 50)

plaintext plaintext everywhere....

![midi.pcap](files/midi.pcap)

### Solution:

Let's open the attached file with Wireshark:

![midi1.png](files/midi1.png)

We see some intercepted traffic, so, the first thing to do is simply to follow the stream :)

![midi1_1.png](files/midi1_1.png)

Yeah!! Pretty easy. The flag is: ```AFFCTF{s3lf_sign3d_is_good_3nough}```

## 6. Pharmacist Nightmare (forensics, 300)

Our malware intercepted traffic from one of drugstore computers. Can you find prescription?

![drugstore.pcap](files/drugstore.pcap)

### Solution:

Let's open the file in Wireshark:

![drugstore_1.png](/files/drugstore_1.png)

We see some unusual traffic here... some URB_BULK in, URB_BULK out, URB_INTERRUPT... wtf?
After some googling I found that it is an intercepted USB traffic from multiple usb devices...


TO BE CONTINUED

