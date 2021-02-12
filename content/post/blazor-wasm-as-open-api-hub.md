---
author: "Mandar Dharmadhikari"
title: "Building Open API document Hub with Blazor Webassembly "
description : "Primer on integrating blazor with open api java script"
date: "2021-02-14"
tags : [
    "Blazor",
    "Open API"
]
draft : "true"
featured : true
thumbnail : "images/blazor-wasm-as-open-api-hub/feature.png"
---

In last year, I have been exploring Blazor Webassembly framework in my free time. I absolutely love the idea of writing Single Page Applications using C# ! What an awesome thought. Blazor also supports executing the Javascript functions from the C#.

In one of engangements with one of my clients, I came across an implementation where they built a react app to host their multiple open api documentation. The SPA acted as a one stop shop for listing all the APIS they had in their environment.

Today, in this article I will try to mimick the same behavior by creating a SPA in the Blazor Webassembly.

## Prerequisites
To develop this SPA we need few things.

1. Visual Studio 2019 or Visual Studio Code installed with the Omnisharp.
2. Dotnet core 3.1 or .Net 5.0 installed.
3. Enable npm in our blazor application(We will come to it in some time.)

And that is it, we can start the work now.

The first step is to create the blazor project. I will head over to the Visual Studio. Visual Studio has out of the box template to create the blzor webassembly application. Visual studio allows us to select whole lot of parameters in GUI way while creating the blazopr application. Following screenshot detail the process.

![Select Template](/images/blazor-wasm-as-open-api-hub/blazortemplate.PNG)

![Name the project](/images/blazor-wasm-as-open-api-hub/projectname.PNG)

![Configure parameters](/images/blazor-wasm-as-open-api-hub/projectname.PNG)

We can create the same project using the dotnet CLI. All we need to do is 

``` shell
dotnet new blazorwasm --n Blazor.OpenAPI.Hub

```
and we have our project created. Visual studio gives the ability to add a solution and then add multiple projects to it if required.

The awesome people managing the swagger repositories have a javascript based npm package called [`SwaggerUI`](https://github.com/swagger-api/swagger-ui) now I will enable npm on our blazor application so that it can use this awesome npm package.

We add a new folder in the blazor application where we will initilaize the npm. We will crate a folder called NpmJS

![Create folder for npm](/images/blazor-wasm-as-open-api-hub/createnpmfolder.PNG)

After opening the command prompt in this folder, all we need to do is initialize the npm

``` shell

npm init -y

```
We will now install a javascript bundler here `Webpack` so that we can get the swagger ui functions for us to call.

```shell

 npm install webpack webpack-cli --save-dev

```
Next we add the index.js file which we will later on bundle into a javascript file inside the wwwwroot folder of the blazor application. The directory looks like following.

![NPM Configured](/images/blazor-wasm-as-open-api-hub/npmconfigured.PNG)

Now we need to tell webpack to bundle all the scripts. We will do this using the npm build scripts. We do that by adding a build script in the `scripts` section of `packages.json` file. The build command looks like following.

```json
{
    "scripts":{
        "build": "webpack ./src/index.js -o ../wwwroot/javascript --output-filename index.bundle.js"
    }

}
```
Visit [Webpack Command Line Doco](https://webpack.js.org/api/cli/)

Now finally we install the npm package for `SwaggerUI` by executing following command in our NpmJS folder.

```shell

npm install swagger-ui

```

That finishes the part to add the `swagger-ui` npm package. Now we need to ensure that every time our blazor project is built, the npm packages are built befor the project is built. We can leverage the `Pre Build Events` to configure this. We can do it by adding command to the `PreBuild` target of the `.csproj` file.

```xml

<Exec Command="npm install" WorkingDirectory="NpmJS" />
<Exec Command="npm run build" WorkingDirectory="NpmJS" /

```

This ensures that whenever we build our blazor app, the webpack bundles the javascript. Once we build the project we can see the `index.bundle.js` file in the javascript folder under wwwroot.

![Javascripts bundled](/images/blazor-wasm-as-open-api-hub/indexbunldejscreated.PNG)

We need to add the reference to this `index.bundle.js` file to the `index.html` file. It can be done by adding following tag in the body.

```html
<script src="javascript/index.bundle.js"></script>

```


