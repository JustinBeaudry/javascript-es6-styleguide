# ES6 JavaScript Style Guide

*Opinionated ES6 JavaScript style guide for teams by [@tim_walker](//twitter.com/tim_walker)*

This style guide is specific to new ES6 features in JavaScript and is based on Google's JavaScript Style Guide. This is meant to be a starting point for teams to maintain consistency with new ES6 features, especially around OO programming with classes.

## Community Credit
The conventions this style guide follows were inspired by John Papa's Angular JS Style Guide:

https://github.com/johnpapa/angularjs-styleguide

## Table of Contents

  1. [Google JavaScript Style Guide](#google-javascript-style-guide)
  1. [One Class per File](#one-class-per-file)
  1. [Private Functions and Properties](#private-functions-and-properties)
  1. [Class Structure](#class-structure)
  1. [Using Arrow Functions to Preserve "this"](#using-arrow-functions-to-preserve-"this")
  
## Google JavaScript Style Guide

As mentioned above, this style guide builds on Google's JavaScript Style Guide.  You will want to review this style guide to give you some context for conventions used in this style guide: 

https://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml

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

**[Back to top](#table-of-contents)**

## Class Structure

**[Back to top](#table-of-contents)**

## Using Arrow Functions to Preserve "this"

**[Back to top](#table-of-contents)**
