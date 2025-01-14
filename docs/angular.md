### Notes on Core Angular Concepts

#### 1. **Architecture Basics: Component-Based Architecture**
Angular applications are built using a **component-based architecture**. This means that the application is divided into small, reusable, and self-contained components. Each component manages a specific part of the UI and can communicate with other components.

- **Structure of an Angular Application:**
  - **Root Component:** The main component that bootstraps the application (usually `AppComponent`).
  - **Feature Components:** Components that represent specific features or sections of the application.
  - **Services:** Used to share data and functionality across components.
  - **Modules:** Used to organize the application into cohesive blocks of functionality.

**Example:**
```typescript
// app.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `<h1>Welcome to {{ title }}!</h1>`
})
export class AppComponent {
  title = 'My Angular App';
}
```

#### 2. **Modules: NgModule**
Angular applications are modular, and **NgModule** is the decorator used to define a module. A module is a container for a cohesive block of functionality dedicated to an application domain, a workflow, or a closely related set of capabilities.

- **Key Properties of NgModule:**
  - **declarations:** Components, directives, and pipes that belong to this module.
  - **imports:** Other modules whose exported classes are needed by component templates declared in this module.
  - **exports:** The subset of declarations that should be visible and usable in the component templates of other modules.
  - **providers:** Services that the module contributes to the global collection of services.

**Example:**
```typescript
// app.module.ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent } from './app.component';

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

#### 3. **Components and Templates**
Components are the building blocks of an Angular application. Each component consists of:
- A **TypeScript class** that defines the behavior.
- An **HTML template** that defines the view.
- Optional **CSS styles** for the component.

**Example:**
```typescript
// example.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-example',
  template: `<p>{{ message }}</p>`,
  styles: [`p { color: blue; }`]
})
export class ExampleComponent {
  message = 'Hello, Angular!';
}
```

**Using the Component:**
```html
<!-- app.component.html -->
<app-example></app-example>
```

#### 4. **Directives**
Directives are used to manipulate the DOM. There are two main types of directives:
- **Structural Directives:** Change the DOM layout by adding or removing elements (e.g., `*ngIf`, `*ngFor`).
- **Attribute Directives:** Change the appearance or behavior of an element (e.g., `ngStyle`, `ngClass`).

**Example of Structural Directives:**
```html
<!-- app.component.html -->
<div *ngIf="isVisible">This is visible!</div>
<ul>
  <li *ngFor="let item of items">{{ item }}</li>
</ul>
```

**Example of Attribute Directives:**
```html
<!-- app.component.html -->
<div [ngStyle]="{'color': textColor}">This text is colored.</div>
<div [ngClass]="{'active': isActive}">This div has a class.</div>
```

**TypeScript Code:**
```typescript
// app.component.ts
export class AppComponent {
  isVisible = true;
  items = ['Item 1', 'Item 2', 'Item 3'];
  textColor = 'red';
  isActive = true;
}
```

### Notes on Data Binding in Angular

Data binding is a powerful feature in Angular that allows you to connect your application's data (in the component) with the UI (in the template). There are three main types of data binding in Angular:

1. **One-Way Data Binding**
2. **Two-Way Data Binding**
3. **Event Binding**

Let’s explore each of these with examples.

---

#### 1. **One-Way Data Binding**
One-way data binding is used to display data from the component in the template. There are two ways to achieve this:

- **Interpolation:** Embed expressions in double curly braces `{{ }}` to display data.
- **Property Binding:** Bind component properties to HTML element properties using square brackets `[]`.

**Example of Interpolation:**
```html
<!-- app.component.html -->
<p>Welcome, {{ username }}!</p>
<p>2 + 2 = {{ 2 + 2 }}</p>
```

**TypeScript Code:**
```typescript
// app.component.ts
export class AppComponent {
  username = 'JohnDoe';
}
```

**Example of Property Binding:**
```html
<!-- app.component.html -->
<img [src]="imageUrl" alt="Angular Logo">
<button [disabled]="isDisabled">Click Me</button>
```

**TypeScript Code:**
```typescript
// app.component.ts
export class AppComponent {
  imageUrl = 'https://angular.io/assets/images/logos/angular/angular.png';
  isDisabled = true;
}
```

---

#### 2. **Two-Way Data Binding**
Two-way data binding allows you to synchronize data between the component and the template. This is commonly used with form inputs. Angular provides the `[(ngModel)]` directive for two-way binding.

**Example:**
```html
<!-- app.component.html -->
<input [(ngModel)]="name" placeholder="Enter your name">
<p>Hello, {{ name }}!</p>
```

**TypeScript Code:**
```typescript
// app.component.ts
export class AppComponent {
  name = '';
}
```

**Note:** To use `[(ngModel)]`, you need to import the `FormsModule` in your `AppModule`.

```typescript
// app.module.ts
import { FormsModule } from '@angular/forms';

@NgModule({
  imports: [
    BrowserModule,
    FormsModule // Add FormsModule here
  ],
  declarations: [AppComponent],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

---

#### 3. **Event Binding**
Event binding allows you to capture user interactions (like clicks, keypresses, etc.) and execute methods in the component. Use the `(event)` syntax to bind to DOM events.

**Example:**
```html
<!-- app.component.html -->
<button (click)="onButtonClick()">Click Me</button>
<p>{{ message }}</p>
```

**TypeScript Code:**
```typescript
// app.component.ts
export class AppComponent {
  message = '';

  onButtonClick() {
    this.message = 'Button was clicked!';
  }
}
```

**Example with Event Object:**
You can also access the event object (e.g., `$event`) to get details about the event.

```html
<!-- app.component.html -->
<input (keyup)="onKeyUp($event)" placeholder="Type something">
<p>{{ inputValue }}</p>
```

**TypeScript Code:**
```typescript
// app.component.ts
export class AppComponent {
  inputValue = '';

  onKeyUp(event: KeyboardEvent) {
    this.inputValue = (event.target as HTMLInputElement).value;
  }
}
```

---

### Summary of Data Binding

| Type of Binding         | Syntax                  | Description                                                                 |
|-------------------------|-------------------------|-----------------------------------------------------------------------------|
| **One-Way (Interpolation)** | `{{ expression }}`      | Displays data from the component in the template.                           |
| **One-Way (Property Binding)** | `[property]="expression"` | Binds a component property to an HTML element property.                     |
| **Two-Way Binding**      | `[(ngModel)]="property"` | Synchronizes data between the component and the template (e.g., form input).|
| **Event Binding**        | `(event)="method()"`    | Executes a method in the component when a DOM event occurs.                 |

---

### Practical Example Combining All Bindings

```html
<!-- app.component.html -->
<h1>Data Binding Example</h1>

<!-- One-Way Binding (Interpolation) -->
<p>Welcome, {{ username }}!</p>

<!-- One-Way Binding (Property Binding) -->
<img [src]="imageUrl" alt="Angular Logo" [style.width.px]="imageWidth">

<!-- Two-Way Binding -->
<input [(ngModel)]="name" placeholder="Enter your name">
<p>Hello, {{ name }}!</p>

<!-- Event Binding -->
<button (click)="onButtonClick()">Click Me</button>
<p>{{ message }}</p>
```

```typescript
// app.component.ts
export class AppComponent {
  username = 'JohnDoe';
  imageUrl = 'https://angular.io/assets/images/logos/angular/angular.png';
  imageWidth = 100;
  name = '';
  message = '';

  onButtonClick() {
    this.message = 'Button was clicked!';
  }
}
```

---

### Notes on Dependency Injection (DI) and Services in Angular

Dependency Injection (DI) is a design pattern in which a class requests dependencies from external sources rather than creating them itself. Angular has a powerful DI system that makes it easy to inject services into components, directives, and other services.

Let’s break this topic into three parts:

1. **Services**
2. **Dependency Injection System**
3. **RxJS Integration**

---

#### 1. **Services**
Services are used to share reusable logic and state across components. They are typically used for:
- Fetching data from a server.
- Sharing data between components.
- Encapsulating business logic.

**Creating a Service:**
Use the Angular CLI to generate a service:
```bash
ng generate service data
```

This creates a service file (`data.service.ts`) and a test file.

**Example of a Service:**
```typescript
// data.service.ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root' // Service is provided at the root level
})
export class DataService {
  private data: string[] = ['Apple', 'Banana', 'Cherry'];

  getData(): string[] {
    return this.data;
  }

  addData(item: string): void {
    this.data.push(item);
  }
}
```

**Using the Service in a Component:**
```typescript
// app.component.ts
import { Component } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <ul>
      <li *ngFor="let item of items">{{ item }}</li>
    </ul>
    <input [(ngModel)]="newItem" placeholder="Add new item">
    <button (click)="addItem()">Add</button>
  `
})
export class AppComponent {
  items: string[];
  newItem = '';

  constructor(private dataService: DataService) {
    this.items = this.dataService.getData();
  }

  addItem(): void {
    this.dataService.addData(this.newItem);
    this.newItem = ''; // Clear the input
  }
}
```

---

#### 2. **Dependency Injection System**
Angular’s DI system is hierarchical, meaning it can provide services at different levels:
- **Root Level:** A single instance of the service is shared across the entire application.
- **Module Level:** A single instance of the service is shared across the module.
- **Component Level:** A new instance of the service is created for each component.

**Providing a Service at Different Levels:**

1. **Root Level (Default):**
   ```typescript
   @Injectable({
     providedIn: 'root'
   })
   export class DataService { }
   ```

2. **Module Level:**
   ```typescript
   // app.module.ts
   @NgModule({
     providers: [DataService] // Provide the service at the module level
   })
   export class AppModule { }
   ```

3. **Component Level:**
   ```typescript
   // app.component.ts
   @Component({
     selector: 'app-root',
     template: `...`,
     providers: [DataService] // Provide the service at the component level
   })
   export class AppComponent { }
   ```

**Example of Hierarchical Injector:**
If a service is provided at both the root level and the component level, the component will use its own instance of the service, while other components will use the root-level instance.

---

#### 3. **RxJS Integration**
RxJS (Reactive Extensions for JavaScript) is a library for reactive programming using Observables. Angular services often use RxJS to handle asynchronous operations like HTTP requests.

**Using RxJS in a Service:**
```typescript
// data.service.ts
import { Injectable } from '@angular/core';
import { Observable, of } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  private data: string[] = ['Apple', 'Banana', 'Cherry'];

  getData(): Observable<string[]> {
    return of(this.data); // Return an Observable
  }

  addData(item: string): void {
    this.data.push(item);
  }
}
```

**Using the Observable in a Component:**
```typescript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <ul>
      <li *ngFor="let item of items">{{ item }}</li>
    </ul>
  `
})
export class AppComponent implements OnInit {
  items: string[] = [];

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.dataService.getData().subscribe(data => {
      this.items = data;
    });
  }
}
```

**Example with HTTP Requests:**
```typescript
// data.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/posts';

  constructor(private http: HttpClient) {}

  getPosts(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl);
  }
}
```

**Component Using HTTP Service:**
```typescript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <ul>
      <li *ngFor="let post of posts">{{ post.title }}</li>
    </ul>
  `
})
export class AppComponent implements OnInit {
  posts: any[] = [];

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.dataService.getPosts().subscribe(posts => {
      this.posts = posts;
    });
  }
}
```

---

### Summary

| Concept                  | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| **Services**             | Reusable classes for sharing logic and state across components.             |
| **Dependency Injection** | Angular’s hierarchical injector provides services at root, module, or component level. |
| **RxJS Integration**     | Use Observables for reactive programming, especially in asynchronous tasks like HTTP requests. |

---

### Practical Example Combining DI and RxJS

```typescript
// data.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class DataService {
  private apiUrl = 'https://jsonplaceholder.typicode.com/posts';

  constructor(private http: HttpClient) {}

  getPosts(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl);
  }
}
```

```typescript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { DataService } from './data.service';

@Component({
  selector: 'app-root',
  template: `
    <h1>Posts</h1>
    <ul>
      <li *ngFor="let post of posts">{{ post.title }}</li>
    </ul>
  `
})
export class AppComponent implements OnInit {
  posts: any[] = [];

  constructor(private dataService: DataService) {}

  ngOnInit(): void {
    this.dataService.getPosts().subscribe(posts => {
      this.posts = posts;
    });
  }
}
```

### Notes on Routing and Navigation in Angular

Routing is a core feature in Angular that allows you to build Single Page Applications (SPAs) by enabling navigation between different views (components) without reloading the page. Let’s break this topic into three parts:

1. **Router Basics**
2. **Lazy Loading**
3. **Route Guards**

---

#### 1. **Router Basics**
The Angular Router enables navigation by interpreting a browser URL as an instruction to change the view.

**Configuring Routes:**
Routes are defined in the `AppRoutingModule` (or directly in `AppModule`).

**Example:**
```typescript
// app-routing.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';
import { UserDetailComponent } from './user-detail/user-detail.component';

const routes: Routes = [
  { path: '', component: HomeComponent }, // Default route
  { path: 'about', component: AboutComponent },
  { path: 'user/:id', component: UserDetailComponent }, // Route with parameter
  { path: '**', redirectTo: '' } // Wildcard route for 404
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

**Using Router Outlet:**
The `<router-outlet>` directive is used in the template to mark where the routed views should be displayed.

```html
<!-- app.component.html -->
<nav>
  <a routerLink="/">Home</a>
  <a routerLink="/about">About</a>
</nav>
<router-outlet></router-outlet>
```

**Passing Route Parameters:**
You can pass parameters in the URL and access them in the component using the `ActivatedRoute` service.

**Example:**
```typescript
// user-detail.component.ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-user-detail',
  template: `<p>User ID: {{ userId }}</p>`
})
export class UserDetailComponent implements OnInit {
  userId: string;

  constructor(private route: ActivatedRoute) {}

  ngOnInit(): void {
    this.userId = this.route.snapshot.paramMap.get('id')!;
  }
}
```

---

#### 2. **Lazy Loading**
Lazy loading is a technique that loads feature modules on demand, improving the performance of your application by reducing the initial bundle size.

**Configuring Lazy Loading:**
To lazy load a module, use the `loadChildren` property in the route configuration.

**Example:**
```typescript
// app-routing.module.ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { path: 'user/:id', component: UserDetailComponent },
  { 
    path: 'products', 
    loadChildren: () => import('./products/products.module').then(m => m.ProductsModule) 
  },
  { path: '**', redirectTo: '' }
];
```

**Products Module:**
```typescript
// products.module.ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { ProductListComponent } from './product-list/product-list.component';
import { ProductDetailComponent } from './product-detail/product-detail.component';

const routes: Routes = [
  { path: '', component: ProductListComponent },
  { path: ':id', component: ProductDetailComponent }
];

@NgModule({
  imports: [RouterModule.forChild(routes)],
  declarations: [ProductListComponent, ProductDetailComponent]
})
export class ProductsModule { }
```

---

#### 3. **Route Guards**
Route guards are used to control access to routes based on certain conditions. Common types of guards include:
- **CanActivate:** Controls whether a route can be activated.
- **CanDeactivate:** Controls whether a route can be deactivated.
- **Resolve:** Pre-fetches data before activating a route.

**Example of CanActivate Guard:**
```typescript
// auth.guard.ts
import { Injectable } from '@angular/core';
import { CanActivate, Router } from '@angular/router';
import { AuthService } from './auth.service';

@Injectable({
  providedIn: 'root'
})
export class AuthGuard implements CanActivate {
  constructor(private authService: AuthService, private router: Router) {}

  canActivate(): boolean {
    if (this.authService.isLoggedIn()) {
      return true;
    } else {
      this.router.navigate(['/login']);
      return false;
    }
  }
}
```

**Using the Guard in Routes:**
```typescript
// app-routing.module.ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { 
    path: 'profile', 
    component: ProfileComponent, 
    canActivate: [AuthGuard] // Protect this route
  },
  { path: '**', redirectTo: '' }
];
```

**Example of CanDeactivate Guard:**
```typescript
// unsaved-changes.guard.ts
import { Injectable } from '@angular/core';
import { CanDeactivate } from '@angular/router';
import { Observable } from 'rxjs';

export interface CanComponentDeactivate {
  canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean;
}

@Injectable({
  providedIn: 'root'
})
export class UnsavedChangesGuard implements CanDeactivate<CanComponentDeactivate> {
  canDeactivate(component: CanComponentDeactivate): boolean | Observable<boolean> | Promise<boolean> {
    return component.canDeactivate ? component.canDeactivate() : true;
  }
}
```

**Using the Guard in Routes:**
```typescript
// app-routing.module.ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  { 
    path: 'edit', 
    component: EditComponent, 
    canDeactivate: [UnsavedChangesGuard] // Prevent accidental navigation
  },
  { path: '**', redirectTo: '' }
];
```

**Example of Resolve Guard:**
```typescript
// user.resolver.ts
import { Injectable } from '@angular/core';
import { Resolve } from '@angular/router';
import { UserService } from './user.service';
import { Observable } from 'rxjs';

@Injectable({
  providedIn: 'root'
})
export class UserResolver implements Resolve<any> {
  constructor(private userService: UserService) {}

  resolve(): Observable<any> {
    return this.userService.getUser();
  }
}
```

**Using the Resolver in Routes:**
```typescript
// app-routing.module.ts
const routes: Routes = [
  { 
    path: 'profile', 
    component: ProfileComponent, 
    resolve: { user: UserResolver } // Pre-fetch user data
  },
  { path: '**', redirectTo: '' }
];
```

---

### Summary

| Concept                  | Description                                                                 |
|--------------------------|-----------------------------------------------------------------------------|
| **Router Basics**        | Configure routes, pass parameters, and handle navigation using `<router-outlet>`. |
| **Lazy Loading**         | Load feature modules on demand to optimize performance.                     |
| **Route Guards**         | Use `CanActivate`, `CanDeactivate`, and `Resolve` to secure routes and manage navigation logic. |

---

### Practical Example Combining Routing, Lazy Loading, and Guards

```typescript
// app-routing.module.ts
const routes: Routes = [
  { path: '', component: HomeComponent },
  { path: 'about', component: AboutComponent },
  { 
    path: 'profile', 
    component: ProfileComponent, 
    canActivate: [AuthGuard], // Protect this route
    resolve: { user: UserResolver } // Pre-fetch user data
  },
  { 
    path: 'products', 
    loadChildren: () => import('./products/products.module').then(m => m.ProductsModule) // Lazy loading
  },
  { path: '**', redirectTo: '' }
];
```
