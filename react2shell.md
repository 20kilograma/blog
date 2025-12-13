<center>
  
<h1> (CVE-2025-55182) React2Shell Overview and Impact </h1>

<img src="https://raw.githubusercontent.com/20kilograma/blog/refs/heads/site/react2shell.jpg" />

</center>

<br>

# >> About

There is a new critical (CVSS 10.0) exploit affecting React. If you haven’t been living under a rock for the last 10 years, you’ve probably heard of React. It is a JavaScript library for building UI, primarily used for FrontEnd web development. React allows developers to create reusable components, efficiently update the UI when data changes, and build complex applications in a structured and maintainable way. <br><br>
Notice how I said <i>primarily</i> for FrontEnd web development? That's because with releases of React Server Component, its capabilities have shifted but also opened up for attackers to explore for more critical attacking vectors. React Server Component is a special component that only runs on the server. They have a direct connection with the database, then render that component before it is sent to the client, so the server does that "pre-processing" there of some heavy data; that's what its goal is.

# >> Vulnerability

