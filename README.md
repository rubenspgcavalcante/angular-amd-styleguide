# Angular AMD Style Guide
A style guide for AngularJS using asynchronous module definition *(using RequireJS)*, based on style
guides like the [John Papa Angular Style Guide] (https://github.com/johnpapa/angular-styleguide),
[Google Style Guide](http://google.github.io/styleguide/angularjs-google-style.html) and the architecture
of [Django apps](https://docs.djangoproject.com/en/1.8/intro/reusable-apps/).



## Table of Contents
1. [Architecture and File Structure](#architecture-and-file-structure)
    * [Module Conventions]()
    * [The Main Module and the Starting Structure]()

## Architecture and File Structure
The idea is to turn your angular application into small apps, making it more independent,
so you can reuse this small app in other projects too. The app in the style guide are the **modules**.
They're not only Angular modules, but also AMD modules too.

### Module Conventions
A module basically is consisted of:

* Controllers
* Templates
* Directives and Partials
* Services
* Factories
* Values
* Constants
* The Module Definition
* The Module Index

The **module definition** is the file where the Angular module is declared and assume the format:  

* *my-module.js*


        define([
            'angular'
        ], function (angular) {
            var myModule = angular.module('myModule', [
                'myOtherModule'
                'myModule.submodule'
                ...
            ]);
        })
