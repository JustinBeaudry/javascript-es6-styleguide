# JavaScript ES6 Style Guide

*Opinionated JavaScript ES6 style guide for development teams by [@tim_walker](//twitter.com/tim_walker)*

This style guide is specific to new ES6 features in JavaScript and is based on Google's JavaScript Style Guide. This is meant to be a starting point for development teams to maintain consistency with new ES6 features, especially around OO programming with classes.

## Community Credit
The conventions this style guide follows were inspired by John Papa's Angular JS Style Guide:

https://github.com/johnpapa/angularjs-styleguide

## Table of Contents

  1. [Google JavaScript Style Guide](#google-javascript-style-guide)
  1. [IIFE](#iife)
  1. [One Class per File](#one-class-per-file)
  1. [Private Functions and Properties](#private-functions-and-properties)
  1. [Dependencies](#dependencies)  
  1. [Class Structure](#class-structure)
  1. [Using Arrow Functions to Preserve "this"](#using-arrow-functions-to-preserve-this)
  1. [Callbacks](#callbacks)
  
## Google JavaScript Style Guide

**Google JavaScript Style Guide**: As mentioned above, this style guide builds on Google's JavaScript Style Guide.  You will want to review this style guide to give you some context for conventions used in this style guide: 

https://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml

**[Back to top](#table-of-contents)**

## IIFE

**IIFE**: You will see the IIFE (immediately invoked function expression) pattern used in some of the examples.  To better understand this pattern and why its used see the following: 

http://addyosmani.com/resources/essentialjsdesignpatterns/book/#detailnamespacing (see #5)

**[Back to top](#table-of-contents)**

## One Class per File

**One Class per File**: Only one class should be declared per file and the file name should match the class name.  If the class is named "Car" then the file should be named "car.js".

*Why?*: By only having one class per file and naming the class and the file the same, you make it much easier to find class declarations by simply browsing your project.  Having multiple classes inside of one file also becomes a maintenance nightmare for a handful of reasons.

```javascript
/* avoid - all the classes below are container in a file called vehicle.js */
class Sedan {
  constructor() {
    ...
  }
    
  drive() {
    ...
  }
}

class Coupe {
  constructor() {
    ...
  }
    
  drive() {
    ...
  }
}
  
class Truck {
  constructor() {
    ...
  }
  
  drive() {
    ...
  }
}
```

```javascript
/* recommend - separate files for each class */

/* sedan.js */
class Sedan {
  constructor() {
    ...
  }
    
  drive() {
    ...
  }
}

/* coupe.js */
class Coupe {
  constructor() {
    ...
  }
    
  drive() {
    ...
  }
}

/* truck.js */    
class Truck {
  constructor() {
    ...
  }
  
  drive() {
    ...
  }
}
```

**[Back to top](#table-of-contents)**

## Private Functions and Properties

**Private Functions and Properties**: JavaScript does not have accessors like OO languages (i.e. private, public, protected, etc).  There are ways to encapsulate functions and properties using closures (see: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Closures).  True private functions and properties are recommended against this in favor of using the private naming convention from the Google JavaScript Style Guide, and allow functions and properties to remain public.

*Why?*: Using closures to encapsulate functions causes issues when using "this" within the context of the private function.  The value of "this" is no longer the instance of the class within the context of the private function.  In order to get a reference to the instance of the class inside a private function, you have to pass the instance as a parameter of the private function.  Even worse, if you have a private function that calls another private function that needs to access the instance of the class, you have to pass the instance down to that function as well and so on.  This style guide makes a trade off to NOT create true private functions to avoid the pain of passing around the instance of the current class.

```javascript
/* avoid - client side example with true private functions and properties */ 
/* Using the IIFE pattern as a closure  */
(function() {
  var privateProperty = 'hello world';
  
  function privateFunction(instance) {
    if(instance.isRunning) {
      ...
    }
  }
  
  class Car {
    constructor() {
      ...
    }
    
    get isRunning() {
    }
    
    drive() {
      // We must pass the "this" (the current instance) to the private function
      privateFunction(this);
      ...
    }
  }
  
  window.vehicles = window.vehicles || {};
  window.vehicles.Car = Car;
})();
```

```javascript
/* avoid - node.js example with true private functions and properties, require.js provides closure */ 
var privateProperty = 'hello world';

function privateFunction(instance) {
  if(instance.isRunning) {
    ...
  }
}

class Car {
  constructor() {
    ...
  }
  
  get isRunning() {
  }
  
  drive() {
    // We must pass the "this" (the current instance) to the private function
    privateFunction(this);
    ...
  }
}

module.exports = Car;
```

```javascript
/* recommend - client side example using naming convention for private functions and properties */ 
/* Still using IIFE to avoid polluting global namespace (window) */
(function() {
  class Car {
    constructor() {
      this.privateProperty_ = 'hello world';
      ...
    }
    
    get isRunning() {
    }
    
    privateFunction_() {
      if(this.isRunning) {
        ...
      }
    }

    drive() {
      // We do NOT need to pass the "this" to the private function
      this.privateFunction_();
      ...
    }
  }
  
  window.vehicles = window.vehicles || {};
  window.vehicles.Car = Car;
})();
```

```javascript
/* recommend - node.js example using naming convention for private functions and properties */ 
class Car {
  constructor() {
    this.privateProperty_ = 'hello world';
    ...
  }
  
  get isRunning() {
  }
  
  privateFunction_() {
    if(this.isRunning) {
      ...
    }
  }
  
  drive() {
    // We do NOT need to pass the "this" to the private function
    this.privateFunction_();
    ...
  }
}

module.exports = Car;
```

**[Back to top](#table-of-contents)**

## Dependencies

**Dependencies**: Dependencies of the class should be at the very top of the file (assuming you are not using a DI framework that uses something like constructor injection)

*Why?*: It needs to be clear what the dependencies of the class are.  This is very important to both testing and maintenance.

```javascript
/* avoid - client side example */ 
(function() {
  class Car {
    constructor() {
      ...
    }
    
    drive() {
      var Dependency = window.someNamespace.Dependency;
      var dependency = new Dependency();
      dependency.foo();
      ...
    }
  }
  
  window.vehicles = window.vehicles || {};
  window.vehicles.Car = Car;
})();
```

```javascript
/* avoid - node.js example */ 
class Car {
  constructor() {
    ...
  }
  
  drive() {
    var Dependency = require('dependency');
    var dependency = new Dependency();
    dependency.foo();
    ...
  }
}

module.exports = Car;
```

```javascript
/* recommend - client side example */ 
(function() {
  var Dependency = window.someNamespace.Dependency;
  
  class Car {
    constructor() {
      this.dependency_ = new Dependency();
      ...
    }
    
    drive() {
      this.dependency_.foo();
      ...
    }
  }
  
  window.vehicles = window.vehicles || {};
  window.vehicles.Car = Car;
})();
```

```javascript
/* recommend - node.js example */ 
var Dependency = require('dependency');

class Car {
  constructor() {
    this.dependency_ = new Dependency();
    ...
  }
  
  drive() {
    this.dependency_.foo();
    ...
  }
}

module.exports = Car;
```

**[Back to top](#table-of-contents)**

## Class Structure

**Class Structure**: Inside of a class, group constructs in the follow order:
- dependencies
- constants
- class
  - constructor
    - properties
  - get/set functions
  - functions

For properties and functions, order by access:
- public
- private

For get/set functions, keep the related get and set together (regardless of access), with the get before the set:
- get function
- set function

Last, apply order within these groups by static or non-static:
- static
- non-static

This would result in this full structure:
- dependencies
- constants
- class
  - constructor
    - public properties
    - private properties
  - static get/set functions
  - get/set functions
  - public static functions
  - public functions
  - private static functions
  - private functions

*Why?*: Grouping constructs based on type, private/public, and static/non-static makes a file extremely easy to browse through and improves maintainability.

*Why?*: For get/set functions, even if one is private and the other is public, you want to keep them together.  Breaking up related get/set functions become a maintainability problem.

```javascript
/* avoid */ 
class Car {
  // public function
  start() {
    ...
  }
  
  // public getter
  get isRunning() {
    return isRunning_;
  }
  
  // static public getter
  static get foo() {
    ...
  }
  
  // static public function
  static bar() {
    ...
  }
  
  // constructor
  constructor() {
    this.publicProperty = 'foo';  // public
    this.privateProperty_ = 'bar'; // private
    this.publicPropertyTwo = 2;  // public
    this.isRunning_ = false; //private
    var Dependency = require('dependency'); // dependency
    this.dependency_ = new Dependency(); // dependency instance
    ...
  }
  
  // public setter
  set isRunning(value) {
    isRunning_ = value;
  }
  
  // public function
  drive() {
    this.privateFunction_();
    ...
  }
  
  // private function
  privateFunction_() {
    if(this.isRunning) {
      ...
    }
  }
}

module.exports = Car;
```

```javascript
/* recommend */ 
var Dependency = require('dependency');

const SOME_CONSTANT = 2;

class Car {
  // constructor
  constructor() {
    this.publicProperty = 'foo';  // public
    this.publicPropertyTwo = 2;  // public
    
    this.dependency_ = new Dependency(); // private, dependency instance
    this.privateProperty_ = 'bar'; // private
    this.isRunning_ = false; //private
    ...
  }
  
  // static public getter
  static get foo() {
    ...
  }
  
  // public getter
  get isRunning() {
    return isRunning_;
  }
  
  // public setter
  set isRunning(value) {
    isRunning_ = value;
  }
  
  // static public function
  static bar() {
    ...
  }
  
  // public function
  start() {
    ...
  }
  
  // public function
  drive() {
    this.privateFunction_();
    ...
  }
  
  // private function
  privateFunction_() {
    if(this.isRunning) {
      ...
    }
  }
}

module.exports = Car;
```

**[Back to top](#table-of-contents)**


## Using Arrow Functions to Preserve "this"

**Using Arrow Functions to Preserve "this"**: When using anonymous functions, use arrow functions.

*Why?*: Arrow functions lexically bind the "this" value.  When used inside the context of a class, you can access "this" and it will be the instance of the class.

*Why?*: Using arrow functions avoids the pattern of having to pass "this" to an IIFE that wraps the anonymous function.

```javascript
/* avoid */ 
class Car {
  constructor() {
    isRunning_ = false;
    ...
  }
  
  get isRunning() {
    return isRunning_;
  }
  
  start_(callback) {
    if(callback) {
      callback();
    }
    ...
  }
  
  drive() {
    // have to pass this into IIFE to return callback
    var callback = (function(instance) {
      return function() {
        if(instance.isRunning) {
          ...
        }
      };
    })(this);
    
    this.start_(callback);
  }
}
```

```javascript
/* recommend */ 
class Car {
  constructor() {
    isRunning_ = false;
    ...
  }
  
  get isRunning() {
    return isRunning_;
  }
  
  start_(callback) {
    if(callback) {
      callback();
    }
    ...
  }
  
  drive() {
    // using arrow function for callback
    var callback = () => {
      if(this.isRunning) {
        ...
      }
    };
    
    this.start_(callback);
  }
}
```

**[Back to top](#table-of-contents)**

## Callbacks

**Callbacks**: If a callback has too many lines of code, instead of using an anonymous arrow function, use a private class function.  There is not a black and white definition for how many lines of code is too much for a callback or in general for a function. Be pragmatic, it should be clear when a function has too many lines of code.

*Why?*: Anonymous arrow functions with too many lines of code within another function can make your code hard to read and maintain.  This is no different than any other function that has too many lines of code and needs to be broken up.

```javascript
/* avoid */ 
class Car {
  constructor() {
  }
  
  start_(callback) {
    if(callback) {
      callback();
    }
    ...
  }
  
  drive() {
    var callback = () => {
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
      ...
    };
    
    this.start_(callback);
  }
}
```

```javascript
/* recommend */ 
class Car {
  constructor() {
  }
  
  start_(callback) {
    if(callback) {
      callback();
    }
    ...
  }
  
  startCallback_() {
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
    ...
  }
  
  drive() {
    this.start_(this.startCallback_);
  }
}
```

**[Back to top](#table-of-contents)**
