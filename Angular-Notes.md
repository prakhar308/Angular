# Angular

A framework for building **Single Page Applications (SPA)** using **HTML** and **TypeScript**.

The architecture of an Angular application relies on certain fundamental concepts.
These are:

- [NgModules](#NgModules)
- [Components](#Components)
- [Data Binding](#Data-Binding)
- [Structural Directives](#Structural-Directives)
- [Custom Pipes](#Custom-Pipes)
- [Nested Components](Nested-Components)
- [Service and Dependency Injection](#Service-and-Dependency-Injection)


NgModules
---

- Provide a compilation context for components
- Describes how the application parts fit together
- Every application has atleast one Angular Module, the *root module*, which must be present for bootstrapping the application on launch. By convention and by default, this NgModule is named **`AppModule`**.
- The default AppModule looks like this:

```js
/* JavaScript imports */
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

/* the AppModule class with the @NgModule decorator */
@NgModule({
  declarations: [
    AppComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

The `@NgModule` decorator identifies `AppModule` as an `NgModule` class. `@NgModule` takes a metadata object that tells Angular how to compile and launch the application. 
- ***declarations*** - tells Angular which components belong to that module
- ***imports*** - import BrowserModule to have browser specific services like DOM rendering.
- ***providers*** - the service providers.
- ***bootstrap*** - the root component that Angular creates and inserts into the `index.html` host web page.


Components
---

Components are what we see on the web page. They define **views**.

Example:
```js
import { Component } from '@angular/core'

@Component({
   selector: 'pm-root',
   template: `
      <div>
         <h1>{{pageTitle}}</h1>
      </div>
   `
})
export class AppComponent {
   pageTitle: string = "Hello. Welcome to Xavier's school for gifted youngsters ";
}
```

- We create **class** for creating a component and use decorator `@Component` to tell Angular that this class is a component.
- The metadata pass to the `@Component` includes the HTML or a link to a HTML file for the component's **template**. It also includes a *selector* property. It represents component's name in HTML.
- In the class we define *data properties* for the component.
- Write component *Logic* in class *methods*.

For using component as directive:
  - Use the directive(selector property of component) as a HTML element.
  - Add the component in the modules declarations array so that it is avaialble to any template associated with this module.


Data Binding
---

**Property Binding - []**
- From class to template
- interpolate values in HTML - {{ class_property_name }}
- bind an element's property to a class property
Example
`<img [src]="product.imageUrl">`

**Event Binding - ()**
- From template to class
- Call a class method on button click
Example
`<button (click)="toggleImage()"></button>`

**Two way binding - [()]**
- That changes in the DOM, such as user choices, are also reflected in your program data.
Example `<input [(ngModel)]="listFilter"/>`
- Make **ngModel** visible to any component declared by AppModule by importing `FormsModule` (which exposes the ngModel directive)


Structural Directives
---
- Prefix with an asterisk *

**ngIf**
- Display the DOM element if expression evaluate to True else don't.
Example
`<table *ngIf="products && products.length">`

**ngFor**
- Define local variable with *let*
Example
`<tr *ngFor="let product of products"></tr>`


Custom Pipes
---
We can create our own custome pipes using which we can transform bound properties before display.
Steps:
1. Create a class that implements `PipeTransform`.
2. Add transform logic in `transform` method. 
3. Use decorator `@Pipe` and metadata will be `name` property which is the name of the custome pipe
4. Add the custom pipe in the *declarations* array of an Angular module which tells Angular where to find the custom pipe. Now any template of a component which is associated with that Angular module can use the custom pipe.

Example:
```js
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
   name: 'convertToSpaces'
})
export class ConvertToSpacesPipe implements PipeTransform {
   
   transform(value: string, character: string): string {
      return value.replace(character, ' ');
   }

}
```


Nested Components
---
- It makes sense to make new nested component if it only manages a fragment of a large view.
- The outer component is called **Container**. The inner component is called **nested** or **child component**.
- Nested components helps in keeping the template and logic for a feature **encapsulated** and **resuable**.
- Send data from container to nested -> **Input properties**
- Send data from nested to container -> **Raising events**

- **Using a Nested Component**:
  1. just create a new component
  2. then use it as a directive in the container's template
  3. add it in the declarations array of the module to which the container component belongs.


- **Passing Data to a Nested Component using `@Input`**:
  
  1. In the nested component, mark the property that you want to be passed from the container with `@Input` decorator.
  ```js
  @Input() rating: number;
  ```
  `ngOnChages` only watches changes to input properties.
  
  2. Next in the container component pass the data by setting this property with property binding
  ```js
  <pm-star [rating]="product.starRating"></pm-star>
  ```

- **Passing Data from a Nested Component using `@Output`**:
  
  1. Mark the property in the nested component with `@Output`. The property type must be an *Event* because the only way to pass data to the container is through an event.
  ```js
  @Output() notify: EventEmitter<string> = new EventEmitter<string>();
  ```
  Here *string* represents type of the *payload/data* that is passed to the container with the event.
  
  2. Next emit an event from the nested component.
  ```js
  this.notify.emit('data to be passed')
  ```

  3. In container we bind this notify event with event binding and call a method.
  ```js
  <pm-star [rating]="product.starRating"
           (notify)="onNotify($event)"></pm-star>
  ```
  Here *$event* represents the payload and *onNotify* represents container class method.


Service and Dependency Injection
---
- Components should not contain logic that is not related to the view. Example: accessing data from a backend service. Plus if we want to share this data or logic across multiple components then how do we do it? We use **Services**

**What is a Service**
- Service is a separate class with focued purpose.
- Helps in:
   + providing shared data or logic across components
   + encapsulating external interactions - data access from a DB or through internet


*After creating a service class we can use that service by creating its object inside any component. But this would make the scope of that object local to the component. We won't be able to share this object or data.*

**Solution**
We register this service with Angular and ask it to create a single object of this service and share it across components. Specifically Angular provides a built-in **Injector** to perform the task of maintaining and creating service objects/instances and **injecting** them into the **dependent** components. This is called as **Dependency Injection**.


**Steps**

**1. Build a service:**
   + create the service class
   + define metadata with a decorator - `@Injectable`
   + import what we need

Example

```js
import { Injectable } from '@angular/core'

@Injectable()
export class ProductService {
   getProducts(): IProduct[] {

   }
}
``` 

**2. Register the service:**

Two options:

   1. Register service with **`Root Injector`** - avaialble to all the components in the application. 
   Specify it in the Injectable decorator's metadata
   ```js
   @Injectable({
      providedIn: 'root',
   })
   ```

   2. With a spcefic components
   Specify it in the @Component decorator's metadata in the component
   ```js
   @Component({
      providers: [ProductService]
   }) 
   ```

**3. Inject the service:**

Define it as a dependency in the **`constructor`** of the component class. Pass the service's instance as a parameter to the constructor.
```js
constructor(private productService: ProductService) {}
```
Whenever the component class is instantiated, the Injector will assign this parameter(productService) with the instance of the ProductService class.
 