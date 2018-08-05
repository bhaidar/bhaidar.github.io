In your Angular application, you have the scenario of 2 **Routes** that load the same component **ComponentA**. *ComponentA* is configured to use **ChangeDetectionStrategy.OnPush**. It checks the route data, calls a REST API to retrieve data from the server and then binds the data to a Grid or table.

- You notice that when visiting the first route, everything works just fine. The data is loaded from the server and the DOM displays the data properly.

- Later on, you click the other route, the one that's pointing to the same component, you notice that the old data is still showing on the DOM. Only a few seconds later, the new data arrives from the server and the DOM reflects that.

When navigating to the other route, the old data sticks to the DOM during the time elapse between clicking the route and receiving the new data from the server!

If you are facing such a scenario, then continue reading on to see how you can eliminate this behavior.

## Background
I assume that you have a good understanding of how **Change Detection** in Angular works. If not, I advise you to read this article [Everything you need to know about change detection in Angular](https://blog.angularindepth.com/everything-you-need-to-know-about-change-detection-in-angular-8006c51d206f) written by [Max NgWizard K](https://blog.angularindepth.com/@maxim.koretskyi). The article explains in depth how the change detection cycle runs in Angular. The two things that I want you to focus on and take away from reading this article, are:

- When Angular checks and updates the children component's input parameters.
- When Angular runs **ngOnChanges()** function for components having the **ChangeDetectionStrategy** set to **OnPush**

In addition, have a look at this article [Angular 2 Component Reuse Strategy](https://medium.com/@juliapassynkova/angular-2-component-reuse-strategy-9f3ddfab23f5) written by [Julia Passynkova](https://medium.com/@juliapassynkova?source=post_header_lockup), where she explains in depth how Angular reuses the same instance of the components to render them again in the DOM.

## The scenario in depth

The scenario at hand involves a main component, *ComponentA*, that embeds another child component, *ComponentB*, and two routes that point to *ComponentA*. This means, the two routes will load the same component. The figure below shows how they are all involved in the process.

![Figure 1: Scenario](./../../assets/2018-08-05-p1/scenario-resources.PNG)
*Figure 1: Scenario*

In summary,
- ComponentB is defined as a *basic* component with change detection strategy set to **OnPush**. 
- ComponentB defines an **@Input** to receive data from outside world.
- ComponentA is defined as a *container* component.
- ComponentA makes use of ComponentB by embedding it on the HTML side.
- ComponentB defines a variable **data** that is bound to the ComponentB input.
- Route 1 points to ComponentA. In this case, Route 1 is a link to display blogs data.
- Route 2 points to ComponentA. In this case, Route 2 is a link to display employees data.

I've provided a sample Angular app to reflect the above scenario. You can access the application on [StackBlitz](https://angular-6-use-change-detection-to-manipulate-dom.stackblitz.io).

When going through the code, you will notice that I've made use of a *basic* and *container* components by following the best practices in developing Angular apps. Sometimes they are also called dumb and smart components. If you are interested in reading more about how to organize your components in an Angular application, follow this link: [Presentational and Container Components](https://medium.com/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0).

## The problem

Run the application and **click** on *Blogs*. *ComponentA* loads, retrieves the data from the server and then binds the data to the input parameters of *ComponentB*. Figure 2 illustrates this action.

> I'm implementing a fake backend provider to simulate calling a REST API, hosted somewhere in the cloud, to retrieve some data. You may check the code for more details.

![Figure 2: First load of ComponentA with Blogs](./../../assets/2018-08-05-p1/first-run.PNG)
*Figure 2: First load of ComponentA with Blogs*

Now, **click** on *Employees* to load the employees data. 

By design, Angular **reuses** the same instances of *ComponentA* and its children components when routing to *ComponentA* multiple times. You clearly see how the problematic behavior is popping up. Figures 3 and 4 illustrates that.

![Figure 3: Second load of ComponentA with Employees](./../../assets/2018-08-05-p1/second-run.PNG)
*Figure 3: Second load of ComponentA with Employees*

Even though you've clicked on a new *Route*, *ComponentA* is still showing the old data!

![Figure 4: Data Arrives](./../../assets/2018-08-05-p1/data-arrives.PNG)
*Figure 4: Data arrives from server*

Upon receiving the new data from the server, *ComponentA* reflects them on *ComponentB* and hence on DOM.

The core problem we are facing here is the fact that the *ComponentA* keeps on holding the old data when a new *Route*, pointing also to *ComponentA* is clicked. Only when the new data arrives from the server, *ComponentA* will reset the DOM and displays the new one!

Let's have a thorough analysis on this problem from an Angular point of view. 

## Analysis on the problem

The behavior we are witnessing here doesn't necessarily mean that Angular is malfunctioning. On the contrary, Angular is doing its job perfectly. We need to adpat our code to fit the way Angular deals with change detection cycles internally.

If you've read the links above, it should be clear by now how Angular performs change detection cycles. However, I'll briefly mention the major milestones of a change detection cycle that will have a direct effect on our problem and solution, of course!

When Angular runs a change detection cycle on *ComponentA*, it will start first by locating it's children components. In this case there is only a single child component, *ComponentB*. 

Then, it updates all the input parameters on *ComponentB*. This means, Angular rebinds the values of the variables that are bound to the input parameters on *ComponentB*. This way, any changes on the local variables will be reflected on those parameters.

Angular continues on with the change detection cycle, and makes a check to see if there is any change in the values of the input parameters on *ComponentB*. If a change is detected, it will trigger **ngOnChanges()** function on *ComponentB*.

Finally, Angular renders the DOM of *ComponentB* with the new input parameter values, if there was a change, otherwise, it will render the old data.

We can easily relate the above to the problematic behavior in hand. What is really happening in our case can be summarized as follows:

1. Angular loads *ComponentA* when the *Blogs* route is clicked. This means, *ComponentB* is also rendered on the DOM.

2. *ComponentA* retrieves the data from the server, passes it to *ComponentB* via input parameters.

3. *ComponentB* render the new data onto the DOM.

4. When you click the *Employees* route, Angular **reuses** the same instances of both *ComponentA* and *ComponentB*. This means, **ngOnInit()** is not called again. Also, their *constructors* are not called.

5. *ComponentA* will send a new request to the server to retrieve the new data. This is shown in the code below.

    ````Typescript
    this.routeSubscription = this.route.params.subscribe( ( { source }) => {
          this.schemaService.getSchema(source).pipe(
            tap( (data: Schema[]) => this.schema = [... data ] ),
            switchMap( () => 
              this.service.getData(source).pipe(
                tap( (data: any) => {
                  this.data = [ ... data ];
                }
              )
            )
          )).subscribe();
        });
    ````

6. HTTP requests, in general, are *asynchronous* by nature. Hence, there is a slight delay between sending a request from the client and receiving a response from the server. Angular, by design, triggers a change detection cycle once an asynchronous event is triggered. Therefore, it will go through the same steps presented above.

7. Inside a change detection cycle, Angular checks the local variables inside *ComponentA* and notices that their values haven't change yet (still no response from server has arrived), therefore, it won't call the **ngOnChanges()** function on *ComponentB*, hence the same old data will be rendered to the DOM. This is exactly what is happening in our case!

8. When the new data arrives from the server, Angular issues another change detection cycle. In this case, the local variables are now updated inside *ComponentA*. So Angular binds those variables to the input parameters of *ComponentB*. It will notice that the input parameters have new data, hence, it will trigger the **ngOnChanges() on *ComponentB* and it will render to DOM the new data. In the demo code, you notice that when the data is received from the server, the UI refreshes and the new data is now displayed.

    ````Typescript
    this.schema = [... data ];
    this.data = [ ... data ];
    ````

## The solution

We want to reset the UI, while waiting for the new data to arrive from the server, upon clicking the second route. This means, we want to reset the local variables in *ComponentA*, instantly when the second route is clicked, so that Angular can detect a change in the input parameters of *ComponentB* and hence renders nothing on the DOM instead of the old data. 

To do so, we will hook into **Router** events collection and locate the **NavigationStart** event. When you click a route, Angular triggers some events to give a progress indication on the navigation cycle. One of those events is the *NavigationStart* event that is triggered at the beginning of the navigation.

````Typescript
this.routerSubscription = this.router.events.subscribe(route => {
  if (route instanceof NavigationStart) {
    this.schema = undefined;
    this.data = undefined;
  }
});
````

Once subscribed to the *Router* events collection, the code acts only when the event is of type *NavigationStart*. It simply clears the local variables in *ComponentA* that are bound to the input parameters of *ComponentB*.

````Typescript
@Component({
  selector: 'c-a',
  template: `<c-b [schema]="schema" [data]="data"></c-b>`,
  styles: [``]
})
export class ComponentA implements OnInit, OnDestroy  {
````

By the time the change detection cycle starts, Angular detects a change in the input parameters of *ComponentB* and hence it remove the old data from DOM and shows blank instead.

When the new data arrives, Angular triggers another change detection cycle, updates the input parameters of *ComponentB* with the new data received and finally renders them to the DOM.

![UI Reset When navigating to new Route](./../../assets/2018-08-05-p1/solution.PNG)

## Summary

Learning how a change detection cycle works in Angular, helps you find solutions to many problems that you face in your daily work. In this article, we've made use of the Router events available, to reset local variables, inside a Component, that are bound to input parameters on a child Component. This is so that the change detection would notice a change, reflects that on the DOM when a new Route is clicked and eventually re-load the same Component at hand.






