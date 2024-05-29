# Exclusive CiPh3r (500p)
## Challenge Description  

<em> Our secret agents have recovered a short encrypted message that is believed to contain one of the flags capable of turning off the evil AI Wintermute. Despite their best efforts, our crypto analysts have been unsuccessful in deciphering the message. They now believe that brute-forcing is the only remaining method to crack this "Exclusive Cipher". The rumors says the key might consist of up to 12 digits long key, but no one really knows.

Author: Mikael Svall
**4d3 6367e 5a49 5 058 4766 61 767b 757 66f6 d6 95 d41 715b4 4706 c7b** </em>

## Thought Process
- I initially tried downloading some tools for XOR bruteforcing, as I knew the maximum keylength, part of the plaintext (it's probably `O24{`), and the desc hinted that bruteforcing it might be the only remaining way. I could not get this to work.
- For these types of challenges you sometimes need to assume some things and then try to solve them while holding your assumptions as truth. You start by trying with the assumptions you think are most likely to be correct. This can be difficult and it usually requires trial and error. In this challenge, the correct assumptions are:
	1. It's an XOR cipher (fairly confident in this considering the title and description)
	2. The spacing doesn't make sense and is just manually added to obfuscate the message, it should be removed (not very confident)
	3. It's hexadecimal encoded (so-and-so confident)
	4. The first four characters should be `O24{` (pretty confident but not 100%, as what if the cleartext is base64 encoded for example?)

## Solution
1. With the abovementioned assumptions in mind, copy the string to [Cyberchef](https://gchq.github.io/CyberChef/) and remove the spacing.
2. Cyberchef's "Magic wand" suggests to decode it "From Hex", comply.
3. Grab the XOR recipe and start inputting the key. We assume that the first plaintext character is `O`, in hex `4f`. The first ciphertext character is `4d`. XOR:ing these values with an online calculator gives us `02`, so we know that to get `O` as our first plaintext character the first part of the key should be `02`.
4. We continue this process and find the key `02040205`, which produces `O24{XMR]Ebcsyqtjom_Ds_Fun*` (final character in this string is the unprintable `0x7f`, represented here by a `*` character). This plaintext looks promising, as we can make out two word delimiters `_` which look to be placed appropriately, and the string `Fun` followed by only a single character which we think probably should be `}`.
5. From here you could probably take a more cryptoanalytic approach to find the rest of the key, but you could also notice the current key pattern and attempt to continue it. That is much easier, and does in fact produce the correct plaintext. The final key is: `0205020602070208`.
