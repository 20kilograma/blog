# <center> NULLCON CTF Web Writeups </center>

Hello everyone! <br> Today I'm writing my writeup for the CTF Challenges on Nullcon that I've done.
Those challenges are all Web related and these are the challenges I've done: <code>TYpicalBoss</code>, <code>Debugger</code>, <code>IPfilter</code>, <code>Colorful</code>, <code>Magic Cars</code>.
<br>
Let's see now:
<br>
# > TYpicalBoss
This was probably the easiest one of all, since I already did some CTFs where <a href="https://secops.group/php-type-juggling-simplified/" target="_blank">PHP Type Juggling</a> was the key to it. <br>
This was the Description of TYpicalBoss Challenge: <br>
<code>
My boss just implemented his first PHP website. He mentioned that he managed to calculate a hash that is equal to 0??? I suppose he is not very experienced in PHP yet.
</code> after I read that I looked up at / and there were some files there and one important there was the .db file, I downloaded that file and opened it. <br>
I saw the Hash of the password from admin and already knew what it was about since I already did <a href="https://secops.group/php-type-juggling-simplified/" target="_blank">PHP Type Juggling</a> challenges. <br>
<img src="dbfile.png" /> <br>
The description and that hash already made me think it's gonna be PHP Types, I first tried to find out which hashing algorithm it uses so I could search for a number that could match the Type for that algorithm so it can get to TRUE when matched together.<br>





