## Challenge description
*Jigboy, the superhero, possesses the remarkable ability to reel in colossal fish from the depths of the deep blue sea.*


## Thought process
We are given a small file of 415 bytes named [flag.damaged](https://github.com/Jonnen98cool/CTF_writeups/blob/main/mapna_ctf_2024/flag.damaged), it looks like this:

![](https://github.com/Jonnen98cool/CTF_writeups/blob/main/mapna_ctf_2024/original_file.png)

Running `file flag.damaged` gives `flag.damaged: PGP Secret Sub-key -`, which doesn't seem right. I suspect the file is corrupt somehow and I need to restore it (the file name also hints to this).  

I look at the first couple of bytes to see if their signature matches a known filetype according to [this site](https://www.garykessler.net/library/file_sigs.html). They don't, but I notice that they are pretty similar to .png (`89 50 4E 47 0D 0A 1A 0A`). I edit the file with `hexeditor` to contain the signature of .png, and rename the file to `flag.png`. I try to open it, no luck. Of course it wouldn't be that easy. With tools like `pngcheck`, I try to debug why it doesn't work. PNG files follow a specific format regarding file structure ([PNG specification](https://www.w3.org/TR/2003/REC-PNG-20031110/#5DataRep)). After studying the PNG specification for a while I realize that most of it is missing from the file I've been given. Perhaps it wasn't a .png file after all.  


## Solution
I look at the trailer signature of `flag.damaged` and lo and behold, it exactly matches the signature of the filetype jbig2 (compressed image file format). Looking at the first couple of bytes again, they very closely resemble the correct signature for a `.jb2` file, which is: `97 4A 42 32 0D 0A 1A 0A`. I edit the 2nd and 3rd byte to match the jbig2 signature and rename the file to `flag.jb2`. 415 Bytes is pretty small for an image file, but if it's compressed it makes more sense. The challenge title also leads me to beleive I am on the right track.  

I look for tools to open a `.jb2` file and find `jbig2dec`. I run `jbig2dec flag.jb2` but get some errors which are not very easy to debug. I consult the `man` pages for the tool and run it again with flags `--verbose=3` and `--dump` (="Print the structure of the JBIG2 file rather than explicitly decoding it."). Unfortunately, the latter option was not yet implemented. I spent some time trying to find a specification for jbig2 file structure but could not find anything useful.  

Eventually I had to resort to trial-and-error modification of bytes according to the error message I was given. First up is this error:
![](https://github.com/Jonnen98cool/CTF_writeups/blob/main/mapna_ctf_2024/error1.png)

It says `FATAL ERROR segment too short (segment 0x00000000)`. After identifying which 4 bytes refer to the segment (currently valued `0x00000000`) and changing these to no avail, I target `data_length=1` instead. I look for a `0x01` byte near the begining of the file and set it to `0x02`, running `jbig2dec` again until my change shows up. Through trial and error, I find that I need to edit the 24th byte. I set it to exponentially higher values to try to solve the error and get this. Setting it to `0xA1` for example produces a warning: `WARNING extra data in segment (segment 0x00000000)`. Eventually I find that `0x13` is the correct value. This change was enough for the file to be decoded into a viewable image:
![](https://github.com/Jonnen98cool/CTF_writeups/blob/main/mapna_ctf_2024/partial_flag.png)  

So close! I studied the verbose output - which now lacked any warnings or errors - to try figure out how to view the remainder of the image. I tried changing several values and observing if this had a positive effect on the decoded image. Eventually I discovered that the image viewer I was using displays the image resolution, which was 257x19. I needed "more image" so I searched for this value's hexadecimal equivalent (``0x101) and modified it to something higher, ran `jbig2dec` again and voila:
![](https://github.com/Jonnen98cool/CTF_writeups/blob/main/mapna_ctf_2024/complete_flag.png)  

I then spent an embarissingly long time trying to actually submit the flag, eventually having to ask my teammate how he interpreted the individual characters...  



After having solved the challenge I still don't know what the challenge description is supposed so reference.
