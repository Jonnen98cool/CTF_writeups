## Challenge description
*Jigboy, the superhero, possesses the remarkable ability to reel in colossal fish from the depths of the deep blue sea.*


## Thought process
We are given a small file of 415 bytes named [flag.damaged](https://github.com/Jonnen98cool/CTF_writeups/blob/main/mapna_ctf_2024/flag.damaged), it looks like this:

![](https://github.com/Jonnen98cool/CTF_writeups/blob/main/mapna_ctf_2024/original_file.png)

Running `file flag.damaged` gives `flag.damaged: PGP Secret Sub-key -`, which doesn't seem right. I suspect the file is corrupt somehow and I need to restore it (the file name also hints to this).  

I look at the first couple of bytes to see if their signature matches a known filetype according to [this site](https://www.garykessler.net/library/file_sigs.html). They don't, but I notice that they are pretty similar to .png (`89 50 4E 47 0D 0A 1A 0A`). I edit the file with `hexeditor` to contain the signature of .png, and rename the file to `flag.png`. I try to open it, no luck. Of course it wouldn't be that easy. With tools like `pngcheck`, I try to debug why it doesn't work. PNG files follow a specific format regarding file structure ([PNG specification](https://www.w3.org/TR/2003/REC-PNG-20031110/#5DataRep)). After studying the PNG specification for a while I realize that most of it is missing from the file I've been given. Perhaps it wasn't a .png file after all.  


## Solution
I look at the trailer signature of `flag.damaged` and lo and behold, it exactly matches the signature of the filetype jbig2 (compressed image file format). Looking at the first couple of bytes again, they very closely resemble the correct signature for a `.jb2` file, which is: `97 4A 42 32 0D 0A 1A 0A`. I edit the 2nd and 3rd byte to match the jbig2 signature and rename the file to `flag.jb2`. 415 Bytes is pretty small for an image file, but if it's compressed it makes more sense. The challenge title also leads me to beleive I am on the right track.  

I look for tools to open a `.jb2` file and find `jbig2dec`. I run ``





After having solved the challenge I still don't know what the challenge description is supposed so reference.
