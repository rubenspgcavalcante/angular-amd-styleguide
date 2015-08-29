# Angular AMD Style Guide
A style guide for AngularJS using asynchronous module definition *(using RequireJS)*, based on style
guides like the [John Papa Angular Style Guide] (https://github.com/johnpapa/angular-styleguide),
[Google Style Guide](http://google.github.io/styleguide/angularjs-google-style.html) and the architecture
of [Django apps](https://docs.djangoproject.com/en/1.8/intro/reusable-apps/).  
  
*By [Rubens Pinheiro](https://github.com/rubenspgcavalcante)*


## Table of Contents
1. [Architecture and File Structure](#architecture-and-file-structure)
    * [File Conventions](#file-conventios)
    * [Module Conventions](#module-conventions)
    * [The Main Module and the Starting Structure](#the-main-module-and-the-starting-structure)

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
* Templates
* Directives and Partials
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


### The Main Module and the Starting Structure