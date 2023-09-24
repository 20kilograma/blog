# <center> Stored XSS on [REDACTED] - Profile Page | Bug Bounty </center>

Hello, Today I will be writing about a bug I've found a year ago on VDP. <br>
It was Stored XSS on a target which is something let's say, like a Social Media Platform. <br>
<hr>
# Bug Explained
I was editing one parameter that is directly displayed on your Profile Page. I noticed that the input is stored like this: <br>
<code>&lt;a class="..." href="..." title="<i>YOUR_INPUT_HERE</i>"&gt;<i>YOUR_INPUT_HERE</i>&lt;/a&gt;</code> <br>
Whenever you see that your input is being storing in an attribute you should always try to escape that and then do the other part of the magic. <br>
I tried adding <code>"</code> and it was a success, I escaped the " now the source looked like this: <br>
<code>&lt;a class="..." href="..." title="" "&gt;<i>"</i>&lt;/a&gt;</code> <br>
Notice that there is another <code>"</code> left so our payload would look like this: <code>"onload="alert()</code> because if we didn't put these quotes before alert <br>
our alert wouldn't execute it would look like this <code>onload=alert()"</code> and that is an error. Now if we add <code>"</code> before the alert the full source would be like this <br>
<code>&lt;a class="..." href="..." title="" onload="alert()"&gt;<i>"onload="alert()</i>&lt;/a&gt;</code> <br>
BUT it didn't work, the alert didn't fire, there are filters for event handlers on the website. Here at this point started trying all the event handlers. <br>
After few minutes I got an event handler that is not filtered <code>onmouseover</code>, now the payload looked like this <code>"onmouseover="alert()</code> <br>
It worked alter fired when I hover with my cursor over that anchor tag 😌. <br>
I had a good <strong>Impact</strong> there since HttpOnly wasn't set and I could steal victims Cookies, there were some sensitive Information in cookies too. <br>
You can also redirect victim to other pages, do SpearPhising, chain it with something else... was a High P2 if you ask me.
