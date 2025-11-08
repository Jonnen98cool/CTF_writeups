# Intro
Hello! I'm John, I work as a pentester at Outpost24. Me and my colleague Cathleene Sandgren are the main authors behind the wwwinterpub on-premises CTF challenge. Here's my writeup for our challenges. 

# Challenges
For this on-premises CTF, users get an initial sequence of numbers `0761` + 6 challenges or "Fragments" which they must solve. These challenges were designed to be able to be solved with pen and paper and your phone. Players got access to the website at `https://wwwinterpub.samurai.nu/s3cf3st_ctf` which acted as the main "hub" of the CTF, where challenge descriptions and clues were present. It looked a little something like this:<br>
![Website screenshot](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/hub_image.png)<br><br>

The idea was that each challenge will give a number 0-9 as the "flag", and players append the 6 numbers they get from solving the challenges, in order, to the initial sequence of `0761`. This forms a complete phone number which players call, and the answering machine provides them with a message which basically tells them to go to a certain path on the webapp. The content on that path then shows a secret phrase which they verbally state to any of the guys at the Outpost24 booth to claim their prize. The players don't immediately know that each challenge has a single digit as its flag, but it becomes apparent after solving multiple challenges.


## Challenge 1
![Chall1 description](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment1_desc.png)<br><br>
This challenge has no description, and its title is merely `?`. The `?` is actually a link to `/_1_.html` which displays this image:<br><br>
![Chall1 solution](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment1_solution.png)<br><br>

<b>Flag: 1</b>

## Challenge 2
![Chall2 description](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment2_desc.png)<br><br>
The first part of this challenge's description is a hint to the location of the challenge. The challenge is located on a shelf between two flower pots which are the shape of a monkey and a tiger respectively. The final part "Neither moose, nor horse, they still guide the course" is a hint. The challenge consists of a computer running a "glitchy" video on loop, where the glitching looks random but is either a "short glitch" or long one. There's also a longer pause between some of the glitches. This is actually a message encoded in Morse Code. The morse code is: `- .... . -- ....- - .-. .. -..-`. Decoding it gives `THEM4TRIX`. <br><br>

<b>Flag: 4</b>

## Challenge 3
![Chall3 description](https://github.com/Jonnen98cool/CTF_writeups/blob/mainCreated_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment3_desc.png)<br><br>
This challenge is - as the description states - a chessboard located among some barrels in the venue. The chessboard looked something like this:<br><br>
![Chall3 challenge](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment3_chessboard.png)<br><br>

This challenge was originally (and possibly still) one of the trickiest ones due to its multiple required steps, and the overload of information provided in the description and on the chessboard. Several steps were taken to make the challenge easier. This was the intended solution:<br>
1. Realize that any piece on a chessboard can be described by the row and column combination they are located in. The King for example is on position `A4`, or `4A`. The fact that no pieces are present in `G1-8` and `H1-8` is a hint that hexadecimal encoding has been used, which of course is the system using characters `1-9` and `a-f`. The description also hints at this: "... pieces notably absent from the "G" and "H" parts of the board".
2. The numbers 1-10 written on the board represent the order the message should be retrieved in. You start with the King, which represents `4A`, which is the character `J` in hex encoding. Continuing this process with the Knight and the remaining pieces, you get the following message: `JL+K-Z^M=?`. This fits with the title of the challenge, and will hopefully assure you that you're on the right track.
3. After having made this discovery, you are supposed to analyse the mysterious riddle which describes five different pieces. You may also note that the Pawns are not described in the riddle, and that Pawns are the only duplicate pieces on the board. Pawns represent non-variable characters in the equation (like `+` and `=`) while the remaining pieces all represent a variable (like `J` or `Z`). The riddle describes how various pieces enter the "battlefield" and what they are wearing. Noticably, the pieces are all misspelled - one extra character has been introduced. The extra character added represents that piece's variable. You already have this information from the board (the King is the 1st character of the equation which is `J`, the Bishop is the 4th character which is `K`), but the misspelling is meant to make it clearer. What you actually need to do at this point is to solve the equation, that is, find what `JL+K-Z^M` equals. This is where the riddle comes in, as you are supposed to substitute the five variables with the numbers each riddle hints towards. In isolation, the riddle for each piece might be hard to figure out, but as a collective you are meant to realize that you need to count the number of things each piece is wearing:
	- "... the Kijng arrived, wearing his crown with pride" --> 1
	- The Knight wears five crests --> 5
	- Bishop is *missing* their mitre --> -1
	- Queen wears two rings --> 2
	- "... the mRook emerged, crowned with twin banners" --> 2
4. Plugging in the values in the equation: `JL+K-Z^M=?` --> `1*5+(-1)-2^2=0` (note that `JL` should be treated as `J*L`, a common mathematical shorthand). The final message "Individually, the pieces are adorned. Collectively, they are bare." is meant to hint that the solution should be 0.<br><br>

<b>Flag: 0</b>

## Challenge 4
![Chall4 description](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment4_desc.png)<br><br>
With challenge being called "A Bit of Art" and the sole description being an image, it's clear that you will need to extract the binary message from the image. Reading top-down and left-to-right gives `010000100100010101000110010011110101001001000101000000000101010001010111010011110000000001001001000000000101001101010100010000010100111001000100` which translates
 to `BEFORE\00TWO\00I\00STAND`. Slightly tricky, but the message actually references `1`, which stands before two.<br><br>

<b>Flag: 1</b>
 
## Challenge 5
![Chall5 description](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment5_desc.png)<br><br>
We get a cryptic message `WmbLmppwAexglSzivVsqi` from a "Ruler". Googling "ides of march" reveals that it refers to the day Julius Caesar was assassinated. So it's a Caesar Cipher. From here you can easily bruteforce via an online decoder but the key is revealed from this message: "The fourth shall lead". The key is 4, and from that we get the message `SixHillsWatchOverRome`.<br><br>

<b>Flag: 6</b>

## Challenge 6
![Chall6 description](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment6_desc.png)<br><br>
" In a place you go to let it all out are instructions for how to play "The Counting Song" " refers to the fact that there's sheet music at a piano which is located inside one of the restrooms of the venue. It looked like this:<br><br>
![Sheet music](https://github.com/Jonnen98cool/CTF_writeups/blob/main/Created_CTFs/Securityfest2025_wwwinterpub_CTF/images/fragment6_sheet_music.png)<br><br>

For this instrument (i.e. piano), sheet music is read a little different compared to other instruments. Some notable distinctions are that both a bass and treble clef is used and that the notes are read from two rows simultaneously. So you would read rows 1 and 2 together followed by rows 3 and 4 together. One slightly tricky thing is that the note names (i.e. A, B, ..., G) for the different clefs differ, so this will have to be kept in mind when the notes are looked up on your phone. The first note of the song is "A". Then there's a pause for both rows, so SPACE. Then we have a "D" note on the treble clef, but everything on the treble clef is encoded and a "4" or "6" should be prepended. What this means is that it should be interpreted as "4D" or "6D". This is hexadecimal encoding for "M" or "m" respectively. So the message we got so far is `a m`. Continuing this until the end gives us `a mage and no monk and no angel and an elf and no dog`. Counting this party we get two (there's only a mage and an elf), so the flag is 2.<br><br>

<b>Flag: 2</b>



# Conclusion
Having solved all 6 challenges, the players can now complete the sequence, which is a phone number: `0761140162`. Calling this phone number, you are immediately met with the answering machine which plays a pre-recorded message voiced by Linda and Margarita from Cyber Chats & Chill in which they tell you to "... append winterpubwinner to your path". Doing this (i.e. going to `https://wwwinterpub.samurai.nu/s3cf3st_ctf/winterpubwinner`) you receive a message that the challenge has been completed and that you may claim your prize at the Outpost24 booth by stating a certain phrase.<br><br>

Thanks for playing, we sincerely hope you enjoyed our challenges!
