# Solution
To solve this challenge, you need to figure out two things:
1. Roughly what format the password is most likely to have
2. How to crack that format (i.e. what command to use)

The premise behind the challenge is that of a "reverse password", i.e. "Password123!" becomes "!123passworD". This is hinted at several times in the description, along with an example variable name (plus the "2.0 insidE ouT") that follows the reverse format. Still, it is not entirely obvious. We know that the password must contain certain types of characters (e.g. at least one uppercase), and we know that it (probably) uses an English word as base.<br><br>

Therefore, what we want is a *Hybrid Attack*. It's called "hybrid" because it's a mix of a traditional wordlist attack and a mask attack. The hybrid attack applies the mask on each item in the wordlist. Therefore, each word can have multiple permutations, e.g. "hello", "hello!", "hello67/?". To use the hybrid attack, use mode `-a 6` for appending the mask to the word, and mode `-a 7` for prepending.<br><br>

Following the idea of a "reverse password", what we want is to prepend some special characters to the word. We don't know which characters nor how many of them, but if at least one number and one special character is required, let's start out with one to four characters (technically, we should use `--increment-min 2` since we know there must be at least one special char and one number, but neglecting this costs so little that it's fine). This is denoted as `-1 ?s?d '?1?1?1?1' --increment` in the command we are going to use. To explain it; we define a custom character group with `-1 ?s?d` which contains all special character and all digits. When then use our custom character group four times with `'?1?1?1?1'`. The `--increment` specifier tells hashcat to increment the mask, meaning it starts with `?1`, then tries `?1?1` and so on until `?1?1?1?1`. We use `--increment` because we don't know how many characters to prepend. It could be more than four characters, but we're starting out with four because the keyspace will grow very quickly (remember that we are trying the mask for each words in the wordlist).<br><br>

Next up, if the description is to be trusted, the password will probably end in an uppercase character. We can use `sed` to transform a file such that the final character will become uppercased. To be on the safe side, we also use the original wordlist without the transformation in the crack. I combine the original and modified wordlists into `combined_wordlist.txt`:
```bash
sed 's/.$/\U&/' english_words_466k.txt  > english_words_466k_final_char_UPPER.txt
cat english_words_466k.txt english_words_466k_final_char_UPPER.txt > combined_wordlist.txt
```

The final command is thus as such: `hashcat -a 7 -m 0 afdbf16a4cab2c8fbd5810b8dbcac640 -1 ?s?d '?1?1?1?1' --increment combined_wordlist.txt --potfile-disable`. This takes 10s to crack on a GTX 970 and the password/flag is: `!1_conveyoR`.
