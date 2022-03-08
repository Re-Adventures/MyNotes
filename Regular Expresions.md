# Regular Expressions

Regular Expressions are search patterns which can be used to search for that patterns inside of a string.

I am using JS for testin these regexes

* Simple word matching using test()

```js
var helloString = "Hello world"
var myRegex = /world/
myRegex.test(helloString);

// return true or false
```

* Matching from multiple words

For that we use the `|` (or) character

Example: `regex = /Hey|Hi|Ahoy/`


* Getting the word using match()

test() only returs a bool value, but if we want the matched word we need to use match() from the string & pass the regex

Example: `string.match(regex)`

* Case insensitive search

We just need to pass the `i` flag in the regex

Example: `/Hello/i`

* Matching all the words

The regex we used earlier only return the first occurance, if we want all the occurences, we need to use the `g` flag

Example: `/Hello/g`

* Wildcard for single character

When we want to match words in which we dont know the actual character at a specific index, we can use the `.` character

Example: `/.un/`

The above regex will match fun, run, sun, etc.

* Wildcard matching character from a specific set of characters

Example: `/b[a,i,e]n/`

The above regex will only match ban, bin & ben

We can also match a range of letters
Example: `/[a-z]/` `/[1-9]/` `/[3-7a-j]/`
          a to z     1 to 9    3 to 7 or a to j


* Negated character set

When we dont want something to match, we can use the `^` character for that


Example: `/[^0-9]/`
          Not to match numbers 0 to 9


* Matching one or more times

`/a+/` Matches one or more a

* Matching zero or more times

`/hi*/` match h & then i zero or more times





