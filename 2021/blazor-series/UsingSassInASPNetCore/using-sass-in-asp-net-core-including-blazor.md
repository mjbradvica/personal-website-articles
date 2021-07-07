# Using SASS in ASP.NET Core, including Blazor

[Sass](https://sass-lang.com/) is a css-preprocessor that allows us to create stylesheets for web application using mixins, variables, functions, and nested rules. These SASS files will then be compiled into standard css files for us to use in our application. ASP.NET Core and Blazor have the ability to integrate Sass into our the build process. This allows us to use write our stylesheets in sass first, or import and modify existing frameworks. Today, we are going to use sass to import the Bulma library, and change the primary color with just a few lines of code.

A few things are necessary first:

1) An updated version of dotnet Core

2) An updated version of Node.js

3) In Visual Studio, start a new ASP.NET Core Web or Blazor project

The first thing we need to do is make sure our tooling is correct for how Visual Studio will use Grunt.

Go to Options -> Tools -> Projects and Solutions -> Web Package Management. Make sure it looks like this:

![Correct Tooling](ide-tooling.png)

Once that is correct, we need to add a package.json to our solution because we will be using a few npm packages to handle our sass to css conversion.

In the Solution Explorer at the solution level, right-click -> Add -> New Item -> npm Configuration File

![Package.json](package-json.png)

When the file is added, open up the command prompt or Git Bash and navigate to the solution level where the package.json file is located. One of the easiest ways to do so is navigate to the location in File Explorer the right-click -> Git Bash Here

We want to add the following packages as a dev dependency and bulma as a normal dependency.

- Grunt
- Grunt CLI
- Grunt Sass
- Node Sass

You can use the following commands:

npm install bulma
npm install grunt grunt-cli grunt-sass node-sass --save-dev

Our package.json file should now look like this:

```json
{
  "version": "1.0.0",
  "name": "asp.net",
  "private": true,
  "devDependencies": {
    "grunt": "^1.4.1",
    "grunt-cli": "^1.4.3",
    "grunt-sass": "^3.1.0",
    "node-sass": "^6.0.1"
  },
  "dependencies": {
    "bulma": "^0.9.3"
  }
}
```

Your version numbers will probably be different as this post was done in the summer of 2021.

Similarly to how we added our package.json, at the solution level, right-click -> Add New Item -> Choose a javascript file and name it "gruntfile.js"

![Add Gruntfile](add-gruntfile.png)

Add the following contents to your new gruntfile:

```javascript
/// <binding AfterBuild='default' />

module.exports = function (grunt) {

    var sass = require('node-sass');

    grunt.initConfig({

        sass: {
            options: {
                implementation: sass,
                sourceMap: true,
                outputStyle: 'compressed'
            },
            dist: {
                files: [
                    {
                        expand: true,
                        cwd: "Styles",
                        src: ["**/*.scss"],
                        dest: "wwwroot/css",
                        ext: ".css"
                    }
                ]
            }
        }
    });

    grunt.loadNpmTasks('grunt-sass');

    grunt.registerTask('default', ['sass']);
};
```

This will tell the pre-processor where to look for our sass files, and where to compile them to. Currently, we are saying our sass files will be located in a folder called "Styles" that will exist at the same level as our package.json file, or the solution level. We will output the compiled css to the css folder located in the wwwroot. The binding at top says that our pre-process will run after every build.

Note: Your sass files can not be in the wwwroot folder, that is reserved for serving up static files only.

Add your "Styles" folder, or whatever you wish to call it. Create a sass file inside of it. Here is what it looks like at the solution level:

![Solution](solution.png)

I am going to change a the primary color in bulma from its default of turquoise to something different. This post is not concerned with bulma, but I am using it as changing a color is a common use case.

```scss
@charset "utf-8";

// Define a variable.
$orchird: #AF69EF;

// Update bulma's primary color
$primary: $orchird;

// Import the rest of bulma
@import "../node_modules/bulma/bulma";

```

All we have done is updated the primary color and imported the rest of the library.

Now we just build our solution and our color.css file should be generated.

Note: I had to unload and reload the solution to get Visual Studio to fully recognize the gruntfile.

Our color.css file being generated:

![Generated file](generated-file.png)

All we have to do is import our new css file into our application and test it out:

Our index.html with our css import:

```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
    <title>SassBlazor</title>
    <base href="/" />
    <link href="css/color.css" rel="stylesheet" />
</head>

<body>
    <div id="app">Loading...</div>
<script src="_framework/blazor.webassembly.js"></script>
</body>

</html>
```

And our Index page to look at our update:

```html
@page "/"

<h1>Hello, world!</h1>

<p class="has-text-primary">Welcome to your new app.</p>
```

And lastly our final result with a new primary color:

![Result](result.png)

I would like to thanks Mr. Colin Bacon for providing the inspiration and skeleton for this article. His blog post was published in 2017, but has since been outdated from new versions of the npm packages. This article was an exercise in getting sass operational in the newest versions of dotnet core, node, and trying to solve a common use case. You can read his blog [here](https://www.iambacon.co.uk/blog/how-to-use-sass-in-asp-net-core-2-0-mvc).