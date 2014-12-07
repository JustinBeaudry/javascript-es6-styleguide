# JavaScript ES6 Style Guide

*Opinionated JavaScript ES6 style guide for teams by [@tim_walker](//twitter.com/tim_walker)*

This style guide is specific to new ES6 features in JavaScript and is based on Google's JavaScript Style Guide. This is meant to be a starting point for teams to maintain consistency with new ES6 features, especially around OO programming with classes.

## Community Credit
The conventions this style guide follows were inspired by John Papa's Angular JS Style Guide:

https://github.com/johnpapa/angularjs-styleguide

## Table of Contents

  1. [Google JavaScript Style Guide](#google-javascript-style-guide)
  1. [IIFE](#iife)
  1. [One Class per File](#one-class-per-file)
  1. [Private Functions and Properties](#private-functions-and-properties)
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

**Private Functions and Properties**: JavaScript does not have accesors like OO languages like Java (i.e. "private", "public", etc.).  There are ways to encapsulate functions and properties using closures (see: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Closures).  I recommend against this in favor of following the Google Style Guide naming convention for private functions and properties, and allow them to remain public.

*Why?*: Using closures to encapsulate functions causes issues when using "this" within the context of the private function.  The value of "this" is no longer the instance of the class within the context of the private function.  In order to get a handle to the instance of the class inside a private function, you have to pass the instance as a parameter of the private function.  Even worse, if you have a private function that calls another private function that needs to access the instance of the class, you have to pass the instance down to that function as well and so on.  This style guide makes a trade off to NOT create true private functions to avoid the pain of passing around the instance of the current class.

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
  
  window.vehicles = {};
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
  
  window.vehicles = {};
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

## Class Structure

**Class Structure**: Inside of a class, group functions and properties in the follow order:
- constructor
  - private properties
  - public properties
- get/set functions (keep get/set together for same property, with the get before the set, even if one is private and the other is public)
- private functions
- public functions

*Why?*: Grouping functions and properties based on type and private/public makes a file extremely easy to browse through and improves maintainability.

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
  
  // constructor
  constructor() {
    this.publicProperty = 'foo';  // public
    this.privateProperty_ = 'bar'; // private
    this.publicPropertyTwo = 2;  // public
    this.isRunning_ = false; //private
    ...
  }
  
  // public setter
  set isRunning(value) {
    isRunning_ = value;
  }
  
  // private function
  privateFunction_() {
    if(this.isRunning) {
      ...
    }
  }
  
  // public function
  drive() {
    this.privateFunction_();
    ...
  }
}
```

```javascript
/* recommend */ 
class Car {
  // constructor
  constructor() {
    this.privateProperty_ = 'bar'; // private
    this.isRunning_ = false; //private
    
    this.publicProperty = 'foo';  // public
    this.publicPropertyTwo = 2;  // public
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
  
  // private function
  privateFunction_() {
    if(this.isRunning) {
      ...
    }
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
}
```

**[Back to top](#table-of-contents)**

## Using Arrow Functions to Preserve "this"

**Using Arrow Functions to Preserve "this"**: When using anonymous functions, use arrow functions.

*Why?*: Arrow functions lexically bind the "this" value.  When used inside the context of a class, you can access "this" and it will be the instance of the class.

*Why?*: Using arrow functions help avoid having to assign "this" to a variable to then be used inside the anonymous function closure.

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
    // assigning "this" to "instance" to be used inside the anonymous function
    var instance = this;
    var callback = function() {
      if(instance.isRunning) {
        ...
      }
    };
    
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

**Callbacks**: If a callback is too large, instead of using an anonymous arrow function, use a private class function.  There is not black and white definition for a long running function. Be pragmatic, it should be clear when a function is too long.

*Why?*: Long running anonymous arrow functions can make your code hard to read and maintain.  No different than any other long running function.

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
  }
  
  drive() {
    this.start_(this.startCallback_);
  }
}
```

**[Back to top](#table-of-contents)**
