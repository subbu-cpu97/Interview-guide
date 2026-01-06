# Angular – Basics

## What is Angular?

Angular is a front-end framework created by Google for building modern web applications. It helps developers create single-page applications (SPAs) that update the content dynamically without reloading the entire page. Angular uses TypeScript and follows a component-based architecture, making code easier to manage, test, and scale.

## What are the building blocks of Angular?

Angular applications are built using several core elements that define their structure and behavior.  
**Modules:** Containers that organize components, directives, and services.  
**Components:** Define the structure and logic of different sections of an application.    
**Templates:** HTML layouts that display data dynamically.    
**Directives:** Instructions to manipulate DOM elements.    
**Services:** Handle data fetching and business logic.    
**Routing:** Enables navigation between different views.    

## What is Virtual Scrolling in Angular?

Virtual scrolling is a technique used in Angular to improve performance when displaying large lists of data. Instead of rendering all items at once, Angular dynamically loads only the items that are currently visible in the viewport.Virtual scrolling helps in reducing memory usage and speeds up the page load by rendering elements only when needed.

Example of virtual scrolling in Angular
```ts
 <cdk-virtual-scroll-viewport itemSize="50">
   <div *cdkVirtualFor="let item of items">{{ item }}</div>
 </cdk-virtual-scroll-viewport>    
 ```

 ## How do you create a drag-and-drop feature in Angular?

Angular provides built-in drag-and-drop functionality using the Angular CDK (Component Development Kit). This feature allows users to reorder lists or move elements using simple drag operations.    
Steps to implement Drag and Drop.    
Import DragDropModule from Angular CDK.    
Use the cdkDropList directive on a container.    
Use cdkDrag on draggable elements.    
Handle the drop event to update the data.   


Example code.   

```ts
 <div cdkDropList (cdkDrop)="drop($event)">
   <div cdkDrag>Item 1</div>
   <div cdkDrag>Item 2</div>
 </div>    
```

## What is the Ivy Renderer engine in Angular?

Ivy is the new rendering engine introduced in Angular to improve performance and reduce bundle sizes. It replaces the older View Engine.   

Key Benefits of Ivy
  - Faster build times and compilation.    
  - Reduces the size of Angular applications, making them load faster.    
  - Improves debugging with better error messages.   
  - Allows lazy loading of components to enhance performance.    
  - Supports dynamic component creation without extra configuration.

 ## What is the Budget Bundle feature in Angular?

The Budget Bundle feature in Angular helps developers monitor the size of JavaScript bundles to keep applications lightweight. Large bundle sizes can slow down page loading and affect performance. Angular allows budget limits to be set in the angular.json file. If the bundle size exceeds the limit, Angular will show warnings or errors.

Example of setting bundle size limits

```ts
 "budgets": [
   {
     "type": "initial",
     "maximumWarning": "2mb",
     "maximumError": "5mb"
   },
   {
     "type": "anyComponentStyle",
     "maximumWarning": "6kb"
   }
 ]   
```
##  What is a Component in Angular?

A component is the main building block of an Angular application. It controls a specific section of the user interface (UI) and     
consists of three key parts:   
template (HTML),    
 class (TypeScript), 
 and styles (CSS).  

## What is Data Binding in Angular?
Data binding is a way to connect the UI with the component’s logic so that changes in the data automatically update the interface. It helps in handling dynamic content and user interactions efficiently.   
**Interpolation** ({{ }}): Displays component variables in HTML.   
**Property Binding** ([ ]): Binds HTML element properties to component variables.  
**Event Binding** (( )): Responds to user actions like clicks or keystrokes.  
**Two-way Binding** ([(ngModel)]): Synchronizes data between the UI and the component.   


## What is the difference between Structural and Attribute Directives?

Directives in Angular help modify elements' behavior and structure.   Structural directives affect the layout and structure of the DOM, whereas attribute directives change the appearance or behavior of elements.
  - Structural Directives (*ngIf, *ngFor, *ngSwitch): Modify the DOM by adding or removing elements.     
  - Attribute Directives ([ngClass], [ngStyle]): Change the styling or behavior of existing elements.

## What are Angular Lifecycle Hooks?

Angular provides lifecycle hooks to allow developers to run code at different stages of a component’s lifecycle, from creation to destruction.    
**ngOnInit():** Runs after the component is initialized.    
**ngOnChanges():** Runs when input properties change.  
**ngOnDestroy():** Runs when the component is about to be removed.   

## How does Change Detection work in Angular?
Change detection in Angular keeps the UI updated when data changes. It checks for modifications in the component’s data and updates the view accordingly.
  - Angular uses a zone-based change detection system.
  - Developers can use ChangeDetectorRef for manual control.
  - There are two modes: Default (automatic updates) and OnPush (updates only when inputs change).

## What is a Module in Angular?

An Angular module is a container that helps organize and manage different parts of an application. It groups related components, directives, services, and pipes into a single unit, making development easier by keeping the code structured and reusable.
  - It improves code maintainability by breaking down an application into   smaller, independent parts.
  - It allows lazy loading, which helps improve performance by loading modules only when needed.
  - Every Angular application must have at least one module, called the root module (AppModule).

## What is the difference between the Root Module and the Feature Module?

The Root Module is the main module of an Angular application, while Feature Modules help divide the application into separate functional parts. Feature modules allow better reusability, scalability, and code organization.  

| **Factor**   | **Root Module (AppModule)**          | **Feature Module**                                           |
| ------------ | ------------------------------------ | ------------------------------------------------------------ |
| Purpose      | Bootstraps the entire application    | Encapsulates a specific feature of the application           |
| Scope        | Available throughout the application | Limited to a specific feature or module                      |
| Imports      | Imports all required modules         | Imports only necessary dependencies for its feature          |
| Reusability  | Not reusable in other applications   | Can be reused in multiple applications                       |
| Lazy Loading | Always loads at application start    | Can be lazily loaded for better performance                  |
| Declaration  | Declared in `app.module.ts`          | Declared in its feature module file (e.g., `user.module.ts`) |


## What is Lazy Loading in Angular Modules?

Lazy Loading is a performance optimization technique in which Angular loads feature modules only when they are needed. Instead of loading all modules at once, it helps reduce the initial load time and improves the application's speed.

lazy load  Example
~~~ts
  const routes: Routes = [
        { path: 'users', loadChildren: () => import('./user/user.module').then(m => m.UserModule) }
  ]; 
~~~   

## What are the multiple NgModule metadata properties?

The @NgModule decorator provides metadata that defines the structure of an Angular module. These properties help Angular understand how to organize and use different parts of the module.  
**declarations:** Defines the components, directives, and pipes used in the module.    
**imports:** Specifies other modules on which this module depends.  
**providers:** Registers services that should be available in the module.   
**bootstrap:** Specifies the main component that Angular should load first.
**exports:** Makes certain components, directives, or pipes available to other modules.


## What is the purpose of the Shared Module in Angular?

A Shared Module stores and reuses common components, directives, and pipes across multiple feature modules. It helps reduce code duplication and keeps the project clean.
  - It includes commonly used UI components like buttons, modals, and form inputs.
  - It is imported into multiple feature modules to provide shared functionality.
  - It does not contain services; instead, services should be placed in the Core Module.


## What are Providers in Angular?
 In Angular, Providers define and configure dependencies that can be injected into different parts of the application. They are a key part of Angular's Dependency Injection system, which helps manage and share services efficiently.
  - Providers define how services are created and made available to different components or modules.
  - They can be registered in the provider array of a module, component, or directive.
  - Using providedIn: 'root' ensures that a service is available globally without needing to be added to the providers array.

Example of a service with a provider

~~~ts
 @Injectable({
     providedIn: 'root'
 })
 export class DataService {
    constructor() { }
 }

 ~~~

 