<center>
  
<h1> OOP in JavaScript and differences from other languages </h1>

</center>

<img src="https://gatling.io/hubfs/Website/Features/JavaScript/Banner%20-%20JavaScript.png">

# >> About

JavaScript is the most widely used language on the web but is also used for desktop (Electron.js) and mobile (React Native, Cordova, etc.) apps. In this a bit shorter blog, I will try to explain the best way possible OOP in JavaScript. I will specifically explain the main differences from the other OOP languages like Java or C# and also give a few examples, which may help you if you are new to JS, but already have some experience in other languages.

# >> Outline

# >> Prototype (based) language

Unlike the usual OOP languages, JavaScript is prototype-based, which means that the objects inherit the data from prototypes. But what is a prototype exactly?
<br><br>
Imagine you have cookies like from the popular TV Show Squid Game: 

<img src="https://freegamesboom.com/media/upload/game/f9/f95ce0ec0dcbe7f9152cee94478e30be.jpg">

Then we take the O cookie and create 4 multiples of them through a cutter machine that cuts them perfectly, and every one of them is totally identical. Now imagine on that machine you change the shape, and magically all the cookies you previously cut change the shape too; in this case, the "machine" is the prototype in JavaScript.
  
<img src="https://i.imgur.com/l3VCaTq.png">

<h1>|

v
</h1>

<pre><code class="language-js">// Machine changes the shape
Cookie.prototype.shape = "star";
</code></pre>

<h1>|

v
</h1>

<img src="https://i.imgur.com/kM1Htg0.png">
