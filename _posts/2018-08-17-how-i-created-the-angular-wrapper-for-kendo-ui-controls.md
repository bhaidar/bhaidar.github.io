---
image:
  path: "/assets/2018-08-17-p1/kendoui.png"
---

Creating Angular NPM packages has never been easier with the latest release of the Angular-CLI. Before the new Angular-CLI, a long and complicated process was required to create an Angular library as an NPM package.
 
If you are planning to build your next Angular library and still haven’t checked the new features of Angular-CLI, then this article is for you!
 
I am going to show you how to wrap the Telerik Kendo UI TreeList jQuery control in an Angular component so that you can easily use the TreeList Component without having to repeat the configurations needed when importing jQuery plugins inside an Angular app.
 
Here, I have to assume you are aware of the Angular CLI and its capabilities. In case you need to learn more about it, you may follow this link: [Angular CLI on Github](https://github.com/angular/angular-cli)
 
## Install the Angular-CLI

To install the Angular-CLI, globally, on your dev machine, I recommend running the steps below, in order, using a Command / Terminal Window:
 
1. `npm uninstall -g @angular/cli`
1. `npm cache clean`
1. `npm install -g @angular/cli@latest`

Now that you have installed the Angular-CLI, let’s create our app.
 
## Create an Angular app

Run the command `ng new angular-wrapper-kendo-ui-app --style=scss`
Angular-CLI takes few seconds to generate and initialize the new app
 
The command above:

- Creates a new directory / folder with the name of `angular-wrapper-kendo-ui-app`.
- Generates the Angular project files needed
- Initializes a new `git` local repository
- Installs all NPM packages
 
Starting with Angular 6, the Angular-CLI creates a new **Workspace** of multiple projects. Let’s discover together the `angular.json` file that the CLI has created. 

The `angular.json` file replaces the old `.angular-cli.json` file for Angular 2+ apps.
 
What is notable here, in the `angular.json` file is the **projects** section.

````
"projects": {
    "angular-wrapper-kendo-ui-app": { },
    "angular-wrapper-kendo-ui-app-e2e": { }
}
````
 
The Angular-CLI creates two projects. One for the actual application to run and use. The other one is for running the tests of your application.
 
Let’s dissect the **angular-wrapper-kendo-ui-app** project:
 
 ````
 "root": "",
 "sourceRoot": "src",
 ````
 
The **root** field represents the directory / folder of the current app. An empty string means the current folder.
 
The **sourceRoot** field represents the directory / folder that holds the source code of this application.

```` 
"projectType": "application",
````
 
The **projectType** field represents the nature or type of the project. In this case, the type is an application. You will see later that we have a new project type called **library**.
 
 ````
"architect": {
"build": {
  "builder": "@angular-devkit/build-angular:browser",
  "options": {
	"outputPath": "dist/angular-wrapper-kendo-ui-app",
	"index": "src/index.html",
	"main": "src/main.ts",
	"polyfills": "src/polyfills.ts",
	"tsConfig": "src/tsconfig.app.json",
	"assets": [
      	"src/favicon.ico",
      	"src/assets"
	],
	"styles": [
  	    "src/styles.scss"
	],
	"scripts": []
  },
  "configurations": {
	"production": {
  	"fileReplacements": [
    	{
      	"replace": "src/environments/environment.ts",
      	"with": "src/environments/environment.prod.ts"
        }
  	],
  	...
	}
  }
},
"serve": {
  "builder": "@angular-devkit/build-angular:dev-server",
  "options": {
	"browserTarget": "angular-wrapper-kendo-ui-app:build"
  },
  "configurations": {
	"production": {
  	"browserTarget": "angular-wrapper-kendo-ui-app:build:production"
	}
  }
},
"extract-i18n": {
  ...
}
````  

The rest of the file is more or less similar to the content of the old `.angular-cli.json` file. Basically, they specify how to build the application, how to serve the application and how to run the unit tests of the application. In addition, under each section, some settings are configured and set.

Explaining the entire `angular.json` file is another article in itself. If you are interested, read more about the different sections involved in this file at: [Understanding the Angular CLI Workspace File]( https://nitayneeman.com/posts/understanding-the-angular-cli-workspace-file/).
 
An important point to understand here, in this context, is the new Angular-CLI creates a new Workspace with a set of projects, rather than just a single project.
 
To run and make sure the application is properly configured, run `ng serve`. You should be able to see the default Angular-CLI page up and running.

## Add an Angular library

The latest version of Angular-CLI introduced a new command to help generate a library. To create a new library, you issue the command below:

````
ng generate library angular-wrapper-kendo-ui --prefix=aku`
````

The Angular-CLI creates the new library inside the project with a prefix for all the components, inside the library to use by default **aku**. Of course, there are plenty of other options to configure when generating a new library and you may read about them here: [ng generate library]( https://github.com/angular/angular-cli/wiki/generate-library)

The new library project files have been created under the **projects/angular-wrapper-kendo-ui** folder.  

![Library Project Files](/assets/2018-08-17-p1/library-project.png)

The new library has a similar structure to a normal Angular 6 app. It has a list of configuration files that will be explained shortly. In addition, it has a **src** folder to contain the source code of the library.

The `package.json` and `tsconfig.lib.json` files function exactly the same as those present in the main Angular app.

The new addition is the `ng-package.json` file, that you use to configure the ` ng-packagr` package. This package packs and creates an NPM package out of the library. Simply ignore this file, as the Angular-CLI team have done a great job in configuring and redirecting it to the right path for the better.

## Angular Library Files
The command to generate a new library creates the library folder together with a bunch of files as shown in the figure below:

![Library Source Files](/assets/2018-08-17-p1/library-src.png)

No doubt you'll be familiar with the component and module classes and files created, as they are exactly the same as those you find and create in a normal Angular app.

However, you will notice the `public_api.ts` file. This file defines the public interface of the library. In other words, what the library exposes from services and components to the outside.

A rule of thumb, always make sure to include all components and services, to be used outside this library, in the **exports** section of the NgModule. Also, you need to export them inside the `public_api.ts` file. Exporting them in both locations helps in exposing and making the components and classes visible to the outside.

The `public_api.ts` file is as simple as:

````
/*
 * Public API Surface of angular-wrapper-kendo-ui
 */
 
export * from './lib/angular-wrapper-kendo-ui.service';
export * from './lib/angular-wrapper-kendo-ui.component';
export * from './lib/angular-wrapper-kendo-ui.module';
Simple **exports** statements, exposes any component or service or the library module to the outside.
````

With the new library created, revisit the `angular.json` file and notice that a new node has been added under the **projects** section.

````
{
   ...
   "projects": {
      	"angular-wrapper-kendo-ui-app": {
                     	...
      	},
      	"angular-wrapper-kendo-ui-app-e2e": {
                     	...
      	},
      	"angular-wrapper-kendo-ui": {
      	  "root": "projects/angular-wrapper-kendo-ui",
      	  "sourceRoot": "projects/angular-wrapper-kendo-ui/src",
      	  "projectType": "library",
      	  "prefix": "aku",
      	  ...
      	}
   },
   "defaultProject": "angular-wrapper-kendo-ui-app"
}
````

A new project node has been added under the name of **angular-wrapper-kendo-ui**. The same name that was given to the new library.
 
The **root** field now points to the root folder of the library project. The **sourceRoot** field points to the src folder of the library project. The **prefix** field takes the value that you have supplied upon creating the library, in this case it is *aku*. Finally, the **projectType** field has the value of `library`. This field indicates that this project node is a library and not a full-fledged application.

 
## Add the TreeList Component to the library

To create a new component inside the library:

1. Run the following command: `ng generate component treelist --project=angular-wrapper-kendo-ui `
    
    The command creates a new component, **TreeListComponent**, inside the *angular-wrapper-kendo-ui/src/lib* folder.
 
    > There are plenty of options to configure the `ng generate component` with. You can read more about them here: [ng generate component]( https://github.com/angular/angular-cli/wiki/generate-component).

1. First of all, let’s us export the new component to both, the library module class and the `public_apis.ts` file respectively.

    ```` 
    @NgModule({
      imports: [
      ],
      declarations: [TreeListComponent],
      exports: [TreeListComponent]
    })
    export class AngularWrapperKendoUiModule { }
    ````

1. In the `public_apis.ts` file:

    ````
    export * from './lib/tree-list.component';
    ````

    Now that we are sure the new component is available to the outside, let us focus on building the component functionality.
 
1. Locate the `package.json` file inside the library and add the following packages:
 
    ````
    "peerDependencies": {
    	…
    	"@progress/kendo-ui": ">= 2018.2.806",
    	"jquery": ">= 3.3.1"
      },
      "devDependencies": {
    	"@progress/kendo-ui": ">= 2018.2.806",
    	"@types/jquery": "^3.3.6",
    	"@types/kendo-ui": "^2018.2.3",
    	"@types/node": "~8.9.4",
    	"jquery": ">= 3.3.1"
      }
    ````
 
    Add the `@progress/kendo-ui` and `jquery` packages under the **perDependencies** section of the `package.json` file.

    The code uses a range of version numbers for the dependency packages so that the library is not limited to a specific package version.
 
1. Add the same packages as above under the **devDependencies** section. You add the same packages as above with the addition of some Typescript packages for the Kendo UI and jQuery packages so that you benefit from a free intellisense while coding!
 
1. Run the following commands to install the required dependencies:
  
    ````
    cd projects/angular-wrapper-kendo-ui
    npm install
    ````

    The command aboveinstalls all the required dependencies into a local *node_modules* folder inside the library projects.
 
1. Go back to the main application and add the same packages as above to the `package.json` file at the root of the applicztion. Make sure to add the packages under the **dependencies** section.

    ````
    "@progress/kendo-ui": "^2018.2.806",
    "jquery": "^3.3.1"
    ````

    Once again, run the `npm install` command at the application level to install all packages locally.

## Build the TreeList Component

1. Switch back to the library module file and import the the following two packages:
 
    ````
    import { NgModule } from '@angular/core';
    import { TreeListComponent } from './tree-list.component';
     
    import '@progress/kendo-ui';
    import 'jquery';
     
    @NgModule({
      imports: [
      ],
      declarations: [TreeListComponent],
      exports: [TreeListComponent]
    })
    export class AngularWrapperKendoUiModule { }
    ````

    The two packages are imported, each on a single line. When building the library, *WebPack* takes note of those two import lines and loads the corresponding libraries into the client so that they are available to be used by the library at runtime. 

1. Go back to the **TreeListComponent** and let us start building the component.
 
    In the **tree-list.component.html** file include the following HTML:
    
    ````
    <div #treelist></div>`
    ````

    The HTML markup adds a *div* element and uses a [Template Reference Variable]( https://angular.io/guide/template-syntax#ref-vars) to mark the div element with a name.

1. Back to the `tree-list.component.ts` file. Start by declaring a new variable for Kendo object to get full intellisense.

    ````
    declare var kendo: any;`
    ````

1. Define the Component *changeDetection* strategy to be: 

    ````
    changeDetection: ChangeDetectionStrategy.OnPush`
    ````

    This component defines a few input parameters. With the *changeDetection** set to *OnPush*, Angular would run a change detection cycle on this component only when the input parameters are changed. 
 
1. Change the component selector to something like: `ng-treelist`.
 
1. Add an input parameter to define the options for the TreeList control. 

    ````
    @Input()
    public options: kendo.ui.TreeListOptions;
    ````

    The component expects an input parameter named **options** of type *kendo.ui.TreeListOptions*. To keep things simple, the code is using theinterfaces already defined by Kendo UI library. This way, you maintain the same developer experience when using the Kendo UI controls inside an Angular app. 
 
1. Add a reference to the HTML div element marked previously with a template reference variable of `treelist`:

    ````
    @ViewChild('treelist')
    treelistEl: ElementRef;
    ````

1. Once the component is loaded into DOM and *View* behind the component is up and ready, you can initialize the TreeList control inside the **ngAfterViewInit** Angular life-cycle hook:
 
    ````
    ngAfterViewInit() {
        const treeList = kendo.jQuery(this.treelistEl.nativeElement);
        
        this.zone.runOutsideAngular(() => {
          this._treelist = treeList
        	.kendoTreeList(this.options)
        	.data('kendoTreeList');
        });
    }
    ````

    The code starts by getting a jQuery reference to the actual DOM element that will eventually hold the TreeList control in the DOM.
 
    The code then initializes a new Kendo UI TreeList control using the typical jQuery method and supplying the *options* passed-in as input parameters to this component. Finally, the the code saves a reference to the **kendoTreeList** DOM for future use.
 
    **The initialization of the TreeList control is done inside a zone.runOutsideAngular block.**
 
    > Angular initiates a new change detection cycle with every event triggered in the application. For instance, moving the mouse over the TreeList control, that would cause Angular to run a new change detection cycle. Do you really need Angular to run that much of change detection cycles often? Of course not! That’s why, in order for Angular not to run change detection cycles frequently, you **must** initialize the TreeList control outside the boundary context of Angular. This ensures the best performance when using not only the Kendo UI TreeList control, but also any jQuery plugin you want to wrap inside an Angular component.

1. It is recommended to destroy the whole HTML div that is hosting the TreeList control when Angular runs the Destroy event on the TreeListComponent:
 
    ````
    ngOnDestroy(): void {
        kendo.destroy(this.elementRef.nativeElement);
    }
    ````

1. Finally, you can expose any function on the TreList jQuery Control and make it public and visible to everyone. For instance, the code exposes a single function, the TreeList control `clearSelection` function:
 
    ````
    public clearSelection(): void {
        return this._treelist && this._treelist.clearSelection();
    }
    ````

1. Build the library by running the command: `ng build –prod angular-wrapper-kendo-ui`. This command builds the library and generates a build folder under the **dist** folder at the root of the application.

    >Angular-CLI already includes the required paths inside the root `tsconfig.json` file so that the application can realize and reference the TreeListComponent easily.
 
    ````
    "paths": {
      "angular-wrapper-kendo-ui": [
        "dist/angular-wrapper-kendo-ui"
      ],
      "angular-wrapper-kendo-ui/*": [
    	"dist/angular-wrapper-kendo-ui/*"
      ]
     }
    ````

1. Import the TreeListComponent to the main App. Switch back to the main app NgModule class and import the **AngularWrapperKendoUiModule**.
 
    ````
    import { AngularWrapperKendoUiModule } from 'angular-wrapper-kendo-ui';
    …
    …
    imports: [
    	BrowserModule,
    	AngularWrapperKendoUiModule
      ],
    ````

    The imports the module from the library using the library name surrounded by single quotes. Then it adds the module into the list of **imports** of the main app module. With this configuration, you have now access to the TreeListComponent anywhere in the main app.
 
1. Open the `app.component.html` file and include the component as follows:

    ````
    <ng-treelist [options]="options"></ng-treelist>
    ````
 
    The TreeListComponent requires a single input parameter called `options`. The component defines this object as follows:
 
    ````
    private options: any;
    ````
 
1. In the **ngOnInit** Angular life-cycle hook, the code creates the actual content of the `options` object.
 
    ````
    this.options = {
      dataSource: dataSource,
      height: 540,
      toolbar: ['create'],
      editable: {
    	move: true,
    	inline: true
      },
      columns: [
    	{ field: 'Position' },
    	{ field: 'Name' },
    	{ field: 'Phone' },
    	{ title: 'Edit', command: [ 'edit', 'destroy' ], width: 250,
      	attributes: {
      	    style: 'text-align: center;'
      	},
    	},
      ],
      selectable: true,
      change: ($event: kendo.ui.TreeListChangeEvent) => {
    	this.zone.run( () => {
      	console.log('Something has changed! ', $event);
    	});
      },
    };
    ````

    If you are familiar with using Kendo UI controls, you will notice there's nothnig new here! This is similar to the way you initialize and use Kendo UI controls outside Angular. Teaching you how to use Kendo UI controls is outside the scope of this article. You may learn more about the Telerik Kendo UI TreeList jQuery Control by following this link: [TreeList Widget]( https://demos.telerik.com/kendo-ui/treelist/index).
 
    The code above triggers the **change** event of the TreeList control under the context of a a **zone.run()** block. This block functions in the opposite direction to the **zone.runOutsideAngular()** block.
 
    When the user selects a row on the TreeList control, you want Angular to be aware of this change of selection event. Inside the **change** event handle, you can access any locally defined variable, update its value and manipulate it the way you want. Because of that, you want Angular to run a change detection cycle so as to catch all the changes performed inside the **change** event handler and to render updated values to the DOM accordingly.

1. Finally you run the application by issuing the `ng serve` command at the root of the application.

    ![TreeList Control in Angular](/assets/2018-08-17-p1/treelist-running-in-angular.png)
 
    The Kendo UI TreeList jQuery control functions as expected inside an Angular Component.
 
    Remember, any time you do a change on the library files, you have to build the library again before running the main application. For that reason, you can add a helper NPM script into the `package.json` file at the root folder of the application as follows:
 
    ````
    "start": "npm run build-lib && ng serve",
    "build-lib": "ng build --prod angular-wrapper-kendo-ui",
    ````

    The script above ensures that the library is built before running and serving the application.

>
You can download the entire library source code from GitHub by following this link: [Angular Wrapper Kendo UI App]( https://github.com/bhaidar/angular-wrapper-kendo-ui-app).
>

## Summary

This article serves to highlight the major steps required to build a new Angular library using the latest Angular-CLI version.

Any enquiries are welcome and I will be answering any questions you may have. Either leave a comment or email me directly.