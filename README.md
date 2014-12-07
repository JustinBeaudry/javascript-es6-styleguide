# ES6 JavaScript Style Guide

*Opinionated ES6 JavaScript style guide for teams by [@tim_walker](//twitter.com/tim_walker)*

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
  
## Google JavaScript Style Guide

As mentioned above, this style guide builds on Google's JavaScript Style Guide.  You will want to review this style guide to give you some context for conventions used in this style guide: 

https://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml

**[Back to top](#table-of-contents)**

## IIFE

You will see the IIFE (immediately invoked function expression) pattern used in some of the examples.  To better understand this pattern and why its used see the following: 

http://addyosmani.com/resources/essentialjsdesignpatterns/book/#detailnamespacing (see #5)

**[Back to top](#table-of-contents)**

## One Class per File
  
```javascript
/* avoid - same file vehicle.js */
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
/* recommend - seperate files for each class */

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

- **Private Functions and Properties**: There are not accesors in JavaScript like there are in say Java ("private", "public", etc.).  There are ways to encapsulate functions and properties using closures (see: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Closures).  I recommend against this in favor of following the Google Style Guide naming convention for private functions and properties, and allow them to remain public.
  
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

**[Back to top](#table-of-contents)**

## Using Arrow Functions to Preserve "this"

**[Back to top](#table-of-contents)**
