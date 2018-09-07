---
image:
  path: "/assets/2018-08-22-p1/windowsazure.jpg"
---

In [Part I](https://www.bilalhaidar.com/2018/09/create-a-web-app-on-the-windows-azure-portal) of this series on creating and hosting an ASP.NET Core app on Azure, you've learnt how to create a Web App container using the Azure Portal. 

This article, will take you through the process of creating an ASP.NET Core app using the .NET Command-line Interface tools. You will also learn how to publish your Web app to Azure using Git.

## Prerequisites
Assuming you are familiar with ASP.NET Core and Git we can proceed. If not, I recommend the official documentation for both ASP.NET Core and Git. 

To access the Microsoft official documentation on ASP.NET Core, follow this link: [Introduction to ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/).

To access the Git official documentation, follow this link: [Git](https://git-scm.com/doc).

In addition, you need to have a valid Azure subscription.

## .NET Command-line Interface (CLI) tools

The .NET Command-line Interface tools, also known as .NET Core CLI, is a cross-platform tool that provides commands to create, restore packages, building, running and publishing .NET applications from the command line without the need for a full featured IDE.

The .NET Core CLI is installed as part of the .NET Core SDK.

To start using the .NET CLI you need to install the relevant .NET Core SDK, keeping in mind which medium you will use to develop your app; Windows, macOS and Linux. Go to [.NET Downloads](https://www.microsoft.com/net/download?initial-os=linux) and grab the .NET Core SDK.

## Exploring the .NET Core CLI

Open a command-prompt and run the following command:

`dotnet --help`

With this command, you get detailed documentation on how to use the .NET Core CLI tool. The output below has been stripped down to show only the SDK commands that are most relevant for this article.

````
...
Usage: dotnet [sdk-options] [command] [command-options] [arguments]

...
Execute a .NET Core SDK command.
SDK commands:
  ...
  new               Create a new .NET project or file.
  publish           Publish a .NET project for deployment.
  restore           Restore dependencies specified in a .NET project.
  run               Build and run a .NET project output.
  test              Run unit tests using the test runner specified in a .NET project.
  ...


Run 'dotnet [command] --help' for more information on a command.
````

The SDK Commands are self-explanatory. In case you need more information on the .NET Core CLI or how to use it, you can always refer to the official documentation here: [.NET Core command-line interface (CLI) tools](https://docs.microsoft.com/en-us/dotnet/core/tools/?tabs=netcore2x)

## Create a new MVC app

We will start by creating a new ASP.NET Core MVC application. The `dotnet new` command will be used for this purpose.

Open a command prompt and run the following command: `dotnet new --help`. Once again, you can see a detailed documentation on how to use this SDK command.

````
Usage: new [options]

Options:
  -h, --help          Displays help for this command.
  -l, --list          Lists templates containing the specified name. If no name is specified, lists all the templates.
  -n, --name          The name for the output being created. If no name is specified, the name of the current directory is used.
  -o, --output        Location to place the generated output.
  -i, --install       Installs a source or a template pack.
  -u, --uninstall     Uninstalls a source or a template pack.
  --nuget-source      Specifies a NuGet source to use during install.
  --type              Filters templates based on available types. Predefined values are "project", "item" or "other".
  --force             Forces content to be generated even if it would change existing files.
  -lang, --language   Filters templates based on language and specifies the language of the template to create.


Templates                                         Short Name         Language          Tags
----------------------------------------------------------------------------------------------------------------------------
Console Application                               console            [C#], F#, VB      Common/Console
Class library                                     classlib           [C#], F#, VB      Common/Library
Unit Test Project                                 mstest             [C#], F#, VB      Test/MSTest
NUnit 3 Test Project                              nunit              [C#], F#, VB      Test/NUnit
NUnit 3 Test Item                                 nunit-test         [C#], F#, VB      Test/NUnit
xUnit Test Project                                xunit              [C#], F#, VB      Test/xUnit
Razor Page                                        page               [C#]              Web/ASP.NET
MVC ViewImports                                   viewimports        [C#]              Web/ASP.NET
MVC ViewStart                                     viewstart          [C#]              Web/ASP.NET
ASP.NET Core Empty                                web                [C#], F#          Web/Empty
ASP.NET Core Web App (Model-View-Controller)      mvc                [C#], F#          Web/MVC
ASP.NET Core Web App                              razor              [C#]              Web/MVC/Razor Pages
ASP.NET Core with Angular                         angular            [C#]              Web/MVC/SPA
ASP.NET Core with React.js                        react              [C#]              Web/MVC/SPA
ASP.NET Core with React.js and Redux              reactredux         [C#]              Web/MVC/SPA
Razor Class Library                               razorclasslib      [C#]              Web/Razor/Library/Razor Class Library
ASP.NET Core Web API                              webapi             [C#], F#          Web/WebAPI
global.json file                                  globaljson                           Config
NuGet Config                                      nugetconfig                          Config
Web Config                                        webconfig                            Config
Solution File                                     sln                                  Solution

Examples:
    dotnet new mvc --auth Individual
    dotnet new classlib --framework netcoreapp2.1
````

All the options available to use with the `dotnet new` command are listed at the top. In addition, all available templates that you can create with that command are then listed with an explanation for each template. Finally, an example on how to use the command is shown at the end.

For our scenario, we will be creating an ASP.NET Core MVC app. Therefore, you need to run the following command:

`dotnet new mvc --name "AnAppADay" `

The command above outputs the following:

````
The template "ASP.NET Core Web App (Model-View-Controller)" was created successfully.
This template contains technologies from parties other than Microsoft, see https://aka.ms/aspnetcore-template-3pn-210 for details.

Processing post-creation actions...
Running 'dotnet restore' on AnAppADay\AnAppADay.csproj...
  Restoring packages for D:\my-apps\AnAppADay\AnAppADay.csproj...
  Generating MSBuild file D:\my-apps\AnAppADay\obj\AnAppADay.csproj.nuget.g.props.
  Generating MSBuild file D:\my-apps\AnAppADay\obj\AnAppADay.csproj.nuget.g.targets.
  Restore completed in 4.97 sec for D:\my-apps\AnAppADay\AnAppADay.csproj.

Restore succeeded.
````

This gives you enough verbose information about the creation process of the app.

Now that the application is created locally, let's run it!

To run the application, navigate to the root folder of the app and run the following command: `dotnet run`.

The command above generates the following output:

````
D:\my-apps\AnAppADay>dotnet run
...
Hosting environment: Development
Content root path: D:\my-apps\AnAppADay
Now listening on: https://localhost:5001
Now listening on: http://localhost:5000
...
````

Open a browser and navigate to either URL (Https or Http). The default ASP.NET Core MVC template is displayed.

![Default Template](/assets/2018-09-10-p1/default-template.png)

## Add Git 

Now that the application is created locally, let's add Git to the mix!

### Initialize a new local repository

Before you can initialize a local Git repository, you need first to install Git locally. If you need help installing Git locally, you may follow this link: [Install Git](https://www.atlassian.com/git/tutorials/install-git).

Once Git is installed, navigate to the application root folder and run the following commands:

````
git init
````

The command above initializes a new local Git repository.

````
Initialized empty Git repository in D:/my-apps/AnAppADay/.git/
````

### Stage Web App files

To inform Git about your web app files, you need to add all the files to the **Working Directory** of Git.

````
git add .
````

The command above adds all files in the web app folder to the staging state of Git.

### Commit the changes

The next step is to commit your changes (the files that were staged above) to the **Git Commit History** on your local machine. This is an essential step to let Git know what files are needed to be pushed later on to a remote Git repository.

````
git commit -m "Initial create".
````

All the Web app files are now committed to Git.

### Add Remote for the local Git repository

Before you can push your Web app to Azure, you need to add a **Git Remote** for the local Git repository. A Git remote specifies the remote Git repository that we want to sync our files with, whether push or pull.

To add a Git remote, first you need to locate the Git repository that you have created on Azure Portal in [Part I](https://www.bilalhaidar.com/2018/09/create-a-web-app-on-the-windows-azure-portal) of this series. To do so:

1. Login to Azure Portal
1. Navigate to the **AnAppADay** Web app blade
1. Locate and click on the **Deployment slots** on the left-side menu
1. Click the **anappaday-staging** deployment slot
1. Copy the **Git clone url** on the staging blade.

    ![Staging Blade](/assets/2018-09-10-p1/staging-blade.png)

1. Run the following command to add a Git remote:

    ````
    git add remote origin https://anappaday-user@anappaday-staging.scm.azurewebsites.net:443/AnAppADay.git
    ````

    The command above hooks your local Git repository with the remote Git repository on Azure.

1. To verify that the remote has been added successfully, run the following command:

    ````
    git remote -v
    ````

    The output shall be something like:

    ````
    origin  https://anappaday-user@anappaday-staging.scm.azurewebsites.net:443/AnAppADay.git (fetch)
    origin  https://anappaday-user@anappaday-staging.scm.azurewebsites.net:443/AnAppADay.git (push)
    ````

Sweet! The local Git repository is now properly connected to the Git repository on Azure.

## Push the App to Azure using Git

The next step is to push the Web app to Azure. To do so, you need to run the following command:

````
git push origin master
````

You will be prompted to enter the password that you have configured in [Part I](https://www.bilalhaidar.com/2018/09/create-a-web-app-on-the-windows-azure-portal) of this series while setting the **Deployment credentials**.

Git starts uploading your committed files to Azure remote Git repository configured under **Staging deployment slot**. Remember, we are pushing the Web app to the staging deployment slot, that we have already created in [Part I](https://www.bilalhaidar.com/2018/09/create-a-web-app-on-the-windows-azure-portal).

## Verify the App is Running on Azure 

To verify the app is running on Azure, follow the steps below:

1. Login to Azure Portal
1. Navigate to the **AnAppADay** Web app blade
1. Click the **Deployment slots** on the left-side menu
1. Locate and click on the **anappaday-staging** deployment slot
1. Click the **URL** on the staging blade

    ![Staging App URL](/assets/2018-09-10-p1/staging-app-url.png)

Voila! You have successfully pushed your local Web app to Azure!

## Swap Slots

As mentioned previously, we've pushed the Web app to the staging deployment slot. Once tested and you are happy with the results, it is time to swap the staging with the production deployment slot.

To do so, follow the steps below:

1. Login to Azure Portal
1. Navigate to the **AnAppADay** Web app blade
1. Locate and click the **Deployment slots** on the left-side menu
1. Click on the **Swap** button at the top of the blade.

    ![Swap](/assets/2018-09-10-p1/swap.png)

    You select **staging** as the source. For the destination, you select **production**. Meaning that you want to swap the content of the staging deployment slot with the content of the production deployment slot.

This results in the Web app that we push to the staging deployment slot is now swapped and hosted for production. The default Azure page that was previously placed in the production deployment slot, is now swapped and moved to the staging deployment slot.

To verify that, navigate to: [https://anappaday.azurewebsites.net/](https://anappaday.azurewebsites.net/) to see our Web app. Also, navigate to [https://anappaday-staging.azurewebsites.net/](https://anappaday-staging.azurewebsites.net/) to see the default Azure page (previously hosted under production).

## Summary
And with that, we complete the process of creating and building ASP.NET Core apps on Windows Azure. 

Hope you enjoyed the series and gained something out of it! Please feel free to contact me for further information if need be. 
