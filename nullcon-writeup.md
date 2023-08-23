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
I saw the Hash of the password from admin and already knew what it was about, since I already did <a href="https://secops.group/php-type-juggling-simplified/" target="_blank">PHP Type Juggling</a> challenges. <br>
<img src="dbfile.png" /> <br>
The description and that Hash already made me think it's gonna be PHP Types, I first tried to find out which hashing algorithm it uses so I could search for a Magic Number/String that could match the Type for that algorithm.<br>
I tried figuring out the hashing algorithm by adding [] to the username/password parameter so I could get an PHP Error/Warning, that it is not an array, I may get some Info like that.<br>
<img src="phperrorarray.png"><br>
I actually succeded, it's sha1! <br>After some Google Search I found this <code>10932435112</code> "Magic" Number that should work with sha1 algorithm and guess what, it actually worked.
![flagfoundtypicalboss](https://github.com/KiraReys/blog/assets/44244085/b2313ac3-9cb8-48ee-ab21-73a103c34c2e)
<br>
I got the flag 😁: <code>ENO{m4ny_th1ng5_c4n_g0_wr0ng_1f_y0u_d0nt_ch3ck_typ35}</code>
<br>

# > Debugger

The next challenge had nothing on the landing page so I first checked the source code <br>![image](https://github.com/KiraReys/blog/assets/44244085/14d184a5-a33c-4a5d-9905-b6c8771585dd)
<br>
I visited that page and this is the source: <br>
![image](https://github.com/KiraReys/blog/assets/44244085/ed9a77c1-9fbd-45fc-89b2-4a0024951507)
<br>
First thing I was focused on was this <code>is_admin</code> check and thought it may have had something to do with some host headers but it didn't work, later I focused on the whole code<br>
and I noticed the <a href="https://www.codementor.io/@hayeskier/php-functions-makes-your-site-vulnerable-172bxpju01">insecure usage</a> of <code>extract();</code> storing it in <code>debug_info</code> which is later getting loaded, so we could exploit this by using this <code>extract()</code> to overwrite<br>
<code>is_admin</code> to 1, and that's exatcly what I did.<br>
<code>http://52.59.124.14:10018/?action=debug&filters[is_admin]=1</code><br>
Another flag: <code>ENO{N3ver_3xtract_ok?}</code>
<br>

# > IPfilter

This one was the same thing, there was nothing on the landing page, again check the source code and got the URL for Source Code 🙂 <br>
![image](https://github.com/KiraReys/blog/assets/44244085/d4267c0f-2275-48b5-a1f5-0922452c3de5)<br>
A bit longer source, but there are three parameters<br>
![image](https://github.com/KiraReys/blog/assets/44244085/a08d0577-bda8-4193-815b-7aa0a2dd0b11)<br>
bip one is where we are typing the IP and that IP gets fetched + we get the all the information (if we put debug_filter to 1) like it's shown on that Picture above. <br>
If that IP is not allowed which is for example the IP for backend with secrets <code>192.168.112.3</code> or any other not-local IP it will give you an error message. <br>
We had to bypass it somehow to fetch the <code>192.168.112.3</code>. I first looked at the regex check: <br>
![image](https://github.com/KiraReys/blog/assets/44244085/67092763-e7fe-4df9-b43a-bfb98989426a)<br>
It's simple regex, you can type up to 3 numbers there and the first thing I tried was adding a 0 before the 3, so the IP looks like this now: <code>192.168.112.03</code> <br>
There should be check for 192.168.112.03 and 003 too but there isn't and it didn't list it as bad IP <br>
![image](https://github.com/KiraReys/blog/assets/44244085/e992f16a-aba2-4ad5-bafc-d362ce171268) <br>
Flag 🥳: <code>ENO{Another_Fl4G_something_IP_STuff!}</code> 









