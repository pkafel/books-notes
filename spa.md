# SPA design and Architecture: Understanding single-page web application (by Emmit A. Scott Jr.)

## Benefits of SPAs over traditional web applications
* Renders like a desktop application, but runs in a browser
* Decoupled presentation layer
* Faster, lightweight transaction payloads
* Less user wait time
* Easier code maintenance

## Common MV* concepts 
* *Models* —Models represent the data of our application. They contain properties and possibly logic to access and manage the data, including validation. The model often contains business logic as well.
* *Views* —Views are what the user sees and interacts with and are where models are visually represented. In some MV* implementations, views may also contain presentation logic.
* *Templates* —Templates are the reusable building blocks for views when dynamic content is needed. They contain placeholders for data, as well as other instructions for how content in the template should be rendered. One or more templates will be used to create a view in an SPA.
* *Binding* —This term describes the process of associating the data from a model with an element in a template. Some MV* implementations also provide other types of binding, such as bindings for events and CSS styles.

## Models
* Implicit
* Explicit 

## Bindings
* Two-way binding
* One-way binding (mostly for read only scenarios)
* One-time binding

## Benefits of MV* frameworks
* Separation of concerns
* Routine tasks simplified 
* Productivity gains 
* Standardization 
* Scalability

## Modules 

### Benefits of modular programming
* Avoid name collisions
* Protecting code integrity
* Hiding complexity
* Reducing the impact of code changes
* Organizing your code

### Traditional module pattern

```js
var numberModule = (function() {
    var num1 = 2;
    
    function addNumbersInternally(num2) {
        return num1 + num2;
    }
    
    return {
        addTwoNumbers : function(num2) {
            alert(addNumbersInternally(num2));
        }
    }
})();

numberModule.addTwoNumbers(2);
```

### Revealing module pattern

```js
var numberModule = (function() {
    var num1 = 2;
    
    function addNumbersInternally(num2) {
        return num1 + num2;
    }
    
    return {
        addTwoNumbers : addNumbersInternally
    }
})();

numberModule.addTwoNumbers(2);
```

## Inter-module interactions
* Direct dependencies pros and cons
  * Pros
    * easier to debug
    * no intermediary objects are involved
  * Cons
    * coupling
    * in some cases you want to invoke some method on a lot of dependencies which will be daunting
* Publisher\\subscribers dependencies pros and cons
  * Pros
    * loose coupling
    * messages can be broadcasted to many subscribers
  * Cons
    * If not build-in your framework you need to add additional dependency
    * You need to rely on `String` and naming conventions
    * One direction communication
    * Harder to debug 

## Asynchronous Module Definition 

AMD script loaders handle the low-level, boilerplate code to manage the asynchronous download process and let you specify dependencies that must be present for a module to function.

Two base structures:
* `define` - lets you declare a module and its dependencies which will be resolved on library level

```js
define('someId',
    ['dependency1', 'dependency2'],
    function(dep1, dep2) {
        // more code
        return {
            // more code
        };
    }
);
```

* `require` - use to asynchronously load/fetch a particular module 

```js
require(
    ['module1', 'module2'],
    function(m1, m2) {
        // more code
    }
);
```

Example of AMD is RequireJS.

## Routing

* Fragment identifier method - by using `#` browser is not reloading however it is making entry in browser history which lets user use back and forth button
* HTML 5 history API - not supported on older browser

Example of routing (in Angular.js):

```js
$routerProvider.when("/routes/faculty", {
    templateUrl: "App/components/factList.html",
    controller: "facultyController"
}).otherwise({
    redirectTo: "/routes/faculty"
});
```
