---
layout: default
title: Spaced out part 1: deciphering dynamic programming
---

<div class="box">

# {{ page.title }}

This is my second year participating in the [National Cipher Challenge][http://www.cipher.maths.soton.ac.uk/], a competition to crack encoded texts. The challenges get harder over time, and one of the ways that this is achieved is that proper spacing is removed, leaving only a long stream of characters with no indication of where words start and stop. I've developed a useful algorithm that tries to insert spaces in decoded text, which makes our tasks of reviewing it far easier. I suspect that it could also form part of an automated substitution cipher decoding system, but first let's see the algorithm itself.

The principle is simple enough. Let's say that I have a string, "`ateststring`", that I want to split into individual words to stick spaces between, and that I want to end up with words that are all commonly used in a typical English text. I could stick a space after the first `a`, giving me "`a teststring`". Alternatively if I put it after `at`, I get "`at eststring`". I could even reasonably end up with "`ate ststring`". However, the problem with the last two spacings is that it isn't possible to split the rest of the string sensibly. This makes it clear that splitting after the `a` is the best.

How do we formalise the concept of a "commonly used word"? We can define a "badness" score to a word, which gives an indication of how rarely that word comes up in typical English text -- higher is rarer. Then, the objective of the algorithm is to split up the string into words which give the lowest badness score when summed. Let's call our algorithm `split`, taking a parameter `s`, the string to split, and outputs this lowest. We want to take a single word off the front of the unspaced string, find the badness of that word, and then check the best way to split the rest of that string. There are many ways of taking a word off the front, so let's try them all. Notice that we can define split in terms of itself (Pythonesque pseudocode):

```
def split(s):                                       # "ateststring"
    return min(badness(s[0:1]) + split(s[1:]),      # "a" "teststring"
               badness(s[0:2]) + split(s[2:]),      # "at" "eststring"
               badness(s[0:3]) + split(s[3:]),      # "ate" "ststring"
               ...)
```

Of course, this naive recursion is very inefficient in itself, but like other simple recursive algorithms, we have two ways of reducing it to a more efficient algorithm: adding memoisation or rephrasing in terms of dynamic programming. I chose the latter, as it lends itself to a very nice iterative solution.

Dynamic programming involves creating an array containing the best spacings calculated so far. Let's say that `bestSplit[i]` contains the score of the best spacing up to a certain position, `i`, in the string. Then, we take a new word of length `l` starting from `i`, and see whether this creates a better split than currently exists in `bestSplit[i+l]`. Rinse and repeat with increasing values of `l` until the new word that you have does not exist. Then, when `i` is increased by 1, you know that the new `bestSplit[i]` is optimal.

Clearly, this algorithm only tells you the score of the best spacing, not the positions of the spaces themselves! What we need to do is keep track of the location of where the previous split occurred that gave rise to the current split. This way, you can find all of the split locations by starting from the end of the string and following the previous split location again and again until you get to the beginning of the string.

Let's see this all in Pythonesque pseudocode:

```
# start with an infinitely bad score
bestSplit = [infinity] * length of string
prevSplit = [None] * length of string

bestSplit[0] = 0

for i = 0 to length of string:
    for l = 1 to maximum length of word:
        newScore = bestSplit[i] + badness(s[i:i+l])
        if bestSplit[i+l] > newScore:
            bestSplit[i+l] = newScore
            prevSplit[i+l] = i
```

In the next part of this series, I will be exploring the badness function, the trie, and further applications of this algorithm beyond the obvious.

</div>
