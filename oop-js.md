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

<br>

<pre><code class="language-js">// Machine changes the shape<br>
Cookie.prototype.shape = "star";
</code></pre>

<br>

<img src="https://i.imgur.com/kM1Htg0.png">

Let's now give it technically a go; a prototype is just a hidden parent object that teaches new objects what "they don’t know". When you create a class, JavaScript automatically creates a hidden object called a prototype. 
The prototype acts like a shared parent for all objects (instances) created from the class. If an object doesn’t know something, it looks up to its prototype to see if it can find it there.
Take this technical example:

<pre><code class="language-js">class Car {
    constructor(name) {
        this.name = name;
    }
}

const audi = new Car("A3");
const bmw = new Car("M3");

Car.prototype.fuel = "Diesel";</code></pre>

![image](https://github.com/user-attachments/assets/24442801-9ca2-4414-af45-3d129994dd58)

