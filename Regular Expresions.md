# Regular Expressions

Regular Expressions are search patterns which can be used to search for that patterns inside of a string.

I am using JS for testing these regexes

## Simple word matching using test()

```js
var helloString = "Hello world"
var myRegex = /world/
myRegex.test(helloString);

// return true or false
```

## Matching from multiple words

For that we use the `|` (or) character

Example: `regex = /Hey|Hi|Ahoy/`


## Getting the word using match()

test() only returs a bool value, but if we want the matched word we need to use match() from the string & pass the regex

Example: `string.match(regex)`

## Case insensitive search

We just need to pass the `i` flag in the regex

Example: `/Hello/i`

## Matching all the words

The regex we used earlier only return the first occurance, if we want all the occurences, we need to use the `g` flag

Example: `/Hello/g`

## Wildcard for single character

When we want to match words in which we dont know the actual character at a specific index, we can use the `.` character

Example: `/.un/`

The above regex will match fun, run, sun, etc.

## Wildcard matching character from a specific set of characters

Example: `/b[a,i,e]n/`

The above regex will only match ban, bin & ben

We can also match a range of letters
Example: `/[a-z]/` `/[1-9]/` `/[3-7a-j]/`


## Negated character set

When we dont want something to match, we can use the `^` character for that

Example: `/[^0-9]/`
          Not to match numbers 0 to 9

* The `^` negates the matches only when it is inside of the `[]`, otherwise it has a different meaning


## Matching one or more times

`/a+/` Matches one or more a

## Matching zero or more times

`/hi*/` match h & then i zero or more times


## Greedy & Lazy matching

`s = <h1>Hello</h1>`

* Now if we use the regex `/<.*>/`, which is match `<` as the first character, `.*` means match anything zero or more times & then at the end match a `>`.

* On the above sentence, the regex will do a greedy matching & return the whole string because there is a `>` in the end of the string.

* But if we only want to end the match at the first occurence of `>`, ie. just return `<h1>`, we need to do a lazy matching.

* Lazy matching is done using the `?` character, so the regex would be `/<.*?>/`

## Matching at the start

* The `^` character can be used to match at the start of the string

Example:
```
regex `/^a/`
string1 = "asdf" ;match
string2 = "sadf" ;not a match
```

## Matching at the end

* The `$` character after the pattern will match the end of the string

Example:
```
regex `/f$/`
string1 = "asdf" ;match
string2 = "asfd" ;not a match
```

## Specifying the length

* We can specify the length of string to be matched using `{min, max}`

Example:
```
regex = /[a-zA-Z]{4,8}/ ; this will match strings from 4 to 8 chars long
regex = /Oh{4,}/  ;Matches h atleast 4 times
regex = /hi{2}/   ;Exactly matches 2 i afte h i.e hii
```

## Possible occurance of character

* We can use the ? to specify that a char may or may not be present

Example:
```
regex = /colou?r/  ;will match color & colour

; The above can also be written like this /colou{0,1}r/
```

## Look ahead




## Predefined patterns

1. `/\w/`: Matches A-Z, a-z, 0-9 & \_. Shorthand for `/[a-zA-Z0-9_]/`
2. `/\W/`: Matches everthing which is **NOT** A-Z, a-z, 0-9 & \_. Similar to `/[^\w]/`
3. `/\d/`: Matches all numbers. Shorthand for `/[0-9]/`
4. `/\D/`: Matches everthing except numbers. Shorthand for `/[^0-9]/`
5. `/\s/`: Matches whitespace including \r, \n, \t
6. `/\S/`: Excludes whitespaces

