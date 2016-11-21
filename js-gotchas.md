# Javascript gotchas

## Variables

```
function f(x) {
    a = 5;
    return a+x;
}
```

---

```
f(x);
a; // 5
```

---

```
function f(x) {
    var a = 5;
    return a+x;
}
```

## Multiple script files

```
// script.js
var a = 5;
```

---

```
// otherscript.js (loaded after script.js)
a; // 5
```

---

```
// script.js
(function () {
    var a = 5;
})();
```

## Checking if a variable is defined

```
if (a != null) {
    console.log(a);
}
```

---

```
// ReferenceError: a is not defined
```

---

```
if (typeof a !== 'undefined' && a !== null) {
    console.log(a);
}
```


## Truthiness may not be what you expect

```
Boolean(undefined)  // false
Boolean(null)       // false
Boolean(false)      // false
Boolean(0)          // false
Boolean("")         // false
Boolean(NaN)        // false
 
Boolean(1)          // true
Boolean([])         // true
Boolean({})         // true
Boolean(function(){}) // true

// insider tip: !!x is the same as Boolean(x)
```


## Comparison is certainly not what you expect!

```
!![]            // true (because arrays are)
[] == false     // true

!!'0'           // true (because non-empty strings are)
'0' == false    // true

'' == 0         // true
```

Summa summarum: coercion leads to unexpected stuff. Better use the === / !== operators (which also require the type to be the same, i.e. no coercion allowed).


## Comparison special cases: null, undefined, NaN

```
!!null                  // false
!!undefined             // false
null == true            // false
null == false           // false
null == null            // true
undefined == undefined  // true
undefined == null       // true

!!NaN           // false
NaN == false    // false
NaN == null     // false
NaN == NaN      // false
```


## This is right, right?

```
if (typeof a !== undefined && a !== null) {
    console.log(a);
}
```

---

```
// typeof returns a string!
if (typeof a !== 'undefined' && a !== null) {
    console.log(a);
}
```


## Arithmetics with different types

```
var a = 5;
var b = '3';

a + b; // 53
a - b; // 2

// manually typecast to be sure:
'' + a;     // '5'
String(a);  // '5'
+b;         // 3
Number(b);  // 3
```


## Number rounding

```
0.1 + 0.2 == 0.3    // false
```


## Optional semicolons

```
function f(x) {
    return
    {
        a: 5
    };
}
```

---

```
f(x); // undefined
```

---

```
function f(x) {
    return {
        a: 5
    };
}
```


## HTML+Javascript: more hairy stuff

From WHATWG HTML Living Specification:

```
<!-- Do not use this style, it has a race condition! -->
 <img id="games" src="games.png" alt="Games">
 <!-- the 'load' event might fire here while the parser is taking a
      break, in which case you will not see it! -->
 <script>
  var img = document.getElementById('games');
  img.onload = gamesLogoHasLoaded; // might never fire!
 </script>
```


