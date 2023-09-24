# <center> Stored XSS on [REDACTED] - Profile Page | Bug Bounty </center>

Hello, Today I will be writing about a bug I've found a year ago on VDP. <br>
It was Stored XSS on a target which is something let's say, like a Social Media Platform. <br>

<hr>

I was editing one parameter that is directly displayed on your Profile Page. I noticed that the input is stored like this: <br>
<code><a class="..." href="..." title="YOUR_INPUT_HERE">YOUR_INPUT_HERE</a>
