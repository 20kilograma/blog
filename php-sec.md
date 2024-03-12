# <center>PHP beyond SQLi (detailed) - its security, vulnerable code examples & improvement </center>
![image](https://github.com/KiraReys/blog/assets/44244085/8a52accc-7544-490c-858b-5da4b2031fac)


# >> About
PHP is a dynamically typed, server-side scripting language, created in the 1990s. This language has been out for a while, and it's today's standard when someone mentions back-end development for the web.
When PHP was created, one of its most important features was that it could be directly embedded into HTML and create dynamic content, which led to its wide spread in the 2000s. One of the biggest problems for developers
in the early versions of PHP was its Security. Early versions of PHP didn't have that many in-built security features like today, and PHP has improved since then sincerely. A big part of the community still thinks it's the same like it was back then. Blaming PHP when problems arise nowadays because you didn't sanitize user input, handle sessions properly, etc. is clearly a skill issue. In my opinion, today's community is still way too unaware of the issues that may arise from writing vulnerable code. That is why I am writing this blog; hopefully someone will catch up with more stuff and learn something new. <br>
If someone is wondering why the name of this blog is "PHP beyond SQLi", I just wanted to make a little joke because many developers (I'm talking about those devs that blame PHP when it comes to security) today only think about SQL Injection and preparing the statements because they heard they should do that, and maybe they heard about XSS too.

# >> Outline
<ul>
  <li>Popular Bugs</li>
  <ul>
    <li>Cross-Site Scripting(XSS)</li>
    <li>SQL Injection</li>
    <li>Cross-Site Request Forgery(CSRF)</li>
    <li>File Inclusion/Path Traversals</li>
    <li>Server-Side Request Forgery(SSRF)</li>
    <li>Remote Code Execution(RCE) & Code Injection</li>
    <li>Insecure Direct Object References(IDOR)</li>
    <li>XML External Entity(XXE) Injection</li>
  </ul>
  <li>Less Popular Bugs</li>
  <ul>
    <li>Type Juggling</li>
    <li>Rate-limit issues</li>
    <li>Sessions related issues</li>
    <li>PHP Object Injection(Insecure Deserialization)</li>
    <li>Integer & Float Overflow/Underflow</li>
    <li>PHP ReDoS</li>
  </ul>
  <li>Tips to write less vulnerable code</li>
  <li>Conclusion</li>
</ul>

## Cross-Site Scripting(XSS)

We are starting with the easiest one and, at the same time, the most common vulnerability that has been really long out there. If we search for XSS on <a href="https://www.hackerone.com" target="_blank">Hackerone</a>, we can see <i>~2500</i> pages in the results, which is an insane number if you think about it for just one platform. XSS is very underestimated, even today, and can be achieved in various ways. I'll cover the basics here, including dos and don'ts, along with a few examples. <br>
There are three main types/classifications of XSS: Reflected, Stored and DOM XSS. We are only interested in the first two (Reflected and Stored) because the third one happens when the DOM gets manipulated into executing arbitary JavaScript and it arises in the client-side code, so it's not of our interest. It's very easy to tell the difference between Reflected and Stored XSS, Reflected happens when an attacker injects his payload. We don't handle it appropriately, and it gets now reflected on our website but not stored, unlike in a case of Stored. It's okay if you don't understand; we'll make an easy example here.

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

As you maybe have guessed, in this case it's Reflected XSS. It's not getting stored or saved somewhere so there is no way for it to be <u>persistently</u> there (fun fact other name for Stored XSS is Persistent XSS).
