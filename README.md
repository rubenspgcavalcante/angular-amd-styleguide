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
    * [One Artifact per File](#one-artifact-per-file)
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
* The file name must be followed by *dot* and the type of artifact declared in the file, excepting the module file;
* The module file must have the same name as the module definition, using the dash notation;
* A module contains directories to each type of artifact plus the templates and sub-modules.

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


Then, you'll have your require js main file, where you describe you dependencies and shim configurations:

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

### One Artifact per File
Never put more than one artifact per file. This make constructors more easily found.  
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

They always must have the artifact type of artifact in appended in the name like, for instance, *HomeController*, 
*LoginService* or *UserFactory*.

### Using the $inject property

The AngularJS comes with a dependence injector, to load all dependencies of the declared artifact.
You probably already have seen if you pass as arguments to the constructor, it will load up before
the provider factory create the instance of the artifact, like:

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
check if the given constructor have this property defined and do his work. So, your code will be like:

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
TODO

### Services and Factories
TODO

### Values and Constants
TODO