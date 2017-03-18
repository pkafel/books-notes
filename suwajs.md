# Shaping up with Angular.js (by Code School)

## Directives

Marker on a HTML or new HTML tag that tells Angular to run or reference some Javascript code.

```html
<body ng-controller="StoreController"></body>
```

Example of directives:
* `ng-app` - attach application module to the page
* `ng-controller` - attach controller to HTML element
* `ng-show` - show HTML element only when evaluate to `true`
* `ng-hide` - negation of `ng-show`
* `ng-repeat` - loop
* `ng-click` - on click
* `ng-init` - evaluate expression in current scope during initialization
* `ng-class` - set html element's class if expression after colon evaluates to `true` 
* `ng-model` - bind the form element value to the property
* `ng-submit` - bind controller method call to form so it will be called on submitting form
* `ng-include` - let you inject snippets of html code (worth noting is that `ng-include` expects variable so if you want to pass directly name of the file you need to wrap it with `'`)

### Writing custom directives

```js
app.directive("DirectiveName", function() {
    return {
        restrict: 'E', // E for element and A for attribute, required
        templateUrl: 'my-file.html', // required
        controller: function() { // optional
        },
        controllerAs: 'alias' // optional
    };
});
```

## Expressions

Allow you to insert dynamic values into HTML.

```html
<p>
    Im {{4 + 6}}
</p>
```

Expressions define 2-way binding which means that expressions are re-evaluated when a property changes.

## Filters

Provides possibility to transform data inside expression

```html
<p>
    Im {{4 + 6 | currency}}
</p>
```

Example of filters:
* `currency` - format a number to a currency format
* `date` - format a date to a specified format
* `filter` - select a subset of items from an array
* `json` - format an object to a JSON string
* `limitTo` - limits an array/string, into a specified number of elements/characters
* `lowercase` - format a string to lower case
* `number` - format a number to a string
* `orderBy` - orders an array by an expression
* `uppercase` - format a string to upper case

## Controllers

Logic of your application

```js
app.controller("ExampleController", function() {
    // more code goes here
});
```

Injecting dependencies into controller:

```js
app.controller('StoreController', ['$http', function($http){
    var store = this;
    store.products = [];
    $http.get('/store-products.json').success(function(data) {
      store.products = data;
    });
  }]);
```

## Modules

Set of controllers, directives etc. enclosed in one namespace;

```js
angular.module('NameOfTheModule', []); // second parameter is a list of dependencies
```

## Services

Give your controllers additional functionality.

Example:
* `$http` - fetching data from a server. Example usage:
  * `$http.get('/product.json', {apiKey: 'myApiKey'})`
  * `$http({method: 'GET', url: '/product.json'})`
* `$log` - console logging
* `$filter` - filtering arrays
