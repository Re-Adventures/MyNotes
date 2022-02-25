First we need to install dotnet compiler. We will use the command line utility
We can download that from [here](https://dotnet.microsoft.com/download/dotnet)

For creating a project we need to run the following command
`dotnet new console`

This will create a project named similar to the folder name in which this command is run


On the directory there will be a `Program.cs` file where we can start writing our code.

We can directly start writing code without defining functions, classes, etc., but following is the template is generally used while coding 

```c#
namespace ProjectName{
  class Anything{
    static void Main(string[] Args){
      ....
    }
  }
}
```

## Printing to console

We can use the `Console.WriteLine()` function to print something to the screen with a terminating newline(\n). To avoid the last \n, we can just use `Console.Write()`

Example
```c#
Console.WriteLine("Hello World");
Console.Write("Hello World");
```

For printing a variable, we need to use placeholders

```c#
Console.Write("Hello {0}: {1}", name, age)
```

## Taking user input

We need to use `Console.ReadLine()` for taking user input

Example
```c#
var s = Console.ReadLine();
```

## Array

```cs
var myArray = new int[size] {val1, val2, ...};
```

When we are initializing the elements when declaring the array, we dont need to specify the size of array

## Loops

### While

```cs
while(condition){

}
```


### For Loop

```cs
for(int i=0; i<10; ++i){
  ....
}
```

### Foreach

Useful for iterating over array elements
```cs
foreach(var item in array){
  ...
}
```

## Switch

```cs
switch(variable){
  case 0:
    ....
    break;
  case 1:
    ....
    break;
  ....
  default:
    ....
    break;
}
```


## Function

Syntax:
```cs
static returnType functionName(argList){
  ....
  return;
}
```


## List

List is a fancy array which can support features like push pop dynamically

We need to import a module for using list which is `System.Collections.Generic`

```cs
using System.Collections.Generic;
var myList = new List<datatype>();
```


* List Methods
1. .Add
2. .Remove
3. .Contains(item)
4. .Insert(idx, item)
5. .Clear


## Classes

* We can use classes as a means for constructing structs like in C



Syntax
```cs
// It is a good practice to start public variables names with capital letters & private with small letter
class Employee{
  public int ID;
  public string Name;
  public int Phone;
  private int salary;
}

public static void Main(string[] Args){
  var emp = new Employee();
  emp.ID = 1234;
  emp.Name = "ASDF";
}
```

* We cant use var keyword for class variables

* Classes have properties which can `set` & `get` values

```cs
class Employee{
  public int ID;
  public string Name;
  public int Phone;
  private int salary;

  // Salary is a property here
  public int Salary{
    set{
      salary = value;
    }
    get{
      return salary;
    }
  }
}
```




