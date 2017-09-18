---
layout: post
title: Sarah - A secure hand cipher.
---
## Want to see more things like this?

### Patreon CTA
### Email list CTA

## What is this?

I made an encryption algorithm named "Sarah" that I think is reasonably secure against non-nation-state attackers, but can be performed by hand, taking under 20 seconds per character enciphered.

There's [JavaScript code on JS Bin](https://jsbin.com/duqusigiju/edit?js,console,output) so you can play with it yourself, and a $100 challenge at the end.

Unlike every secure cipher in modern use, Sarah uses plain English letters.

Lots of people have wanted something like this for a long time, and now it exists.

## Why did I make this?

I feel there's a dearth of strong ciphers that can be easily understood.

There's a wide gulf between simple cryptogram puzzles in the newspaper, and AES.

Cryptogram:
![cryptogram](/assets/images/Cryptogram-example.jpg)

AES cheat sheet from the excellent [Stick Figure Guide to the Advanced Encryption Standard (AES):](http://www.moserware.com/2009/09/stick-figure-guide-to-advanced.html)
![AES](/assets/images/aes_act_4_scene_17_crib_sheet_576.png)

I spent a really long time trying to understand what makes AES (and it's ancestor, DES) "strong" ciphers, and found it was really difficult.

So, I made Sarah so that hopefully you can understand a little of the magic that makes strong ciphers work.

Here's hoping this cipher is a useful teaching tool for understanding, and a fun toy to play with.

## The basics

The cipher uses only the 26 letters of the alphabet (`abcdefghijklmnopqrstuvwxyz`, in case you forgot) for both input and output. If you want spaces in your messages, just use the letter x, for example: "this**x**is**x**a**x**test"

A **key** is a random shuffling of the letters of the alphabet, for example: `xchgbydvjpalnfetrqmzkowuis`

The cipher uses two operations: *addition* and *substitution*.

To *add* two letters together, just use this handy Vigenere cipher chart: ![Vigenere cipher chart](/assets/images/864px-Vigenère_square_shading.svg.png) or this JavaScript code:

```
function add(letter1, letter2) {
  return String.fromCharCode((letter1.charCodeAt(0) + letter2.charCodeAt(0) - 97 * 2) % 26 + 97);
}
```

For example, say we want to *add* `test` to `xchg`.

We *add* `t+x=q`, `e+c=g`, `s+h=z`, and `t+g=z`, and we get `qgzz`.

To *substitute* a letter, replace that letter with the corresponding letter in the key. 

For example, say we have the alphabet, followed by our key:

abcd**e**fghijklmnopqr**s****t**uvwxyz

xchg**b**ydvjpalnfetrq**m****z**kowuis

We *substitute* t→z, e→b, s→m, and t→z.

`test` becomes `zbmz`

## What are the steps?

### By hand

In summary, Sarah is a system of ciphers, where Sarah-3 is the 3-round variant. Sarah-3 is implemented by the following steps:

#### Set up:

1) Choose a key by shuffling the alphabet at random (this yields approximately 88 bits of entropy).

`xchgbydvjpalnfetrqmzkowuis`

2) Prepare a key substitution paper with A-Z written across the top, and the key written beneath.

`abcdefghijklmnopqrstuvwxyz`

`xchgbydvjpalnfetrqmzkowuis`

3) Print out (or make) a Vigenere cipher grid for reference

4) Write the plaintext out in a row on graph paper (row 1).

`samplexplaintext`

#### Encryption:
Repeat 3 times:

1) Start working in a new, blank row (row 2).

2) Fill row 2 by replacing every letter in the previous row with its corresponding letter in the key.

`samplexplaintext`

`mxntlbutlxjfzbuz`

3) Start working in a new, blank row (row 3).

4) Copy the first letter in step 1 to row 3.

`m_______________`

5) Write a letter to each blank position in row 3 by combining the left letter in row 3 with the letter above it in row 2 using the Vigenere grid.

`mjwpabvozwfkjked`

6) Start working in a new, blank row (row 4).

7) Combine the first and last letters in row 3 with the Vigenere grid.

`pyujjkftsotdmwad`

8) Repeat steps 4 and 5 on row 4.

`tikppayzmezgnwxg`

`tblappnmycbhuqnt`

`mnyyncpbzbcjdtgz`

`nfiifhtcschpgzds`

`nsainunphjqflknf`

`skksfzmbirhmxhuz`

Final step:
Repeat step 1 and 2 from above.

`maamysncjqvnuvks`

And **maamysncjqvnuvks** is your ciphertext, after all that work! Yay!

Decryption is a reversal of the encryption steps.

### JS

<https://jsbin.com/duqusigiju/edit?js,console,output>

## Here's why this is hard to break, and other hand ciphers are easy.

Two words: confusion and diffusion.

Confusion (in our letter-y cipher) means that every letter should depend on several parts of the key, and diffusion means that changing a single letter in the plaintext should change all the letters in the output to be any random letter (so, on average, 25 out of 26 letters will change).

Hand ciphers almost never mix all letters.

Sarah mixes them all in every round so every output letter is dependent on every input letter.

For example, in round 1 of our example above, `samplexplaintext` transformed into `pyujjkftsotdmwad` after one round. Not only did every letter change, but every letter was a result of a *sum* of different amounts of each other letter in the plaintext.

Hand ciphers don't make everything depend on the key, usually a given ciphertext letter depends on either one or two key letters, at most.

Sarah mixes all the letters each round after applying the key, ensuring the key application is spread across all output letters.

The other important factor for security is to use non-linear transformations. A linear transformation is one where `f(ax + by) = a*f(x) + b*f(y)` for appropriate + and * operators.

## Features and caveats

### Features

This cipher uses just plain letters.

It's way easier to understand what's going on than the bits-and-bytes flow diagrams for other ciphers.

This is much harder to understand:
![typical hexadecimal](/assets/images/image009.png)

Appending random letters to a message can provide security against encrypting the same message over and over producing identical ciphertext.

### Caveats

If someone encrypts a bunch of short (<16 letters) messages with the same key, this can probably be broken.

Security is dependent on the non-linearity of the key as an S-box.

Sarah isn't guaranteed to use all of the key material, but I would be surprised if this is exploitable, since the set of key material used is likely not predictable from the ciphertext.

Ciphertexts are somewhat malleable. If one character is changed in a ciphertext, decryption still produces a somewhat legible message.

## Recommendations

Minimum length 16 characters.

Prefer enciphering longer messages.

Prefix message with random letters and then xx to hit length targets. [show example of what this means]

Use randomly-generated keys.

Don't use this to encrypt anything important, it's meant to be a toy.

## The road to a toy encryption algorithm

I thought of making a strong-ish cipher that used letters in 2010, but my first attempts were woefully awkward.

### Attempt #1
I tried to build a substitution-permutation network-based cipher in the vein of AES.

[sample of visualization]

It had an enormous 676-entry S-box (one row and column for each letter of the alphabet).

![stupid huge s-box](/assets/images/huge_s_box.png)

It had a key schedule like AES (more stuff to keep track of).

![overkill key schedule](/assets/images/key_schedule.png)

It also had separate key adding, substitution, and permutation steps, so every step was more complex than I would like (over 200 lines of code for what was supposed to be a simple "hand" cipher).

It was here that the cumulative adding (or "mixing" step in Sarah) occurred to me, but I didn't use it.

### Attempt #2
A few years later, it occurred to me that if I could use 26 entry S-boxes, that would be a lot simpler to work with.

I just used `qwertyuiopasdfghjklzxcvbnm` as the s-box for this version.

However, I would need to mix the letters in some way either grouped letter together (like the stupid 676 entry S-box), or "sliced" the letters into smaller pieces.

What if I converted the letters to base 3 and added a space character?

`A=000, B=001, C=002, D=010, ... , W=212, X=220, Y=221, Z=222`

So `bad` becomes `001000010`

Ok, that works, but it looks stupid to have a million 0, 1, and 2s and convert them back and forth to letters.

I could have left them as numbers, but then have I really simplified anything?

I also needed multiple S-boxes, because I didn't know how to prove that any one of them wasn't linear (which ruins the whole cipher).

So I used the shotgun approach of making a bunch of random S-boxes, in the hope that they were mostly non-linear in aggregate.

This would have made using this cipher require converting to base 3, having a bunch of little paper strips with S-boxes, and just a bunch of general complexity (which is exactly the opposite of what I'm trying to achieve here).

This version also still had a separate key mixing step, so a round in that cipher was roughly:
1) Add the key.
2) Convert letters to base 3 numbers.
3) Shuffle the numbers around.
4) Convert the numbers back to letters.
5) Substitute the letters for other letters.
6) Think to yourself: "this isn't very fun".

I decided to abandon this attempt.

### Attempt #3
A few days ago, I had a few insights:

What if the S-box is the key? Hm, that gives `log2(26!) ≈ 88.38` bits of security, which isn't bad.

Now the S-box and key mixing step can be the same step, so that's lot easier to work with and understand.

What if I use that cumulative adding thing that I thought of years ago?

Well, one mix pass isn't enough (the first letter would just be itself, and never get very "mixed" in with the others).

So why not use two passes?

It turns out that takes care of the permutation (or mixing) step, and is very easy to do with pencil and paper.

Now changing one letter changes every letter, which gives the diffusion property needed to claim this is a reasonably secure cipher.

As I was working on this version, I realized: "wait, do i even need blocks?". Hm...

What if the cipher just encrypts the whole message? That's pretty easy to work with (unless your message is a book), and it improves security (I think) if the message is longer.

So the secret really ended up being: "identify things that are cheap to do on paper, and really double-down on those".

## $100 Challenge!

I will give $100 to the best cryptanalysis of Sarah-3 that has been publically posted 90 days after this post goes live. Email me links to your public cryptanalysis at `blake8086@gmail.com`.

## Want to see more things like this?

### Patreon CTA
### Email list CTA

## References

links from people asking about pencil-and-paper ciphers:
[1](https://crypto.stackexchange.com/questions/8237/is-there-any-strong-enough-pen-and-paper-or-mind-cipher)
[2](https://crypto.stackexchange.com/questions/844/is-there-a-secure-cryptosystem-that-can-be-performed-mentally)
[3](https://math.stackexchange.com/questions/458384/cyber-paper-and-pencil)
[4](https://crypto.stackexchange.com/questions/1653/what-is-the-most-secure-hand-cipher)
[5](https://crypto.stackexchange.com/questions/18867/repeatable-crypto)

[DES cipher](https://en.wikipedia.org/wiki/Data_Encryption_Standard)

[AES cipher](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)

[Confusion and diffusion](https://en.wikipedia.org/wiki/Confusion_and_diffusion)

[Avalanche effect](https://en.wikipedia.org/wiki/Avalanche_effect)

[Substitution-permutation network](https://en.wikipedia.org/wiki/Substitution-permutation_network)

[Bifid cipher](https://en.wikipedia.org/wiki/Bifid_cipher)

[The Amazing King](http://theamazingking.com/)

[Linear/non-linear functions](https://www.reddit.com/r/cryptography/comments/4cr7e6/what_is_a_linearnonlinear_function_in_cryptography/)
