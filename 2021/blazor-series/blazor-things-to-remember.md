# Blazor Things To Remember

[Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) is a wonderful new framework put forth by Microsoft as their entry into the WebAssembly development space. It allows us as developers to write responsive web applications in the language of our choice. Our code will be translated to native WebAssembly when we run our application. The biggest advantage to WebAssembly and Blazor is that it allows us to sidestep the short comings of JavaScript by using a fully-featured, type-safe language.

Like many other new technologies, simply using Blazor is no silver bullet for development teams. If you are writing bad code in Angular, React, or Vue, migrating to Blazor won't suddenly solve all your problems. In some cases, it may just make them worse. When choosing to migrate or re-imagine an existing application in Blazor, here are some things to take note of before you commit to a new project:

1) Having zero or as little as possible JavaScript interop

Blazor has the ability to interop with JavaScript both ways. Blazor can invoke JavaScript functions, and JavaScript can invoke Blazor. This is something that is required currently due to the shear amount of JavaScript libraries that exist. This should not be an excuse to use as many JavaScript libraries as possible. The whole point of WebAssembly in the first place is the avoid JavaScript as much as possible. It is understandable that there may be an edge case somewhere in your application for a certain time frame that will require interop. But it will not last forever. Getting away from JavaScript is the reason why you chose Blazor in the first place.

2) Remembering that the Web is a detail

Blazor, and by extension WebAssembly are very new technologies that are swimming in a sea of equally new technologies. Who could have predicted GraphQL ten years ago? Or WebAssembly. So why with all this predictability are we chaining our web applications to our application code? It is imperative to remember that the web is a detail. Just like how mobile and desktop are both details as well. Save yourself another re-write by separating your application from how it is presented to your customer. By taking the extra effect to put your application code behind a solid API. Blazor can utilize its' built in HttpClient will save your development team countless hours of work when the next great technology comes along.

3) Choosing the correct Blazor app

Blazor is not just a single type of application. It comes as a three pack with different hosting options. You have the option of choosing from a standard WebAssembly app. This is the closest to a traditional SPA application. The app is loaded into the browser and is executed via WebAssembly. A progressive web application where the user my physically download the application into their physical machine which may allow them to run the application with our without an internet connection. And finally, a ASP.NET Core hosted solution where the application is executed server-side and communicates to the browser via SignalR.

4) Not embracing Component-based architecture

Embracing Blazor means you are embracing a component-based architecture that puts composition as a first-class citizen. Blazor is very malleable in that it allows you to approach it in many different ways from an architecture standpoint. This does not give you the green light to attempt to shoehorn what you have done in the past with MVC or Razor Pages with Blazor and expect it to work. Every component should be able to live by itself, and still place nicely with others. Blazor may be state-ful, but components should be state-less when possible. This allows for easier testing and greater re-usability between components. In this aspect, Blazor is related to other component-based frameworks. If you are already comfortable with components in Vue, Angular, or React, you've already know the hardest part of Blazor.

5) Just because you can, does not mean you should

Blazor, and software in general are both wonderful and at the same time scary because of all the possibilities that are allowed. Great engineering is concerned with only allowing both clients and developers only a certain set of possibilities. Less possibilities translates into fewer undesirable things that can happen. Blazor is no different to this rule. I would go so far as to say that the number of possibilities are far greater compared to a traditional JavaScript SPA because C# as a language has more features than JavaScript. Remember, just because you can, does not mean you should. The point of a web application is to get information to and from the end-user by means of a graphical interface via a browser. Keep focused on what brings value to your end-users, not what is possible.

Blazor is an exciting new framework that attempts to solve many of the disadvantages that are inherit to traditional JavaScript SPA's. However, Blazor comes with its own set of potential issues that need to be tackled before starting any new projects. Ultimately writing a well architected, engineered, and tested web applications is more important than jumping on the latest technology bandwagon.
