<center>
  
<h1>Machine - CodePartTwo (Easy)</h1>

<img width="867" height="525" alt="image" src="https://github.com/user-attachments/assets/b803b40e-f891-4beb-9468-38116b312e40" />

</center>

Hello everyone, today I am writing a write-up for the CodePartTwo machine on HackTheBox. It featured a vulnerable Js2Py library within a Flask application that allowed for a sandbox escape and then Remote Code Execution.<br><br>

Initial nmap Scan:
<pre><code class="language-none">Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-01 17:17 CET
Nmap scan report for 10.129.232.59
Host is up (0.017s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 a0:47:b4:0c:69:67:93:3a:f9:b4:5d:b3:2f:bc:9e:23 (RSA)
|   256 7d:44:3f:f1:b1:e2:bb:3d:91:d5:da:58:0f:51:e5:ad (ECDSA)
|_  256 f1:6b:1d:36:18:06:7a:05:3f:07:57:e1:ef:86:b4:85 (ED25519)
8000/tcp open  http    Gunicorn 20.0.4
|_http-server-header: gunicorn/20.0.4
|_http-title: Welcome to CodePartTwo
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.43 seconds
</code></pre>
<br>
Now, as you can see, we only have SSH and HTTP 8000 open. When we visit the web app we can see this:
<img width="928" height="900" alt="image" src="https://github.com/user-attachments/assets/73d69581-5e30-4494-a026-32d0f5c607ff" /> <br>
When we download the app, we can see it's a Flask Web App. If we check the <i>requirements.txt</i> file for libraries that are included in the application: <br>
<pre><code class="language-none">flask==3.0.3
flask-sqlalchemy==3.1.1
js2py==0.74</code></pre>
<br>
It contains a vulnerable version of <b>js2py</b> to <a href="https://nvd.nist.gov/vuln/detail/CVE-2024-28397">CVE-2024–28397</a>. <br>
After we register and log in on the web app, there is a Code Editor that allows us to run JavaScript code, we can read the source code from the zip of the app we downloaded, but it was pretty obvious that here is the place where the vulnerable
version of <b>js2py</b> is being used.
<img width="978" height="896" alt="image" src="https://github.com/user-attachments/assets/a1d2ea57-dbb4-4f55-88a3-aaad85e2583e" />



