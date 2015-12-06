# hooks

Hooks library provides full support for adding pre and post hooks to independent functions and functions in objects.

## I. Installation

`npm install hooks.js --save`

Use it like this:

```

// Hookify!
hooks.hookify(myObject);

...

// set hook before function
myObject.someFunction.pre(function(args, meta) {...});

// set hook after function
myObject.someFunction.post(function(args, meta) {...});

...

// now hooks are called before and after someFunction
myObject.someFunction();
```

It can be also very useful for hooking setters and getters if you don't want to put too much complex logic inside them in the class:

```
function myClass () {

  this._constructor = function() {
    this.setName.pre(function(args, meta){
      if (args.name.length < 5)
        throw new Error('Name is too short');
    });
    this.getName.pre(function(args, meta){
      if (args.name === undefined)
        throw new Error('Name is not defined');
    });
  };

  this.setName = function(name) {
    this.name = name;
    return this;
  };

  this.getName = function() {
    return this.name;
  };

}
```

You can also hookify only matching functions:

```
hooks.hookify(myObject);

myObject.pre('^get*$', function(args, meta) {
  console.log('Getter fired');
});

myObject.pre(new RegExp('^get*$'), function(args, meta) {
  var propertyName = meta.name.substr(3);
  propertyName[0] = propertyName[0].toLowerCase();
  if (this[propertyName] === undefined)
    throw new Error(propertyName + ' property is undefined');
});
```

## II. API - ASSIGN

### mount
Arguments: (@original_function:Array, @usePromise:Boolean(optional))

```
myFunction = hooks.mount(myFunction);
```

### hookify
Arguments: (@object:Object, @usePromise:Boolean(optional))

```
hooks.hookify(myObject);
```

## II. API - FUNCTION

You can use them on function on which you called first:

`myFunction = hooks.mount(myFunction);`

or

```
hooks.hookify(myObject);
var myFunction = myObject.myFunction;
```

### pre
Arguments: (@prehook_callback:Function)

```
myFunction.pre(function(args, meta){
  // Something
});
```

### post
Arguments: (@posthook_callback:Function)

```
myFunction.post(function(args, meta){
  // Something
});
```

## III. API - OBJECT

### pre
Arguments: (@regex:String||RegExp, @posthook_callback:Function)

```
myObject.pre('^get*$', function(args, meta) {
  // Something
});
myObject.pre(new RegExp('^get*$'), function(args, meta) {
  // Something
});
```

### post
Arguments: (@regex:String||RegExp, @posthook_callback:Function)

```
myObject.pre(new RegExp('^set*$'), function(args, meta) {
  // Something
});
```

## IV. Examples

### 1) Hookify

If you want to add hooks to all functions in the object:

```
var hooks = require('hooks.js');

...

var myCustomObject = {
  this.myFunction = function(arg1, arg2) {
  }
  this.myOtherFunction = function() {
  }
};

...

// Hookify!
hooks.hookify(myClassObj);

...

// Set custom hook actions
myCustomObject.myFunction.pre(function(args, meta) {
  console.log('Do sth before');
});
myCustomObject.myOtherFunction.post(function(args, meta) {
  console.log('Do sth after');
});

...

// Call functions
myClassObj.myFunction();
myClassObj.myOtherFunction();
myClassObj.myFunction();
```

### 2) Mount

If you want to add hooks to only one specified function:

```
var myFunction = function  (user_name) {
  // Something
}

...

// Mount hook to the function
myFunction = hooks.mount(myFunction);

...

// Set custom hook actions
myFunction.pre(function(args, meta) {
  console.log('Do sth before');
});

...

// Call function
myFunction('Anna');
```