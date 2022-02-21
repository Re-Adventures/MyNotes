For executing script, the script should be inside script tag or we can just include our javascript file
`<script src="myJSFile.js"></script>`

For printing data

We can use document.getElementById(id) to access html element
And then use the innerHTML property on that to print on that specific element

```html
<p id='para'></p>/

<script>
  var x = 10;
  document.getElementById(para).innerHTML = x;
</script>
```


// For declaring a varible

```js
var x = 10;
var x = '10';
var x = {firstName:"John", lastName:"Doe"}; //object

//array
var x = [i1, i2, ...]
or
var x = Array(i1, i2, ...)

/*
Creating Elements dynamically
*/

var my_elem = document.createElement("p");
var my_text = document.createTextNode("Some text");
my_elem.appendChild(my_text);
document.body.appendChild(my_elem);
```


## objects

* An object in JavaScript is an entity with property and type.
* A property is a variable attached to the object.
* These properties define the characteristics of the object.
* For example, car can be an object with properties color, model, etc.
* We can access the object's property using dot (.) operator.
* In this example, car is an object with model as property

Example
```js
var car = new Object();
car.model = 'Ford';
console.log(car.model);


/*Function*/

function mul(v1, v2){
  return v1 * v2;
}

var func = function ...(){

}
```

Javascript also have arrow functions
```js
x = (arg1, arg2, ...) => someOperations;
```

*We can pass variable number of arguments to function and access them using arguments keyword

```js
function summ(){
  for(let i=0; i<arguments.length; ++i){
    total += arguments[i];
  }
}
```

# AJAX

* It stands for Asynchronous Javascript And XML
* AJAX uses Javascript to send & receive data without a page refresh
* AJAX is not a programming language, it is a technology
* The data that we load is mostly XML but it can be plain text, HTML, JSON, etc.

## Sample AJAX Request

```js
// Object creation
var xhr = new XMLHttpRequest();

// Specify the parameters   false here because it is an async request
xhr.open("METHOD", url, false);

// Start the async request and wait for response
xhr.send(null);

// Now we can display it
var elem = document.getElementById("content");
elem.innerHTML = xhr.responseText;

```

## Expoting functions

We can have a separate file which contains functions which can be imported & used in another file

But first we need to export those functions

```js
function sum(a, b) {
  return a+b;
}
function difference(a, b) {
  return a-b;
}
export { sum, difference }  // separate methods using comma
export { sum as add, difference as minus } // aliasing
```

Then in the main file we need to import these

```js
import { sum, difference } from 'common_methods'  
console.log(sum(2, 3));         // 5
console.log(difference(5, 2));  // 3
```

Or import as an object
```js
import 'common_methods' as utils;
console.log(utils.sum(2, 3));         // 5
console.log(utils.difference(5, 2));  // 3
```

## Generators

Generators are functions which can be paused and later resume the code execution using a generator function.


```js
function *name(params) { }
```


```js
function *foo() {
  yield 'hi';
  yield 'hello';
}
let iterator = foo();
iterator.next().value; //Object {value: "hi", done: false}
iterator.next(); //Object {value: "hello", done: false}
iterator.next(); //Object {value: undefined, done: true}
```

We can also use yield to iterate

```js
function *foo() {
  yield* ['hi', 'hello'];
}
```




