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

## Calling using JS
Once we defined our funtion, we need to export that function so that it is visible to our javascript. This can be done using following syntax:

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

Now we need to convert this wat file to wasm file


Finally we can call this function using js using the following code snippet
```js
WebAssembly.instantiateStreaming(fetch('filename.wasm'))
  .then(obj => {
    console.log(obj.instance.exports.MyAddFunction(1, 2));
  });
```

