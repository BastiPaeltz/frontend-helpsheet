
# Javascript

## Best Practices

* compare with `===`
* always use semicolons after statements


## Values

* boolean, number, string, array ...
* all values have **properties**
  * use `.` to read a property --> `value.property`
  * use `.` to assign a porperty --> `obj.foo = 123`
  * use `.` to invoke method --> `'hello'.toUpperCase()`

* primitive values = **immutable** and includes **booleans, numbers, strings `undefined` and `null`

## Objects 

* all **non primitive values** are objects
* **mutable**
* includes **arrays, plain objects and regex**

### comparison 

`({} === {})` -> evaluates to **false** --> **identity** gets compared  
`3 === 3` -> evaluates to **true** --> **content** gets compared

## undefined and null

* `ùndefined` means **no value**
* `var foo` , `function f(x) {return x}`, `obj.foo` evaluates to **undefined** (if f() gets called and foo doesn't exist or isn't initialized)
* `null` means **no object**
* **undefined and null have NO properties**
* **check** for undefined and null with `ìf (x === undefined || x === null)`
* `!x` evaluates also to `false` if x is `NaN`, `false`, `0` or `''` 

### typeof and instanceof

* typeof for **primitive values**
* instanceof for **objects**
* `typeof true` -> Boolean, `typeof 'abc'` -> String, `typeof {}` -> object, `typeof []` -> object
* **careful**: `typeof null` -> object, but `typeof undefined` -> undefined

### Truthy and Falsy

* everything except the following is considered `true`:
* undefined, null, false, 0, NaN and ''
* `Boolean([])` -> `true`

### And + Or

* `&&` -> if first operand is falsy, return it. Otherwise return 2nd operand
  * `123 && 'abc'` -> returns 'abc'
* `||` -> if first operand is truthy return it. Otherwise 2nd operand
  * `123 || 'abc'` -> returns 123

## Numbers

* everything is a **float**, `1 === 1.0` -> `true`
* **NaN** = Not A Number, `Number('abc')` -> `NaN`
* **Infinity** `3/0` -> `Infinity`

## Strings 

* escaped by **backslash**
* access single characters via **square brackets**, `str[1]`
* has Property `length` 
* **concetanate** with `+` 

## Conditionals

* **if**

    if (myvar === 0 ){
        //then
    }else if(myvar > 1){
        //else if 
    }else {
        //else
    }

* **switch**

    switch (fruit){
        case 'banana':
            //i like bananas
            break;
        case 'apple':
            //apples are fine as well
            break;
        default:
            //go here if fruit aint apple or banana
    }

## loops

* **for**
 
    for (var i=0; i < arr.length; i++){
        //do stuff
    } 

* **while** and **do-while**
  * same as in Java

* in all loops: `break` leaves loop `continue` starts new iteration

## Functions

    function add(x, y){
        return x+y;
    }

* you can **assign a function to a variable**

    var add = function(x,y){
        return x+y;
    }

* **functions can directly be passed to other functions**
* **function declarations are hoisted** - allows to refer to them even if they are declared later  

    function foo(){
        bar();
        function bar{
            return 'bar';
        }
    }
    
* **variables** are also hoisted, but assignments performed on them **not**

    function foo(){
        bar(); //error
        var bar = function(){
            return bar;
        }
    }

### arguments

* you can call **any function** with an **arbitrary amount of arguments** -> no error
* they will be made available in special variable `arguments.arguments`

    function f(){ return arguments;}
    var args = f(1, 2, 3);
    args.length --> 3
    args[0] --> 1

* **too many** arguments will be ignored (except by `arguments`), too **few** arguments produces:

    function(x,y){
        console.log(x, y);
    }
    f('hallo');
    --> 'hallo' undefined
    f()
    --> undefined undefined

* optional arguments assigned with `x = x || 0;`
* if a **fixed amount of arguments** is needed, use `arguments.length`

* converting `arguments` to **Array** -> method described in **Speaking Javascript p. 21** 

## Exception Handling

    function getPerson(id){
        if (id < 0) {
            throw new Error('ID must not be negative ' + id);
        }
        return { id : id };
    }

    function getPersons(ids){
        var result = [];
        ids.forEach(function(id){
            try {
                var person = getPerson(id);
                result.push(person);
            } catch (exception){
                console.log(exception);
            }
        });
        return result;
    }

### Strict mode

* enables more warnings and makes JS a cleaner language
* type as first line in your script `'use strict';`

## variable Scopes and Closures

    var x;
    x -> undefined
    y -> ReferenceError, y is not defined

* initialize and declare in **single statement** possible with 
`var x = 1, y = 2, z = 3;`
* recommended: **1 statement per line**
* declare at **beginning of function**

### Variable are function scoped

* scope of variable is always the declaring function -- **NOT the current block** 

### Variables are hoisted

* declaration is **moved** to beginning of function, **assignments not**
* nice example for understanding:
    function foo(){
        console.log(tmp); --> undefined -- NOT ReferenceError, means variable is recognized
        if (false){
            tmp = 3;
        }
    }

### Closures

* each function stays **connected to variables of functions that surround it**, even after it leaves scope of creation

    function createIncrementor(start){
        return function () {
            start++;
            return start;
        }
    }
    --> createIncrementor(5);
    --> inc();
    --> 6
    --> inc();
    --> 7
    ...
* A closure is a function plus the connection to the variables of its surrounding scopes -> `createIncrementor` returns a *closure*.
* Ja, es ist schwer zu verstehen!

## The IIFE Pattern: Creating a new scope

* used when an extra scope needs to be defined, for example to prevent variables from becoming global
  * pattern for this: **IIFE - immediately invoked function expression**
  * a function expression that is immediately called after definition
  * a **new scope inside the function** -> `tmp` doesn't get global

    (function () { // open IIFE
        var tmp = 3; // not a global var
    }()); // close IIFE

* **use case** - inadvertent sharing via **closures**
* see **Speaking Javascript p. 24** 

## Objects and constructors

* **single objects** - objects have **properties** 
* consider an object as a set of properties, where each property is a **key-value pair**
  * key is a String, value is any JS value

    var jane = {
        name : 'Jane',
        describe: function (){
           return 'Person named'+this.name;
        }
    };

* preceding object has properites `name` and `describe`
* you can **get and set** properties as well as create new ones

    jane.name; //get
    jane.name = 'Jürgen' //set
    jane.husband = 'Rainer';
    jane.describe()
    --> 'A person named Jürgen.'

* check **existency** of property with `in`, e.g. `'name' in jane` -> true
* not existing property returns `undefined`, `jane.foo !== undefined` -> false
* **remove property** with `delete`, `delete jane.husband`
* **arbitrary property keys** : `jane['name']` -> Jürgen

## Extract methods

* if you extract methods, it looses its connection **with the object** -> `this` is `undefined`
* example from above, try to **extract** the `describe` method from `jane`:
  * `var func = jane.describe;func();` -> TypeError :  Cannot read property 'name' of `undefined`
  * solution: use method `bind` (which all functions have)
  * `var funcDoneRight = jane.describe.bind(jane);funcDoneRight();` -> "Person named Jürgen."

## Functions inside a method

* every function has it'S own `this`, this is inconvenient for functions inside methods, example:

    var jane = {
        name : 'Jane',
        friends : ['Tarzan', 'Cheetah'],
        sayHiToFriends: function(){
            this.friends.forEach(function (friend){ //this doesn't refer to jane anymore -> not what we want
                console.log("Hi my"+friend+ "I am"+this.name);
            })
        }
    }
    --> sayHiToFriends() produces: 'TypeError cannot read property 'name' of undefined

* one solution might be: use **second parameter of forEach** -> `this.friends.forEach(function (friend){sayHi();}, this);`


## Constructors - Factories for objects

* introduces **inheritance** 
* functions become **constructors** when invoked via `new` 
* by convention : name starts with **Capital letters**

    function Point(x,y) {
        this.x = x;
        this.y = y;
    }
    // Methods
    Point.prototype.dist = function () {
        return Math.sqrt(this.x*this.x+this.y*this.y);
    };
* **prototype** contains an object with the methods
* invoke with `var point = new Point(1,1);`
* `point.x;` --> 1, `point.dist();` --> 2
* `point instanceof Point;` -> `true`


## Arrays

* `var array = [1, 2, 3];`
* **mutable** -> properties can be manipulated
  * `array.length = 1;` -> returns [1], `array[2] = 5` -> array is now [1, 5]
* `in` operator can be used to check for existance
  `1 in array` -> `true`
* arrays are **objects** and can have **custom properties**
  * `array.foo = 7;array.foo` returns 7

### Array methods

* `array.slice(1,2);` -> copy elements, params: begin, end -> end is not included
* `array.push(1000);` -> append to array
* `array.pop()` -> return first element
* `array.shift()` -> remove frist element
* `array.unshift(2000)` -> PREprend an element
* `array.indexOf(2000)` -> find index of element (in this case: returns 0) -> **returns -1 if element is not found**
* `array.join('-')` -> all elements in a single string, seperated by param -> default param is comma

### Iterating over arrays

* **two most important** : `forEach` and `map`
* forEach hands current element and its index to a function:
    [1, 2, 3].forEach(
        function (elem, index){
             console.log(index + ". " + elem);
    });
    --> returns: 0.1 , 1.2 , 2.3

* map **creates new array** by **applying a function** to **each element** of an existing array
    [1, 2, 3].map(function(x){ return x + 9;})
    --> returns following array: [10, 11, 12]

## RegEx

* delimited by **slashes** -> `/^abc$/`
* check for match with Method `test` -> `/^abc$/.test('aaab')` -> `true`
* capture groups with method `exec`
* **the returning array contains the complete match at array position 0, the 1st capture group at pos. 1 and so on**
* replace with method `replace` - see **Speaking Javascript p. 31**

## Math

* is an object with math functions
* `Math.abs(29)` -> 29, `Math.pow(3,2)` -> 9 (3 hoch 2)
* `Math.max(1, 2, -5)` -> 5, `Math.round(1,8)` -> 2, `Math.PI`


