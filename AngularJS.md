# AngularJS

* `ng-app` defines and links an Angular app to an HTML element
* `ng-model` binds values of Angular application data to HTML input controls
* `ng-bind` binds Angular application data to HTML tags

## MVC

* Model View Controller pattern
  * Model - responsible for **maintaining data** - responds to request from view and to instructions from controller to **update itself**
  * View - responsible for **displaying data** - triggered by controller decision to **present data**
  * Controller - controls interactions between Model and View - responds to **user input** and performs **interactions on model**

* **isolates logic from interface layer**

*MVC is popular because it isolates the application logic from the user interface layer and supports separation of concerns. The controller receives all requests for the application and then works with the model to prepare any data needed by the view. The view then uses the data prepared by the controller to generate a final presentable response.*

## best practices

* only directives should access DOM
* 

## Data binding

* Two way data binding
* Template compiled in browser --> produces live view 
* **changes to view are immediately reflected in model and any changes in the model are propagated to the view immediately**
* view just projection of model
  * controller is seperated, it only controlls model

## controllers

* used to augment the **scope**
* attachted to DOM via `ng-controller` directive
  * new child scope is created as new **injectable** parameter to controller's constructor function as `$scope`

* **Do use controllers for** - setting up initial state and adding behaviour to the `$scope` object
* **a countroller shouldn't do too much**
* **Do not use controllers to** :
  * Manipulate DOM - controllers should only contain buisness logic 
  * format input - use **angular form controls**
  * filter input - use **angular filters**
  * share code across instances - use **angular services**
  * manage life cycle of other components - for example to create service instances

### Setting up initial state of scope

* set up state by attaching properties to `$scope` object. Properties contains view model 
* all `$scope` properties will be available to template at the point where **controller is registered in DOM**
```javascript

var myApp = angular.module('myApp',[]);

myApp.controller('GreetingController', ['$scope', function($scope) {
  $scope.greeting = 'Hola!'; // Add greeting property to scope
}]);

//A corresponding HTML div would be:

<div ng-controller="GreetingController">
  {{ greeting }}
</div>

```


### Adding behaviour to a scope object

* in order to react to events or do computation, we must provide **behaviour** to the scope 
* **attach methods to `$scope` object**
* these methods can then be **called from the view** via angular **expressions** and ng event handler directives (e.g. `ngClick`)

```javascript

var myApp = angular.module('myApp',[]);

//add double method to scope which doubles the input number.

myApp.controller('DoubleController', ['$scope', function($scope) {
  $scope.double = function(value) { return value * 2; }; // attach behaviour to scope

  //methods can also have arguments
  $scope.multiply = function(multiplier) {
        $scope.number = number*multiplier;
    };
}]);

//A corresponding HTML div would be:
<div ng-controller="DoubleController">
  <button ng-click="multiply(multiplier)">Multiply</button>
  <p>Your number is {{number}}</p>

  Two times <input ng-model="num"> equals {{ double(num) }}
</div>
```

### Scope Inheritance

* always existent scope = **root scope**
* it is common to attach controllers at **different levels of the DOM** 
* every controller creates it's own scope --> **result: bunch of inherited scopes (hierarchy)**  
* children have access to scope of scopes, which are higher in hierarchy

```javascript

var myApp = angular.module('scopeInheritance', []);
myApp.controller('MainController', ['$scope', function($scope) {
  $scope.timeOfDay = 'morning';
  $scope.name = 'Nikki';
}]);
myApp.controller('ChildController', ['$scope', function($scope) {
  // timeOfDay is inherited from MainController
  $scope.name = 'Mattie';
}]);
myApp.controller('GrandChildController', ['$scope', function($scope) {
  $scope.timeOfDay = 'evening'; // inherited property can be redefined by children
  $scope.name = 'Gingerbread Baby';
}]);
```

### Testing controllers

* one of best conventions = **inject root scope** (`$rootScope`) and `$controller` 
* Jasmine example:

```javascript

describe('myController function', function() {

  describe('myController', function() {
    var $scope;

    beforeEach(module('myApp'));

    beforeEach(inject(function($rootScope, $controller) {
      $scope = $rootScope.$new();
      $controller('MyController', {$scope: $scope});
    }));

    it('should create "spices" model with 3 spices', function() {
      expect($scope.spices.length).toBe(3);
    });

    it('should set the default value of spice', function() {
      expect($scope.spice).toBe('habanero');
    });
  });
});
```
* Example for nested controllers - **recreate hierarchy like in DOM**

```javascript
describe('state', function() {
    var mainScope, childScope, grandChildScope;

    beforeEach(module('myApp'));

    beforeEach(inject(function($rootScope, $controller) {
        mainScope = $rootScope.$new();
        $controller('MainController', {$scope: mainScope});
        childScope = mainScope.$new();
        $controller('ChildController', {$scope: childScope});
        grandChildScope = childScope.$new();
        $controller('GrandChildController', {$scope: grandChildScope});
    }));

    it('should have over and selected', function() {
        expect(mainScope.timeOfDay).toBe('morning');
        expect(mainScope.name).toBe('Nikki');
        expect(childScope.timeOfDay).toBe('morning');
        expect(childScope.name).toBe('Mattie');
        expect(grandChildScope.timeOfDay).toBe('evening');
        expect(grandChildScope.name).toBe('Gingerbread Baby');
    });
});
```

## Services

* substitutable objects wired together using **dependency injection**
* **use services to organize and share code across app**
* **Lazily instantiated** - only when app component depends on it
* **Singletons** - Each component dependent on a service gets a reference to the single instance generated by the service factory

## Using a service

* **add as dependency for component** (controller, directive, service or filter) that depends on this service
* example:

```javascript
angular.
module('myServiceModule', [])
  // notify is added as dependency in the controller constructor
.controller('MyController', ['$scope','notify', function ($scope, notify) {
   $scope.callNotify = function(msg) {
     notify(msg);
   };
 }])
 // service gets initialized with factory keyword, it depends on the builtin service $window 
.factory('notify', ['$window', function(win) {
   var msgs = [];
   return function(msg) {
     msgs.push(msg);
     if (msgs.length == 3) {
       win.alert(msgs.join("\n"));
       msgs = [];
     }
   };
 }]);
```

### Creating services

* create own services by registering **name and factory function** inside angular module 
* **factory function** creates single object that **represents service** to the rest of the application
* the returned object or function is **dependency injected** into component when it (component) specifies a dependency on the service

### Registering services

* registered via **module API**
* use **module factory API** to register service

```javascript
var myModule = angular.module('myModule', []);
myModule.factory('serviceId', function() {
  var shinyNewServiceInstance;
  // factory function body that constructs shinyNewServiceInstance
  return shinyNewServiceInstance;
});
```
* Note: you create a **factory function, not a service instance.** (like a constructor, that only creates instance when called)

### Dependencies

* services can have own dependencies (like seen in above example)
* declared in factories **function signature**
* also possible to register service with `$provide`

```javascript
angular.module('myModule', []).config(['$provide', function($provide) {
  $provide.factory('serviceId', function() {
    var shinyNewServiceInstance;
    // factory function body that constructs shinyNewServiceInstance
    return shinyNewServiceInstance;
  });
}]);
```

### Unit Testing 
* now it gets crazy

```javascript

var mock, notify; //declare mock and notify
beforeEach(module('myServiceModule'));
beforeEach(function() {
  // file variable mock and add a jasmine spy for the property 'alert' which will later be called by notify
  mock = {alert: jasmine.createSpy()};

  // mock imitates the window service this time 
  module(function($provide) {
    $provide.value('$window', mock);
  });
  // fill notify with notify-service
  inject(function($injector) {
    notify = $injector.get('notify');
  });
});

it('should not alert first two notifications', function() {
  notify('one');
  notify('two');
  // call to mock only on 3rd time
  expect(mock.alert).not.toHaveBeenCalled();
});

it('should alert all after third notification', function() {
  notify('one');
  notify('two');
  notify('three');
  // call happened
  expect(mock.alert).toHaveBeenCalledWith("one\ntwo\nthree");
});

it('should clear messages after alert', function() {
  notify('one');
  notify('two');
  notify('third');
  notify('more');
  notify('two');
  notify('third');
  // 6 times notify = 2 times called and most recent call was with 'more two third' 
  expect(mock.alert.callCount).toEqual(2);
  expect(mock.alert.mostRecentCall.args).toEqual(["more\ntwo\nthird"]);
});
```

## Scopes

* scope is an **object** that refers to **application model**
* execution context for **expressions**
* arranged **hierarchical**, which mimic DOM structure
* scopes can **watch expressions** and **propagate events**


### scope characteristics

* scopes provide APIs to observe **model mutations** (`$watch`)
* scopes provide APIs to **propagate model changes** through system into view from outside the angular realm (controllers, services, event handlers) - (`$apply`)
* scopes can be nested
  * nested scopes are either **child scopes** or **isolate scopes**
  * child scope **inherits** properties from parent scope
  * isolate scope does **not**
* scopes provide context against which **expressions are evaluated**
* e.g. `{{username}}` is meaningless unless evaluated against specific scope which defines `ùsername` property

### Scope as data model

* scope is **glue between controller and view** 
* **You can think of the scope and its properties as the data which is used to render the view.** 
* testing excerpt which uses example from <a href="https://docs.angularjs.org/guide/scope">Angular scope guide</a>

```javascript

it('should say hello', function() {
  var scopeMock = {};
  var cntl = new MyController(scopeMock);

  // Assert that username is pre-filled
  expect(scopeMock.username).toEqual('World');

  // Assert that we read new username and greet
  scopeMock.username = 'angular';
  scopeMock.sayHello();
  expect(scopeMock.greeting).toEqual('Hello angular!');
});
```

### Scope hierarchies

* each angular application has exactly one **rootScope**
* Angular searches expressions first into local scope and then works its way up the hierarchy (e.g. childScope -> ParentScope -> rootScope)

### scope event propagation

* event can be **broadcasted to scope children** or **emitted to scope parents**
* both actions also propagate the **callers scope**

### scope life cycle

* to properly process model modification the execution has to enter Angular execution context using the `$apply` method.
* after evaluating the expression, `$apply` performs a `$digest` 
* in digest phase, the scope examines all `$watch` expressions and compares them with previous values

1. creation
  * root scope is created during application bootstrap by `$injector` 
2. watcher registration
  * directives register watcher on scopes 
3. model mutation
  * For mutations to be properly observed, you should make them only within the `scope.$apply()`.
4. ... nachlesen in URL oben ... viel stuff

### Scopes and directives


* *Observing directives, such as double-curly expressions {{expression}}, register listeners using the $watch() method. This type of directive needs to be notified whenever the expression changes so that it can update the view.*

* *Listener directives, such as ng-click, register a listener with the DOM. When the DOM listener fires, the directive executes the associated expression and updates the view using the $apply() method.*

### controllers and scopes

* Scopes and controllers interact with each other in the following situations:

* Controllers use scopes to expose controller methods to templates (see ng-controller).

* Controllers define methods (behavior) that can mutate the model (properties on the scope).

* Controllers may register watches on the model. These watches execute immediately after the controller behavior executes.

## Dependency injection

* can be used when defining components or when providing `run` or `config` blocks for a module

* *Components such as **services, directives, filters, and animations** are defined by an **injectable factory method or constructor function**. These components can be injected with "service" and "value" components as dependencies.*

* *Controllers are defined by a **constructor function, which can be injected with any of the "service" and "value" components as dependencies**, but they can also be provided with special dependencies. See Controllers below for a list of these special dependencies.*

* *The run method **accepts a function, which can be injected with "service", "value" and "constant" components as dependencies**. Note that you cannot inject "providers" into run blocks.*

* *The config method **accepts a function, which can be injected with "provider" and "constant" components as dependencies**. Note that you cannot inject "service" or "value" components into configuration.*

### Factory methods

* **define directives, services or filters with factory function**
* recommended way of declaring factories

```javascript

angular.module('myModule', [])
.factory('serviceId', ['depService', function(depService) {
  // ...
}])
.directive('directiveName', ['depService', function(depService) {
  // ...
}])
.filter('filterName', ['depService', function(depService) {
  // ...
}]);
```

### Module methods

* we can specify functions to run at **config or run time** for a module by calling `config` or `run`. They are dependency injectable too.

```javascript

angular.module('myModule', [])
.config(['depProvider', function(depProvider) {
  // ...
}])
.run(['depService', function(depService) {
  // ...
}]);
```

### Controllers

* Unlike services, there can be **many instances of the same type of controller** in an application.
* additional dependencies for controllers: `$scope`
  * other components (like services) only have access to `$rootScope`
  * *resolves: If a controller is instantiated as part of a route, then any values that are resolved as part of the route are made available for injection into the controller.*

* recommended way of declaring Controllers is using the array notation:

```javascript
someModule.controller('MyController', ['$scope', 'dep1', 'dep2', function($scope, dep1, dep2) {
  ...
  $scope.aMethod = function() {
    ...
  }
  ...
}]);
```

### Dependency annotation

*  Using the **inline array annotation** (preferred) or Using the `$inject` **property annotation**

* `$inject` property annotation

```javascript

var MyController = function($scope, greeter) {
  // ...
}
MyController.$inject = ['$scope', 'greeter'];
someModule.controller('MyController', MyController);
```

### Using strict dependency injection

* use `ng-strict-di` directive 
* use tool **ng-annotate**

### Why dependency injection

* Because it's cool 


## Templates

* written in HTML and contains **angular-specific elements and attributes**
* angular combines template with infos from **controller and model** to render what the user sees
* usable elements and attributes inside templates:
  * **directives** - augments an existing DOM element
  * **(angular) markup** - `{{}}` - binds expressions to elements
  * **filters** - formats data for display
  * **form controls** - validates user input
* *you can display **multiple views within one main page** using **partials** – segments of template located in **separate HTML files**. You can use the **ngView directive** to load partials based on configuration passed to the `$route` service.*
* see Angular tutorial step 7 and 8

## Angular Expressions

*Angular expressions are JavaScript-like code snippets that are usually placed in bindings such as `{{ expression }}`*

### Angular expressions vs JS expressions


* **Context**: JavaScript expressions are evaluated against the global `window`. In Angular, expressions are evaluated against a `scope` object.

* **Forgiving**: In JavaScript, trying to evaluate undefined properties generates ReferenceError or TypeError. In Angular, expression evaluation is forgiving to `undefined` and `null`.

* **No Control Flow Statements**: You cannot use the following in an Angular expression: *conditionals, loops, or exceptions*.

* **No Function Declarations:** You cannot declare functions in an Angular expression, even inside ng-init directive.

* **No RegExp Creation With Literal Notation:** You cannot create regular expressions in an Angular expression.

* **No Comma And Void Operators:** You cannot use , or void in an Angular expression.

* **Filters**: You can use filters within expressions to format data before displaying it.

### Context

* angular expressions don't have access to the global state like `window` or `document` - **use services like `$windows` in functions called from expressions, which provide access to globals**  

### $event

* Directives like `ngClick` and `ngFocus` expose a `$event` object within the scope of that expression.

### One time binding

* an expression starting with `::` will **stop recalculating** once they are stable
* example : `<p id="one-time-binding-example">One time binding: {{::name}}</p>`

<img src="http://www11.pic-upload.de/03.05.15/l6n34jfslv2.png">


