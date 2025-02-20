<center>
  
<h1> OOP in JavaScript & some differences from other languages </h1>

</center>

<img src="https://gatling.io/hubfs/Website/Features/JavaScript/Banner%20-%20JavaScript.png">

# >> About

JavaScript is the most widely used language on the web but is also used for desktop (Electron.js) and mobile (React Native, Cordova, etc.) apps. In this a bit shorter blog, I will try to explain the best way possible OOP in JavaScript. I will specifically explain the main differences from the other OOP languages like Java or C# and also give a few examples, which may help you if you are new to JS, but already have some experience in other languages.

# >> Outline
<ul>
  <li>About</li>
  <li>Prototype (based) language</li>
  <ul>
    <li>Some keywords</li>
  </ul>
  <li>Classic syntax vs ES6 syntax</li>
  <li>Dynamic typing</li>
  <li>Public & Private fields</li>
  <li>Conclusion</li>
</ul>

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

![image](https://github.com/user-attachments/assets/197399d7-999e-4230-bd3c-e5e59bd82c5f)

## Some keywords

## prototype

We already used one keyword above, and that's the <code>prototype</code>, it's the prototype a.k.a. the property under every object that represents the parent object.

<pre><code class="language-js">class Car {}
console.log(Car.prototype); // { constructor: class Car() [[Prototype]]: Object }</code></pre>

## constructor

This is a property of the prototype that will point back to the class the prototype was created from. Like every OOP language, JS has <code>constructor()</code> too, but don't get it mixed up with the <code>constructor()</code>, this is the <u>property of the prototype</u>, not the object's function call when the object is created from the class itself.

<pre><code class="language-js">class Car {}
console.log(Car.prototype.constructor); // class Car {}
</code></pre>

## &#95;&#95;proto&#95;&#95;

This is the internal reference which points to the object <code>prototype</code>, this has been deprecated in the modern browsers, now there are more modern approaches for Get/Set like <code>Object.getPrototypeOf()</code>
or <code>Object.setPrototypeOf()</code>.

## instanceof

<code>instanceof</code> checks if an object was created from a specific class and returns a boolean type.

<pre><code class="language-js">class Car {}
const audi = new Car();
console.log(audi instanceof Car); // true</code></pre>

<br>

# Classic syntax vs ES6 syntax

For our examples, we used the classic <code>class</code> syntax, but that wasn't available in the pure JS before the ES6 update. Functions, arrays, and pretty much everything in JavaScript that is not a default primitive type like numbers or strings is an object. This is how we would create the <code>Car</code> class and <code>audi</code> object from the example above:

<pre><code class="language-js">function Car(name) {
    this.name = name;
}

// You would also have to add methods through the prototypes
Car.prototype.go = function() {
    console.log("vrrr");
};

const audi = new Car("A3");</code></pre>

![image](https://github.com/user-attachments/assets/34984477-760b-4dab-bc31-08fa3ad94d1d)

With the update of EcmaScript in 2015, the <code>class</code> syntax was implemented with no need anymore to use the functions.

<pre><code class="language-js">class Car {
    constructor(name) {
        this.name = name;
    }

    go() { 
        console.log("vrrr"); 
    }
}</code></pre>

<br>

# Dynamic typing

Properties in JavaScript are dynamically typed, which means you won't have to specify a type for the property. JS does it automatically for you. 
Take this example in Java:
<pre><code class="language-java">class Car {
    private String name;
    private int price;

    public Car(String name, int price) {
        this.name = name;
        this.price = price;
    }
}

Car audi = new Car("A5", 13000);
</code></pre>

Notice the types above (<code>name</code> - String and <code>price</code> - Integer).
In JS it would be like this:
<pre><code class="language-js">class Car {
    constructor(name, price) {
        this.name = name;
        this.price = price;
    }
}

let audi = new Car("A5", 13000);

audi.name = "A6";
audi.price = 15000;</code></pre>

Also later if you want to change for example the <code>audi.name</code> to an integer like this:
<pre><code class="language-js">audi.name = 123;</code></pre><br>
This would not cause any errors normally, because there is no type safety in JavaScript, unlike other languages like Java.

<br>

# Public & Private fields

By default the fields in JavaScript are public; if you want to make them inaccessible from outside the class, you need to add the <i>#</i> prefix before the field to make them private. Take this for example:

<pre><code class="language-js">class Car {
    #name;  // Private field
    #price; // Private field

    constructor(name, price) {
        this.#name = name;
        this.#price = price;
    }
}

let audi = new Car("A5", 13000);

console.log(audi.#price); // Syntax error
audi.#name = "A4"; // Syntax error</code></pre>

You can also create private methods by adding the <i>#</i> prefix. Since JavaScript was not by default designed to be an OOP language, there is no <code>protected</code> keyword.

<br>
