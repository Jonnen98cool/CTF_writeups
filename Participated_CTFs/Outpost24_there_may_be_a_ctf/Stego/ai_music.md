# AI-Music (500p)  
## Challenge Description  

<em> The evil AI Wintermute has devised a cunning plan to conquer humanity by using nostalgia against us. Wintermute has re-generated all the summer hits from the 80s, embedding a secret message that could make people give up all resistance in the fight against AI. Your mission is to uncover this hidden message and stop Wintermute's insidious plot.

Author: Mikael Svall
File: [ctf-video.mp4](helper/ctf-video.mp4)</em>


## Thought Process
Tried some methods:
- `binwalk`
- `strings`
- check spectrogram with `audacity`
- watch video and look/listen for hidden clues


## Solution
After having thrown everything I got at the file, I decided to look up "mp4 steganography" and found a tool called `videostego`.
After that it was as simple as `videostego -f *video* -r`. 

