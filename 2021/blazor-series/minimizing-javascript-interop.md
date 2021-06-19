# Minimizing JavaScript Interop

When using Blazor, the most important thing to remember is that Blazor is designed as an enclosed system. It was not intended to play nicely with existing web technologies, most notably, JavaScript. This paradigm is already present in existing component libraries such as React. You are not supposed to manipulate the DOM directly in React through older libraries such as jQuery. React itself is an abstraction over the DOM.

Interop for Blazor exists because the web has only known JavaScript for the past twenty-five plus years. Similar to how Microsoft supported both 32 and 64 bit versions of their operating systems before only offering 64 bit. JavaScript is a 32 bit language that is now swimming in a 64 bit ocean. The main intent of Blazor, is to move the developer, team, and technology stack away from JavaScript.

When compared to a language such as C#, JavaScript has:

- Less features

- Is dynamic

- Not type safe

- Offers no built-in intellisense support

JavaScript is not a language that we as developers should want to gravitate to when Blazor and C# can offer so many quality of life improvements that are irresistible to pass on.

Adding to the disadvantages that JavaScript as a language has. Using JS interop in Blazor is messy at best. Importing scripts and methods requires you to use hard coded string values. Hard coded values are historically suspectable to easy misspellings and frustrating debugging sessions. Interop code must be carefully implemented as it exists outside of what Blazor tracks in the DOM. Mutating an object via both JS and Blazor is a recipe for undefined behavior. And lastly, Interop introduces a new level of complexity which accelerates the rate of entropy in our application, exacerbating the possibility of bugs.

Ultimately, the most likely scenario for having to using the JS interop feature will exist due to a third-party library such as GoogleMaps not offering native Blazor support.

When facing with the possibility of having to use JavaScript interop:

- Double check to see if a Blazor library does not already exist for your use case

- If a library does not exist, look to see if you can re-create the behavior natively in Blazor

- If the two above are not possible, keep to the best practices for JavaScript interop. Most notably by ensuring that any object is only manipulated by either JS or Blazor, but not both

- Keep vigilant for 3rd party developers on releasing native Blazor components

Blazor is an ecosystem in and of itself. While JavaScript interop is possible, it is highly recommended that you stay away from it as much as possible. Use of JS interop should be seen only as a last viable option that should be approached with care and caution by your development team.
