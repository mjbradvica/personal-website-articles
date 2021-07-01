# Blazor Hosting Models

Blazor presents three different ways of hosting your application. I am not talking about hosting in the sense of using a cloud service or having a server farm in a physical location. Hosting, in Blazor’s case, revolves around where the web application will live.

The wonderful thing about all three options is that your code remains the same. Your decision, then, is mostly concerned with how the application will be delivered to the client and the capabilities of their connection. The three hosting methods are via WebAssembly, a "progressive web application,” or PWA, and ASP.NET Core hosted.

The default WebAssembly application is for end-users who use browsers that support WebAssembly. This is almost all modern browsers such as Firefox, Chrome, and Edge. If your customer base has a large portion of people on older browsers, this may impede their ability to use your application. One of the easiest remedies is to check users’ browsers when they visit your application and kindly remind them that newer, more secure options are available.

Another option is a "progressive web application.” This type allows your customers to interact with your application in a traditional browser. Or, if they prefer, they may run your application in a separate window akin to a traditional desktop application. Choose a progressive web application when an end-user may need to use the application but may not have a constant connection or wants to run the application in a separate window.

The final hosting option is to host your application server-side. Instead of loading an entire application into a browser, the application state remains on the server and interacts with the browser via SignalR. The main advantage of using the ASP.NET Core hosting model is that the initial application load is much smaller. Furthermore, there are no compatibility issues with older browsers because you are bypassing the requirements for WebAssembly.

The best ability is availability, and thus Blazor is great because you may choose multiple options at once. Both WebAssembly and Progressive Web App? Absolutely! You can even combine these options retroactively. Because the differences between the hosting options are mostly at the infrastructure level, your UI code can stay intact even while introducing new solutions.

Editors of Microsoft documentation. (12/07/2020) ASP.NET Core Blazor Hosting Models. [Documentation]. Retrieved from https://docs.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-5.0