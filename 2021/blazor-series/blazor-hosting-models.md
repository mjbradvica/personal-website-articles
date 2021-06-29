# Blazor Hosting Models

Blazor is flexible in that it gives you three different ways of hosting your application. Hosting in this sense is different from a traditional web application. I am not talking about hosting in the sense of using a cloud service or having a server farm in a physical location. Hosting as Blazor is concerned revolves around where the web application will live.

The wonderful thing about all three options is that your code stays the same. Your decision is mostly concerned with how the application will be delivered to the client and their capabilities of having a constant connection. The three hosting methods are via WebAssembly, a "progressive" web application, and ASP.NET Core hosted.

The default WebAssembly application are for end-users who you know have browsers that support WebAssembly. This is almost all modern browsers such as Firefox, Chrome, and Edge. If your customer base has a large portion of people on older browsers, this may impede their ability to use your application. One of the easier ways to remedy this is by checking their browser when they visit your application, and gently reminding them their are newer, more secure options available.

Another option to choose from is a "progressive" web application. This allows your customers the option of interacting with your application in a traditional way through the browser. Or they may run it in a separate window akin to a traditional desktop application. Choose a progressive web application when an end-user may need to use the application but may not have a constant connection or wants to run the application in a separate window.

The final hosting option available is to have the application hosted server side. Instead of the entire application loading into the browser, the application state remains on the service and interacts with the browser via SignalR. The main advantage of using the ASP.NET Core hosting model is that the initial application load is much smaller, and there are no compatibility issues with older browsers because you are bypassing the requirements for WebAssembly.

Perhaps the best option you have between these different hosting models is that you can choose multiple options. WebAssembly and Progressive Web App? Absolutely.

Blazor is very flexible with how you want your application to service users. It lets you choose from multiple hosting options, with the ability to combine them, even retroactively. Don't stress out if you get it wrong the first time either. Because the differences between the hosting options are mostly at the infrastructure level, your UI code can stay intact while your move to a more appropriate solution.

Editors of Microsoft documentation. (12/07/2020) ASP.NET Core Blazor Hosting Models. [Documentation]. Retrieved from https://docs.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-5.0