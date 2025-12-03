# Angular – Basics

## 1. What is Angular?

Angular is a TypeScript-based frontend framework used to build single-page applications (SPAs).  
It provides features like components, dependency injection, routing, forms, and HTTP communication.

**Key points:**

- Component-based architecture  
- Two-way data binding with `[(ngModel)]`  
- Built-in dependency injection  
- Routing module for navigation  
- Reactive forms and template-driven forms  

**Example – Simple Angular Component**

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-hello',
  template: `<h1>Hello {{ name }}</h1>`
})
export class HelloComponent {
  name: string = 'Angular';
}

