# Web Assembly

Notes taken from [here](https://developer.mozilla.org/en-US/docs/WebAssembly/Understanding_the_text_format)

The structure of a wat file is an [s expression](https://en.wikipedia.org/wiki/S-expression)

Syntax:
```wasm
(module (memoryN)(func))
```

All webasm code is bounded by a module. ???

## Datatypes

There are four datatypes in wasm file
1. i32
2. i64
3. f32
4. f64


## Functions

Syntax:
  `(func <signature> <locals> <body>)`

where:
* func keyword is used for defining a function
* Signature will specify the number of parameters, datatype of parameters & return type
* Locals are the local variables needed by the function
* Body will contain the instructions/code

Example
```wasm
(module
  (func $myFunction (param $p1 i32) (param $p2 i32) (result i32)
    (local $var1 f32)
  )
)
```

## Accessing parameters

There are two ways to access arguments:
1. using argument name
2. using index

```wasm
(module
  (func $add (param $p0 i32) (param $p1 i32) (result i32)
    local.get 0   ;;using index
    local.get $p1 ;;using variable name
    i32.add
  )
)
```

## Declaring variables

* We can use the `local` keyword for declaring variables

```wasm
(module
  (func $ReturnConst (result i32)
    (local $var1 i32)
    local.get $var1
    i32.const 1
    i32.add
  )
)
```

The instruction `i32.const 1` pushes 1 as i32 into the stack

## get & set methods

`local.get` & `local.set` are two methods which are used for pushing & popping values from stack respectively.

```wasm
(module
  (func $MyFunc (param $p0 i32) (result i32)
    (local $var0 i32)
    local.get $p0   ;;push value of $p0 into stack
    local.set $var0 ;;pop the value from top of stack into $var0
  )
)
```

## Calling other functions

We can call other function using `call` keyword

```wasm
(module
  ;; This func has index 0
  (func $SomeFunc (...) (result ...)
    ...
  )

  ;; This func has index 1
  (func $add (...) (result ...)
    ...
    call $SomeFunc
    ;; We can also do call 0

  )
)
```

## Calling WebAsm functions using JS
* Once we defined our funtion, we need to export that function so that it is visible to our javascript. This can be done using following syntax:

 ```wasm
(export "FunctionNameVisibleToJS" (func $MyFunction))
```
Example
```wasm
(module
  (func $add (param $p0 i32) (param $p1 i32) (result i32)
    local.get 0
    local.get 1
    i32.add
  )
  (export "MyAddFunction" (func $add))
)

```

* Now we need to convert this wat file to wasm file. For that there is a utility known as `wat2wam` which can be installed along with [wabt](https://github.com/WebAssembly/wabt)

`wat2wasm filename.wat`


* Finally we can call this function using js using the following code snippet
```js
WebAssembly.instantiateStreaming(fetch('filename.wasm'))
  .then(obj => {
    console.log(obj.instance.exports.MyAddFunction(1, 2));
  });
```

## Accessing JS Functions

* We can call JS functions from Webassembly. For that we need to first export the function from JS


```js
var import_object = {
  "MyFunctions": {
    "FunctionNameVisibleToWebAsm": (args) => Whatever operation
    /*"Log": (arg) => console.log(arg)*/
  }
}
```
* The object has to be two leveled, because Webasm uses two level namespaces

* Also when intantiating the WebAssembly, we need to pass the import_object object too.

```js
fetch("filename.wasm")
  .then(response => response.arrayBuffer())
  .then(bytes => WebAssembly.instantiate(bytes, import_object))
  .then(response => {
    // Our code
    response.instantiate.exports.FuncName(args);
  });
```


* On the webasm side we need to use the `import` keyword.

```wasm
(module
  (import "MyFunctions" "FunctionNameVisibleToWebAsm" (func $log (param i32)))
)
```
* Now we can just call func $log normally

* The import should always be at the start

## Global Variables

* We can create global variables using the `global` keyword

Syntax:
```wasm
(global $varName varType)
```

Example
```wasm
(global $g (mut i32) (i32.const 1)) ;;assigning 1 to $g
```

* We can also create Webasm global variables from JS

```js
var import_object = {
  "import": {
    "globalVar": new WebAssembly.Global({value: "i32", mutable: true}, 1234)
  }
}
```

On webasm side
```wasm
(global $g (import "import" "globalVar" (mut i32)))
```

For accessing & setting global variables, we have to use `global.get` & `global.set`

```wasm
  global.get $g
  ...
  global.set $g
```


## Memory

* We can consider memory as an array where we can store/retrieve data from a specific index

* We can define a memory region using `memory` keyword

Syntax:
```wasm
(memory 1)
```

* The 1 is the size of memory in pages. According to MDN, a page is 64KB

* There can be only one memory region

* We can resize the memory

* We can also create memory from JS

Syntax:

```js
var import_object = {
  "MyUtils": {
    "MyMem": new WebAssembly.Memory({initial: 1}) //size
  } 
};
```

On wasm side

```wasm
(import "MyUtils" "MyMem" (memory 1))
```

* For storing & retrieving the data from memory, we have two keywords `datatype.load` & `datatype.store`

Example:
```wasm
i32.load
i32.store
```

* Instead of a store, we can also use the `data` keyword for storing the data in memory

Example:
```wasm
(memory 1)
(data (i32.const 0) "Hello")
         ;; offset  Value
```


### Accessing data from memory

1. Store
* The store operation works on three values, two offsets & one constant

* The two offset are base address & offset from that base.

* From the three values, two are poped from stack & 1 from the instruction itself

The operation is of the form Memory[Base + offset] = value

Examples
```wasm
i32.const 1        ;;base
i32.const 1234     ;;value to store
i32.store offset=0 ;;offset from base, it can be skipped

;; It just means Memory[1 + 0] = 1234
```

2. Load
* The load operation takes two arguments, one from stack & one from the instruction itself

* The base is taken from stack & offset from the instruction

* The value retireved is stored on top of stack

Example
```wasm
i32.const 12        ;;base
i32.load  offset=0  ;;offset

;; Which is TOS = Memory[12 + 0]
```


## Random Notes

* According to wasm calling conventions, the parameters are pushed left to right.

* When performing operations like sub, add, the top element is the right operand while the 2nd element from top is left operand
```
Stack:
  v0  <-- Top of Stack
  v1
```

And if we perform sub, the equation will look like
  `v1 - v0`
