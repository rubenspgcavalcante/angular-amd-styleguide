# Angular AMD Style Guide
A style guide for AngularJS using asynchronous module definition *(using RequireJS)*, based on style
guides like the [John Papa Angular Style Guide](https://github.com/johnpapa/angular-styleguide),
[Google Style Guide](http://google.github.io/styleguide/angularjs-google-style.html) and the architecture
of [Django apps](https://docs.djangoproject.com/en/1.8/intro/reusable-apps/).  
  
*By [Rubens Pinheiro](https://github.com/rubenspgcavalcante)*


## Table of Contents
1. [Architecture and File Structure](#architecture-and-file-structure)
    * [File Conventions](#file-conventions)
    * [Module Conventions](#module-conventions)
    * [The Root Module and the Starting Structure](#the-root-module-and-the-starting-structure)
2. [Code Conventions](#code-conventions)
    * [One Element per File](#one-element-per-file)
    * [Controllers and Partials](#controllers-and-partials)
    * [Directives and Templates](#directives-and-templates)
    * [Services and Factories](#services-and-factories)
    * [Values and Constants](#values-and-constants)
    
## Architecture and File Structure
The idea is to turn your angular application into small apps, making it more independent,
so you can reuse this small app in other projects too. The app in the style guide are the **modules**.
They're not only Angular modules, but also AMD modules too.

### File Conventions
* The files must be named into the dashing notation;
* The file name must be followed by *dot* and the type of element declared in the file, excepting the module file;
* The module file must have the same name as the module definition, using the dash notation;
* A module contains directories to each type of element plus the templates and sub-modules.

e.g.: ( **+** Directory, **-** File)

    + user-login
        - user-login.js
        - index.js
      
      + controllers
            - user.controller.js
            - user-login.controller.js
      
      + partials
            - user.partial.html
            - user-login.partial.html
            
      + directives
            - login-form.directive.js
            + templates
                - login-form.template.html
        
      + services
            - login.service.js
        
      + factories
            - user.factory.js
        
      + resources
            - user-resource.factory.js
        
      + constants
            - user-type.constant.js
        
      + values
            - user-backgroud.value.js
        
      + modules  
        + my-sub-module
            ...
        + my-another-sub-module
            ...

### Module Conventions
A module basically is consisted of:

* Controllers
* Partials
* Directives and Templates
* Services
* Factories
* Resources
* Values
* Constants
* The Module Definition
* The Module Index

The **module definition** is the file where the Angular module is declared and assume the format:  

* *my-module.js*

```javascript
define([
    'angular'
], function (angular) {
    'use strict';
    
    var userLogin = angular.module('app.userLogin', [
        'ui.router',
        'app.userLogin.mySubModule'
        'app.userLogin.mySubAnotherModule'
        'app.siblingModule'
        //...
    ]);
    
    userLogin.config = function(){
        //...
    };
});
```


If this module is children of another module or sub-module, use the dot notation into de module declaration:  
E.g.: *(the module home is parent of module signIn which are parent of module userLogin)*

```javascript
...
var userLogin = angular.module('home.signIn.userLogin', [ ... ]);
...
```

The **module index** works like a "manifest" of what is the content of the module, which are the sub-modules
and also which outer modules are dependencies. All modules must have a index file, always with the same
name, **index.js**:

* *index.js*

```javascript
define([
    'user-login', 
    'controllers/user.controller',
    'directives/login-form.directive'
    'services/login.service',
    //...
    'modules/my-submodule/index',
    'modules/my-another-submodule/index'
    '../sibling-module/index'
]);
```

*To add other modules as dependency, just include they indexes files* 


### The Root Module and the Starting Structure

In your project resources directory there'll be a lot of configuration files, like npm, bower
gulp, etc. So to don't mix this files with your application source. Create a module that acts
like the root of the module dependencies tree. This is the **App** module which loads all your main application 
sub-modules. The global configurations of you application is also made here.

* *app.js*

```javascript
define([
    "angular"
], function (angular) {
    'use strict';

    // Loading main submodules
    var app = angular.module("app", [
        'app.ui',
        'app.commons',
        'app.system',
        'app.user',
        //...
    ]);
});
```

And the index file from App module:

* *index.js*

```javascript
define([
    './modules/ui/index',
    './modules/commons/index',
    './modules/system/index',
    './modules/user/index',
    //...
]);
```


Then, you'll have your requireJS main file, where you describe you dependencies and shim configurations:

```javascript
require.config({
    baseUrl: './',
    config: {
        angular: {
            noGlobal: true
        },
        //...
    },
    paths: {
        angular: 'vendor/angular/angular',
        //...    
    },
    shim: {
        //...    
    },
    deps: ['ng-bootstrap'],
    //...
    }
```
        
And you can use a lib to bootstrap your angular application. This case is using the ng-bootstrap
with the domReady RequireJS plugin.  
Ex.:

```javascript
define('ng-bootstrap', [
    'angular',
    'uiRouter',
    'domReady',
    'app/app'
], function (angular) {
    'use strict';
    
    require(['domReady!'], function (document) {
        angular.bootstrap(document, ['app']);
    });
});
```
        
Now, you can already run you application using AngularJS and RequireJS in the proposed architeture.

# Code Conventions

### One Element per File
Never put more than one element per file. This make constructors more easily found.  
**See also [File Conventions](#file-conventions)

### Postfix your Constructors Name
Using postfixes based on type of constructors avoid name collisions.  
e.g.: a service and a controller for **login**:

```javascript
function LoginController ( ... ) { ... }
```
and
```javascript
function LoginService ( ... ) { ... }
```

**except directives** *(see bellow)*

### Namespace all application's directives
Always namespace your applications directives to avoid name collisions. For example, you create a datepicker directive,
but you need one more complex, so you install a angular plugins which contains a directive with the same name, resulting
in a name collision. To avoid this, prefix with your application acronyms. If your application have the name **My Forecast**,
create the directive like **mfDatepicker**.
<sup> Note that the angular built-in directives always comes with the *ng* prefix, e.g.: <... ng-controller='...'>, <... ng-click='...'> </sup>


### Using vm (View Model) reference
Following the [John Papa](https://github.com/johnpapa/angular-styleguide#style-y032) recommendation, always use the
view-model reference in controller:

```javascript
function UserController() {
    var vm = this;
    vm.userName = "Foo Bar";
    vm.sendMail = function() { ... };
}
```
... and the **controller as** ngController notation in partials

```html
<div ng-controller="CustomerController as vm">
    <button ng-click="vm.sendMail">Send to {{vm.userName}}</button>
</div>
``` 

### Constructor and instance names

Constructors always have the **upper camel case** notation:

```javascript
function MyController ( ... ) { ... }

function MyService ( ... ) { ... }
```

The instances always have the **lower camel case** notation:

```javascript
function MyAnotherController ( myService, anotherService) { ... }
```

They always must have the element type of element in appended in the name like, for instance, *HomeController*, 
*LoginService* or *UserFactory*.

### Using the $inject property

The AngularJS comes with a dependence injector, to load all dependencies of the declared element.
You probably already have seen if you pass as arguments to the constructor, it will load up before
the provider factory create the instance of the element, like:

```javascript
//...
function HomeController ($scope, $filter, userService) {
    //All the dependecies are already injected!
    //...
}
//...
```

**But there's a problem**, when you minify your code, all the arguments names will be changed, and
the Angular injector doesn't knows which are the providers. So, to prevent this, you will probably inject using
this format:

**Avoid**

```javascript
...
['$scope', '$filter', 'UserService', function ($scope, $filter, userService) {
    //This is really uggly, and keeps the code very dirty :(
    ...
}]
...
```

So the cleaner way to do this, is just use the *$inject* property. The injector will always first
check if the given constructor have this metadata property defined and do his work. So, your code will be like:

**Recommended**
```javascript
myModule.controller('UserController', UserController);
    
UserController.$inject = ['$scope', '$filter', 'UserService']; //Done!
function UserController ($scope, $filter, userService) {
    //All the dependecies are already injected!
    //...
}
```

### Controllers and Partials

#### Controllers responsibility and fragmented partials
Controllers are responsible by manage event and data flux between the components. **Always** bind one controller to one
partial *(if you want to reuse a logic to more than one view, use directives)*. In a SPA, one controller corresponds to
one action/route of your application, loading its partials to a "view container" inner your app template.
You can break your partials in other partials so:

**Avoid**
```xhtml
<div ng-controller="MyController as mv" ng-init="mv.init()">
    <!-- MONOLITIC XHTML! (hundreds of lines...) -->
</div>    
```

**Recommended**
```xhtml
<div ng-controller="MyController as mv" ng-init="mv.init()">
    <div ng-include=" '../partials/my-menu.partial.html' "></div>
    <div ng-include=" '../partials/my-content.partial.html' "></div>
    <div ng-include=" '../partials/my-footer.partial.html' "></div>
</div>  
```
------

#### One action/route -> One controller
Prefers to use **one** controller per action, because controllers are used to manage top level data and comunication
between components (directives). Other reason is because controller share they scope to inner controllers via prototype, 
causing *a lot* of problems *(just google it and see it!)*. So, use one top controller and in it directives instead of
other controllers.

**Avoid**
```xhtml
<div ng-controller="MyController as mv" ng-init="mv.init()">
    <!-- some xhtml... -->
    
    <div ng-controller="UserTableListController">
        <!-- more xhtml... -->
    </div>
    
</div>    
```

**Recommended**
```xhtml
<div ng-controller="MyController as mv" ng-init="mv.init()">
    <!-- some xhtml... -->
    
    <user-table-list ng-model='mv.users'></user-table-list>
</div>  
```
------
#### Controller initializer
Always use the controller "initializer", to load data and set the default initial controller properties. Like almost
everything in Angular are [*singletons*](https://en.wikipedia.org/wiki/Singleton_pattern), they execute the constructor
one time (more precisely in the provider, which have a behavior of a factory), for dependency injection. So, if you enters
the action which invokes a previous instantiated controller, he will maintain his anterior state:

**Avoid**
```xhtml
<div ng-controller="MyController as mv">
    <!-- content -->
</div>
```

```javascript
//...
function MyController ($scope, $filter, User) {
    var mv = this;
    mv.user = User.get({id: 0});
    //...
}
//...

```

**Recommended**
```xhtml
<div ng-controller="MyController as mv" ng-init="mv.init()">
    <!-- content -->
</div>
```
*Always creates a init method on the top level of your controller constructor body.*
```javascript
//...
function MyController ($scope, $filter, User) {
    var mv = this;
    
    mv.init = function(){
        mv.user = User.get({id: 0});
    }
    //...
}
//...

```

### Directives and Templates

Directive acts like a **web components** *(without the encapsulated CSS, the shadow DOM, etc)*, and angular simulates this behaviour very well. 
Another useful thing is that directives have a **isolated scope**, so you don't need to worry about sharing data into
a non declarative way via scope references, like controllers do.

----
#### Naming directives and prefixing the name
Directives *don't need* postfix name declaration. As exmplained before, to avoid naming collisions with third party directives, you should
set your application prefix before the directive name. Use shortened prefixes, e.g.: Your application name is *Hello World*, 
so your prefix will be *hl*. <sup>(the application acronym) </sup>

**Avoid**
```javascript
//...

myModule.directive('Calendar', Calendar);

Calendar.$inject = ['SomeService'];
function Calendar (someService) {
    
    return {
        restrict: 'E',
        // ...
    };
}
//...

```

```xhtml
<!-- Its easy to collide with some plugin's directive/web-component name -->
<calendar></calendar>

```

**Recommended**
```javascript
//...

myModule.directive('ThCalendar', thCalendar);

thCalendar.$inject = ['SomeService'];
function thCalendar (someService) {
    
    return {
        restrict: 'E',
        // ...
    };
}
//...

```

```xhtml
<!-- Problem solved! -->
<th-calendar></th-calendar>

```

----
#### Templates
Templates represents a view, bound to a component, and must be portable for any part of your application. So, beware
with local CSS rules to your templates. If you put your directive in another part of your system, it must be represented
in the same way (unless your component has multiple templates/views depending on **his** state).

### Services and Factories

####Some of author's personal thoughts
There's always a **lot** of confusion about the service and the factory in angular 1.x applications. But let's keep it simple.  
A **service** basically is a entity of you application which encapsulates some business rules and logic, and externally provides
this for the application via a API.  
A **factory** in the other hand, is a entity responsible for instantiate and the creation of
specific objects, in application's run time. So, if you need to build one or more complex objects, with some specific configurations and
with a similar behaviour, do this inside a factory!  
**Simple right?** But a lot of angular application simply mix this two concepts and do *everything* with "services" using the factory provider! (???)  
Just like this:  

**Avoid**
```javascript
//...

MyService.factory('MyService', MyService);

MyService.$inject = ['OtherService'];
function MyService (someService) {
    
    var _somePrivateMethod = function() {
        //...
    }
    
    return {
        someMethod: function() {
            //...
        }
    };
}
//...

```

**Recommended**
```javascript
//...

MyService.service('MyService', MyService);

MyService.$inject = ['OtherService'];
function MyService (someService) {
    var _somePrivateMethod = function() {
        //...
    }
    
    this.someMethod = function() {
        //...
    }
}
//...

```

Some developers argues, all in all the **service and the factory** will be created, in background, in a specific type of *factory* of the angular, the **provider**.
But in response, I always explains in the background, the language uses the **JUMP** in the assembly code to do the flux control instructions, and in your code you can simply do
using the **GOTO** statement. And after that I question: "Do you use the GOTO instead the 'if ... else', 'while' and 'for' in you application?".  
So, if you building a really large application, instead overload the *factory provider* to act in multiple contexts, simply use the service provider to declare services and the 
factory provider to declare factories. This way, you have benefits! e.g.: you can *decorate* your service provider and intercept the services creation, to log or do other stuff, without
intercept wrongly some factory creations.

#### The Factory
 Always externalise the factory method. Commonly called as **build**, where you can pass some custom options to be joined with your default configuration
 to build the instances.
 
 **Example:**
 
 ```javascript
 //...
 
 VehicleFactory.factory('VehicleFactory', VehicleFactory);
 
 VehicleFactory.$inject = ['OtherService'];
 function VehicleFactory (someService) {
     
     var _buildCar = function() {
         //...
     }
     
     var _buildTruck = function() {
        //...
     }
     
     return {
         build: function(type) {
             //...
         }
     };
 }
 //...
```
 
Factory generally are much simple. They centralize a lot of configuration, and encapsulates the **new** keyword there, letting your controllers and directives to be more clean.
This is important, because a lot of **plugins** require configurations and explicit call they constructor. So, if you're porting a plugin to act like a component, using directives,
the better way to call and instantiate the objects is in a factory, and not in the directive's constructor.

### Values and Constants

#### Constants
Lot of configuration is also static so, basically you can define it using a constant. The advantage, is because the constants are available in *config* time. The constant notation is simple:
 ```javascript
 //...
 
var ConfigExample = {
    REQUEST_TIMEOUT: 60,
    XHR: true,
    DEFAULT_APPLICATION: 'application/json'
};

myModule.constant('ConfigExample', ConfigExample);
 //...
```

#### Values
When you need some processed values, the better way is to declare as a value. Differently the constant, values are not available in config, but in run time. The notation changes a little:
 ```javascript
 //...
 
var LayoutDefinition = {
    choosenTheme: localStorage.getItem("theme"),
    //...
};

myModule.value('LayoutDefinition', LayoutDefinition);
 //...
```