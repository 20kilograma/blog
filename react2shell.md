<center>
  
<h1> (CVE-2025-55182) React2Shell Overview and Impact </h1>

<img src="https://raw.githubusercontent.com/20kilograma/blog/refs/heads/site/react2shell.jpg" />

</center>

<br>

# >> About

There is a new critical (CVSS 10.0) exploit affecting React. If you haven’t been living under a rock for the last 10 years, you’ve probably heard of React. It is a JavaScript library for building UI, primarily used for FrontEnd web development. React allows developers to create reusable components, efficiently update the UI when data changes, and build complex applications in a structured and maintainable way. <br><br>
Notice how I said <i>primarily</i> for FrontEnd web development? That's because with releases of React Server Component, its capabilities have shifted but also opened up for attackers to explore for more critical attacking vectors. React Server Component is a special component that only runs on the server. They have a direct connection with the database, then render that component before it is sent to the client, so the server does that "pre-processing" there of some heavy data; that's what its goal is. <br>

# >> Vulnerability

The vulnerability was found in a protocol called Flight, which handles communication between the client and server in React Server Components. It is used to send component data and results back and forth over HTTP. The issue lies in how the server parses user-supplied data. It blindly trusts serialized payloads from the client and deserializes them without sufficient validation. This is where the vulnerability occurs, as it can lead to unsafe deserialization(server-side execution). <br>

# >> Payload
<pre><code class="language-json">
{ 
 "then": "$1:__proto__:then",  
 "status": "resolved_model",  
 "reason": -1,  
 "value": "{\\"then\\":\\"$B1337\\"}",  
 "_response": {  
   "_prefix": "process.mainModule.require('child_process').execSync('whoami');",  
   "_chunks": "$Q2",  
   "_formData": {  
     "get": "$1:constructor:constructor"  
   }  
 }  
}
</code></pre>
<br>
This payload takes advantage of deserialization by creating a harmful "gadget chain" that deceives the server into running arbitrary code while handling the received data. The exploit depends on the React Flight protocol permitting JSON strings to refer to segments of the object graph (via the $ syntax) and the server's automatic attempt to "resolve" objects resembling Promises. By adding a property called <pre>then<pre> to the root object the attacker generates a custom <pre>then</pre> Promise. When the server's runtime comes across this object it automatically tries to await it causing the execution of the function linked to that then property.
<br><br>
The payload contains the logic within the <pre>_response</pre> and <pre>_formData</pre> properties, using the path <pre>$1:constructor:constructor</pre> to reach the JavaScript global <pre>Function</pre> constructor. This access enables the attacker to convert a string of the <pre>_prefix</pre> value holding <pre><code class="language-js">process.mainModule.require('child_process).execSync('whoami')</code></pre> into runnable JavaScript code. By linking the <pre>then</pre> property to this dynamically created function, the exploit creates a loop. The server deserializes the object, sees it as a <pre>Promise</pre>, calls <pre>then()</pre> to resolve it, and then executes the payload from the _prefix "string".

<br>
<br>

# Thank you for reading this Writeup, appreciate it <3 and I’ll soon write more!
