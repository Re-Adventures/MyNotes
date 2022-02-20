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

