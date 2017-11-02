# Baby Regex
## Challenge
Our team has gotten hands on this text and we know that it has been used by BloodSuckers Corp. as an admittance test for their applicants in the Counter Intelligence(CI) team, since these guys have an eye for pattern recognition! Get this achievement by helping the members of Rebellious Fingers that will soon try to infiltrate the CI team and will need this test's results.

```text
Open your eyes is all that is needing. The heart lies and the head plays tricks with us, but the eyes see true. Look with your eyes. Hear with your ears. Taste with your mouth. Smell with your nose. Feel with your skin. Then comes the thinking, afterward, and in that way <knowing the truth. 
>
Open way to combat the horizon effect is to continue search when an otherwise terminal situation is judged to be particularly dynamic. Such heuristic continuation is sometimes called feedover. 

The mind which is created quick to love, is responsive to everything that is pleasing, soon as by pleasure it is awakened into activity. Your apprehensive faculty draws an impression from a real object, and unfolds it within you, so that it makes the mind turn thereto. And if, being turned, it inclines towards it, that inclination is love, for don't say blabla; that is nature, which through pleasure is bound anew within you. 

Tune up your circuits, check out your Chips

Because you're going to live a Long Life.
Check the identity card, it shows your code.

Listen to the white noise in your ears - it Fades AWAY.

Watching the sunset on the end of the HIGHWAY --- 
City meditation in curving reflections of NEON signs on the Chrome of the Cars.
The WeT Concrete and mirrored Streets recall shows the traffic away, 
recalls you to the smell of scratching cloudy sheets.
Billboards and Cholo-Ads above are the unfocused bottle of Time.
Drink it away, FLY to the ORBITAL Fly.
Away to drivin' the ocean of blue-green.
Drivin' away to the ocean of green-blue.
```
Pay close attention to the text in order to capture the sequence as it appears, understanding these instructions is also part of the challenge. The engine used is python2, everything is working as expected!

## Solution
The regexes were for the most part simple to write, until the various rules and limitations were put into place. To help with this I first ignored the rules/limitations to write regexes that would capture the words and then it was a bunch of trial and error to make them smaller and find clever ways to simplifiy the regexes to fit the limitations.
The best tool for this was using https://regexr.com/ so that I could see exactly how every change to the regex effected what it captured in real time.

>Type the regex that capture: "from "Drivin" until the end of phrase, without using any letter, single quotes or wildcards, and capturing "Drivin'" in a group, and "blue." in another", with max. "16" chars:

Given that the phrase was the very last line in the text I could use `.+$` to get the just the last line of the text and then it was just a matter of getting the two groups which ended up being the first seven characters and the last five characters.

`(.{7}).+(.{5})$`

---
### 

>Type the regex that capture: "(BONUS) What's the name of the big american television channel (current days) that matchs with this regex: .(.)\1", with max. "x" chars:  

The `\1` is a backreference to the first captured group `(.)` so then we know that the last two letters are the same so it becomes just a question of thinking of a three letter television channel where the last two letters are the same.

`CNN`

---
###

>Type the regex that capture: "<knowing the truth. >, without using "line break"", with max. "8" chars:  

We need to capture everything between `<` and `>` which is just `<.+\n>` but since we can't use any line breaks we instead replace it with `\s` which is any whitespace character.

`<.+\s>`

---
###

>Type the regex that capture: "the only word that repeat itself in the same word, using a group called "a" (and use it!), and the group expression must have a maximum of 3 chars, without using wildcards, plus signal, the word itself or letters different than [Pa]", with max. "16" chars:

After finding the only word that repeats itself in the text `blabla`, I figured out it can be easily captured with `(..a)\1` but the challenged required the group to be named. Since https://regexr.com/ doesn't support named groups the challenge then became trying to find the proper way to name groups in Python2.7's regex. From looking up the documentation for Python's regex library I was then able to find that `(?P<name>...)` could be used to create a named group and then it could be backreferenced with `(?P=name)`

`(?P<a>..a)(?P=a)`

---
###

>Type the regex that capture: "All "Open's", without using that word or [Ope-], and no more than one point", with max. "11" chars:

Initially I noticed that I could use `^\w+n` to get the first Open since `^` represents the beginning of the string so I then experimented with different ways to get the second Open. One idea I came up with was using positive lookbehind to look for either the beginning of the text or the `>\n`. That positive lookbehind `(?<=^|>\n)\w+n` captured both Open's but was too many characters. I went through a bunch of trial and error trying to make it less characters including trying to use a word boundary `\b` in the form of `\b\w{3}n`. Eventually I figured out that the regex accepted escaped hexidecimal so I could effectively use an `O` with `\x4f` which made things a lot easier. 

`\x4f\w+n`

---
###

>Type the regex that capture: "Chips" and "code.", and it is only allowed the letter "c" (insensitive)", with max. "15" chars:

My initial attempt was to use `[cC]....\n` but this didn't work since it also captured `Cars.`. Next after noticing the pattern of four words, a comma, two words, and then the words to capture and the pattern of both coming after `your`, I tried to use positive lookbehind but couldn't seem to find a way that stayed under the 15 character limit. Eventually I figured out similarly to the above challenge I could use escape code but this time using octal. By using octal coding I was able to put a `s` into the regex with `\163`.
 
 `c...\.|C...\163`

---
###

>Type the regex that capture: "FLY until... Fly", without wildcards or the word "fly" and using backreference", with max. "14" chars:

I first started off with the simple regex `FLY.+Fly` which captured everything but just didn't satisfy the limitations. Since I had to use backreferencing and the only common letter(s) was `F` then I know I had to change the regex to `(F)LY.+\1ly`. Once I had this I simply just simplified the regex down.

```regexp
FLY.+Fly
(F)LY.+\1ly
(F)L..+\1ly
(F)L.+\1ly
```
Could've made simpler: `(F).+\1..`

---
###

>Type the regex that capture: "the follow words: "unfolds", "within" (just one time), "makes", "inclines" and "shows" (just one time), without using hyphen, a sequence of letters (two or more) or the words itself", with max. "38" chars:

So first I wrote the regex to capture all of the words `unfolds|within|makes|inclines|shows`, but then I need to only capture the words once. I figured out that each word besides `unfolds` followed the word `it` exactly once and `unfolds` followed `and` once from this I added `([td] )` which I later turned into a positive lookbehind, `(?<=t |d )`. Now in order to satisfy the limitation of not having a sequence of letters, I replaced every other character with `.` which is any non-newline character. From there it became a trial and error process of trying to make it shorter, by figuring out which characters I could remove, eventually reducing it to `([td] )(u\w+s|w\w+n|m\w+s|i\w+s|s\w+s)`. I then noticed the pattern from words ending in a `s` so I was able to reduce it down to `([td] )(u\w+|w\w+n|(m|i|s)\w+s)`, but could've reduced it further to `([td] )(w\w+n|(u|m|i|s)\w+s)`. From this point I had the answer `(?<=t |d )(u\w+|w\w+n|(m|i|s)\w+s)` which I thought satisfied all of the conditions but I eventually figured out that I needed to make the `(m|i|s)` be a non-capturing group with `?:`. 

`(?<=t |d )(u\w+|w\w+n|(?:m|i|s)\w+s)`.

```regexp
unfolds|within|makes|inclines|shows
([td] )(unfolds|within|makes|inclines|shows)
([td] )(u.f.l.s|w.t.i.|m.k.s|i.c.i.e.|s.o.s)
([td] )(u\w+s|w\w+n|m\w+s|i\w+s|s\w+s)
([td] )(u\w+|w\w+n|(m|i|s)\w+s)
(?<=t |d )(u\w+|w\w+n|(m|i|s)\w+s)
(?<=t |d )(u\w+|w\w+n|(?:m|i|s)\w+s)
```
Could've made simpler: `(?<=t |d )(w\w+n|(?:u|m|i|s)\w+s)`

---
### Final Solutions
```
nc 200.136.213.148 5000
Type the regex that capture: "the follow words: "unfolds", "within" (just one time), "makes", "inclines" and "shows" (just one time), without using hyphen, a sequence of letters (two or more) or the words itself", with max. "38" chars: (?<=t |d )(u\w+|w\w+n|(?:m|i|s)\w+s)
Nice, next...
Type the regex that capture: "All "Open's", without using that word or [Ope-], and no more than one point", with max. "11" chars: \x4f\w+n
Nice, next...
Type the regex that capture: "(BONUS) What's the name of the big american television channel (current days) that matchs with this regex: .(.)\1", with max. "x" chars: cnn
Nice, next...
Type the regex that capture: "Chips" and "code.", and it is only allowed the letter "c" (insensitive)", with max. "15" chars: c...\.|C...\163
Nice, next...
Type the regex that capture: "FLY until... Fly", without wildcards or the word "fly" and using backreference", with max. "14" chars: (F)L.+\1ly
Nice, next...
Type the regex that capture: "the only word that repeat itself in the same word, using a group called "a" (and use it!), and the group expression must have a maximum of 3 chars, without using wildcards, plus signal, the word itself or letters different than [Pa]", with max. "16" chars: (?P<a>..a)(?P=a)
Nice, next...
Type the regex that capture: "<knowing the truth. >, without using "line break"", with max. "8" chars: <.+\s>
Nice, next...
Type the regex that capture: "from "Drivin" until the end of phrase, without using any letter, single quotes or wildcards, and capturing "Drivin'" in a group, and "blue." in another", with max. "16" chars: (.{7}).+(.{5})$
Nice, next...
CTF-BR{Counterintelligence_wants_you!}
```

## Flag
`CTF-BR{Counterintelligence_wants_you!}`
