# Angular AMD Style Guide
A style guide for AngularJS using asynchronous module definition *(using RequireJS)*, based on style
guides like the [John Papa Angular Style Guide] (https://github.com/johnpapa/angular-styleguide),
[Google Style Guide](http://google.github.io/styleguide/angularjs-google-style.html) and the architecture
of [Django apps](https://docs.djangoproject.com/en/1.8/intro/reusable-apps/).  
  
*By [Rubens Pinheiro](https://github.com/rubenspgcavalcante)*


## Table of Contents
1. [Architecture and File Structure](#architecture-and-file-structure)
    * [File Conventions](#file-conventions)
    * [Module Conventions](#module-conventions)
    * [The Root Module and the Starting Structure](#the-root-module-and-the-starting-structure)
2. [Code Conventions](#code-conventions)
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
* The file name must be followed by the type of artifact of the file itself, excepting the module file;
* The module file must have the same name of the module definition, using the dash notation;
* A module contains directories to each artifact plus the templates and sub-modules.

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

        define([
            'angular'
        ], function (angular) {
            'use strict';
            
            var userLogin = angular.module('userLogin', [
                'ui.router',
                'userLogin.mySubModule'
                'userLogin.mySubAnotherModule'
                'siblingModule'
                ...
            ]);
            
            myModule.config = function(){
                ...
            };
        });


If this module is children of another module or sub-module, use the dot notation into de module declaration:  
E.g.: *(the module home is parent of module signIn which are parent of module userLogin)*

        ...
        var userLogin = angular.module('home.signIn.userLogin', [
        ...

The **module index** works like a "manifest" of what is the content of the module, which are the sub-modules
and also which outer modules are dependencies. All modules must have a index file, always with the same
name, **index.js**:

* *index.js*

        define([
            'user-login', 
            'controllers/user.controller',
            'directives/login-form.directive'
            'services/login.service',
            ...
            'modules/my-submodule/index',
            'modules/my-another-submodule/index'
            '../sibling-module/index'
        ]);

*To add other modules as dependency, just include they indexes files* 


### The Root Module and the Starting Structure

In your project resources directory there'll be a lot of configuration files, like npm, bower
gulp, etc. So to don't mix this files with our application source, we create a module that acts
like the root of the module dependencies tree.
This is the **App** module which loads all your main application submodules. The global configurations
of you application is also made here.

* *app.js*

        define([
            "angular"
        ], function (angular) {
            'use strict';
        
            // Loading main submodules
            var app = angular.module("app", [
                'ui',
                'commons',
                'system',
                'user',
                ...
            ]);
        });

And the index file from App module:

* *index.js*

        define([
            'ui/index',
            'commons/index',
            'system/index',
            'user/index',
            ...
        ]);

There's one difference in the module app. It's because it's not present in the name of the submodules.
The explication is simple, it's implicit that your main modules are under his hood, so there's no
need to explicitly declarations. 

**Wrong**:
    
    var bar = angular.module('app.foo.bar', [ ... ]);

**Correct**:

    var bar = angular.module('foo.bar', [ ... ]);


And then, you'll have your require js main file, where you describe you dependencies and shim
configurations:

    require.config({
        baseUrl: './',
        config: {
            angular: {
                noGlobal: true
            },
            ...
        },
        paths: {
            angular: 'vendor/angular/angular
            ...    
        },
        shim: {
            ...    
        },
        deps: ['ng-bootstrap']
        
And you can use a lib to bootstrap your angular application. This case is using the ng-bootstrap
with the domReady RequireJS plugin.  
Ex.:

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
        
Now, you can already run you application using AngularJS and RequireJS in the proposed architeture.

# Code Conventions
