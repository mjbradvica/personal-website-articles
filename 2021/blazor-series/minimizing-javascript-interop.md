# Minimizing JavaScript Interop

When using Blazor, the most important thing to remember is that Blazor was designed as an enclosed system. It was not intended to play nicely with existing web technologies, most notably, JavaScript. This paradigm is already present in existing component libraries such as React. You are not supposed to manipulate the DOM directly in React through older libraries such as jQuery. React itself is an abstraction over the DOM.

Interop for Blazor exists because the web has only known JavaScript for the past twenty-five plus years. It parallels how Microsoft supported both 32- and 64-bit versions of their operating systems before discontinuing support for 32-bit. JavaScript is a 32-bit language that now swims in a 64-bit ocean. The main intent of Blazor is to move the developer, team, and technology stack away from JavaScript.

When compared to a language such as C#, JavaScript has:

- Fewer features

- Not static

- Not type safe

- No built-in intellisense support

JavaScript is not a language that we developers should pursue when Blazor and C# can offer so many quality of life improvements.

Using JS interop in Blazor is messy at best. Importing scripts and methods requires you to use hard-coded string values. Hard-coded values are historically susceptible to annoying misspellings and frustrating debugging sessions. Interop code must be carefully implemented as it exists outside of what Blazor tracks in the DOM. Mutating an object via both JS and Blazor is a sour recipe for undefined behavior. Lastly, interop introduces a new level of complexity which accelerates the rate of entropy in our application, exacerbating the possibility of bugs.

Ultimately, the most likely scenario for having to using the JS interop feature will exist due to a third-party library such as GoogleMaps not offering native Blazor support.

When faced with the possibility of having to use JavaScript interop:

- Double check to see if a Blazor library does not already exist for your use case

- If a library does not exist, look to see if you can re-create the behavior natively in Blazor

- If neither is possible, keep to the best practices for JavaScript interop, most notably by ensuring that any object is manipulated only by JS or Blazor (but not both!)

- Keep vigilant for third party developers' releasing native Blazor components

Blazor is an ecosystem. While JavaScript interop is doable, it is highly recommended that you stay away from it if possible. Use of JS interop is a last resort that should be approached with care and caution by your development team.
