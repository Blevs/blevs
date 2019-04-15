+++
title = "Clojure Crash Course for JavaScript Developers"
description = """
  A quick trip through the world of Clojure while we prepare to implement the
  Wave Function Collapse algorithm and build a frontend with ClojureScript.
  """
tags = ["wfc"]
draft = false
+++

Welcome to the first post in my series detailing an implementation of
[Wave Function Collapse](https://github.com/mxgmn/WaveFunctionCollapse).

Before we start writing any code, we must talk about the programming language
we will be using --- Clojure. I assume most people following along are not
familiar with it, so I will teach you enough to read and follow along with
later articles.

Many of you may only have experience with a single programming language, and
the idea of learning another may seem daunting. Don't worry. Learning your
second language is much easier than the first, even though Clojure may seem
alien.

In fact, many of the concepts integral to Clojure have made their way into
JavaScript. Things like anonymous functions, map/filter/reduce and
immutability will be familiar to you.

If you would like to follow along, both [repl.it](https://repl.it/languages/clojure) and [clojurescript.io](https://clojurescript.io/) have
repls to get you started.


## Basic Syntax {#basic-syntax}

In JavaScript, we invoke functions the way we expect from math class:

```js
function inc(x) {
    return x + 1;
}

inc(1);
```

```js
2
```

Let's do the same thing in Clojure

```clojure
(inc 1)
```

```clojure
2
```

The parenthesis go on the outside. In Clojure, instead of invoking functions
with `f(x)`, we do `(f x)`. While initially confusing, it offers distinct
advantages. One is that our code is a list. This makes it data. We can edit,
process and modify our code using the same tools we use to work with data.
We won't get into that here, but it is very cool.

Second, more practically, is that it is almost all of the syntax you need to
know. With the exception of data literals (like vectors), and some common
macros, that is pretty much it.

That includes math:

In JavaScript

```js
1 + 1
```

```js
2
```

In Clojure

```clojure
(+ 1 1)
```

```clojure
2
```

Notice how there are no commas between arguments. In Clojure, we use
whitespace to separate arguments.

It is weird, right? But this lets us do things like this:

```clojure
(+ 1 2 3 4 5 6)
```

```clojure
21
```

That discomfort you may feel reading it compared to JavaScript goes away
quickly, especially why you realize you don't have to constantly switch
between normal function, infix operators (like `+`), and member functions.


## Functions {#functions}

In JavaScript, we define functions like so

```js
function add(x, y) {
    return x + y
}

add(1, 2);
```

```js
3
```

In Clojure

```clojure
(defn add [x y]
  (+ x y))

(add 1 2)
```

```clojure
3
```

Here is the basic syntax:

```clojure
(defn function-name [argument1 argument2 ...]
  (body))
```

We use a macro called `defun` and specify a function name. Note that we can,
and prefer, to use hyphenated (or kebabed) names for our functions. We get
to do this because minus, `-,` is a function in Clojure, not a special symbol
that must be reserved.

Then, arguments are specified using a vector. Note the lack of commas.

Finally, the function body is defined. There is no explicit `return`
statement because the body's value is automatically returned.


### Anonymous Functions {#anonymous-functions}

In JavaScript, we have two syntaxes: the function operator and the arrow function.

```js
function(x) {
    return x + 1;
}

(x) => {
    return x + 1;
}
```

In Clojure we also have two syntaxes: the function expression and the
reader macro.

```clojure
(fn [x]
  (+ x 1))

#(+ % 1)
```

The first should be familiar and reasonable. Instead of using `defn`, we
write `fn` with no function name.

The second is more cryptic. The reader macro is transformed by the Clojure
parser and into the first form. It is indicated by a hashed parenthesis
`#()`. Inside, the `%` is evaluated to the first argument. We can use multiple
arguments by adding an integer.

```clojure
;; The same as the add function
#(+ %1 %2)
(#(+ %1 %2) 6 8)
```

```clojure
14
```


## Special Forms and Flow Control {#special-forms-and-flow-control}

What if we want to do multiple things inside of our function body? There is
always the `do` macro. This gives us an environment similar to JavaScript,
where each line is executed independently. The value of the last expression
executed is returned.

```clojure
(do
  (println "Hello, World!")
  (println 2)
  (+ 1 2))
```

```clojure
Hello, World!
2
3
```

As you can see, `println` is our equivalent to `console.log`.

Conditional logic is also important. In Clojure, `if` is an expression. It
evaluates a statement, and then if it is true or false it executes the
appropriate branch and returns its value.

```clojure
(if true
  "yes" ;; true branch
  "no") ;; false branch
```

```clojure
"yes"
```

We can use this returned value inside of other expressions

```clojure
(+ 2 (if false 3 4))
```

```clojure
6
```

This is very similar to the ternary operator in JavaScript

```js
2 + (false ? 3 : 4)
```

```js
6
```

Same effect, but look at how much simpler the Clojure syntax is.

If you want to chain together `if`'s and `else`'s like in JavaScript, use [cond](https://clojuredocs.org/clojure.core/cond)
instead.

We also have access to our same toolkit of logical operators, but in
Clojure they are functions instead of symbols.

```clojure
(println (and true false)
         (or true false)
         (not true)
         (= 1 2)
         (= 1 1))
```

```clojure
false
true
false
false
true
```

You may be weirded out to see the equals sign used to test equality instead
of assigning values.


## Variables {#variables}

In Clojure, we handle variables a bit differently. We tend to work with our
data by passing it from one function to the next, instead of storing it in
a variable and mutating it.

However, we still want to be able to associate constant values with names,
and to use the results of a computation in multiple places.

In the tradition we are more familiar with, Clojure has the `def` macro.

```clojure
(def a 1)
(println a)
(println (+ a 2))
```

```clojure
1
3
```

It defines a variable to a name. It is roughly equivalent to `const` in the
global scope in JavaScript. We **do not** use `def` inside functions to make
local variables.

Instead, the `let` macro is used.

```clojure
(let [a 1
      b (+ a 2)]
  (+ a b))
```

```clojure
4
```

Here is the basic syntax:

```clojure
(let [variable1 expression1
      variable2 expression2
      ...]
  (body))
```

It takes a vector of variable names and expressions. The expressions are
evaluated and the variables are bound to their value inside of the `let`'s
scope. We can reference earlier variables within the expressions for later
variables, as well as in the body.

Again, they are constant and we do not mutate their values.


## Data Structures {#data-structures}


### Numbers {#numbers}

Like JavaScript we have integer and decimal (floating point) numbers. We
also have fractional numbers.

Math is straightforward, with the expected symbols defined as function.

```clojure
1
2.5
(+ 3 4.6)  ; -> 7.6
(/ 2 4)    ; -> 1/2
(quot 2 4) ; 0
(mod 2 4)  ; 2
(2 / 4.0)  ; -> 0.5
(inc 2)    ; -> 3
(dec 3)    ; -> 2
```


### Strings {#strings}

Strings are also much as we expect, but unlike JavaScript they are always
enclosed by double quotes, or created with the `str` function.

```clojure
"I'm a string"
(str "test" "other")
```

```clojure
"testother"
```

Also unlike JavaScript, things like addition are not overloaded to handle
strings.

```clojure
(+ "1" "b")
```

```clojure
class java.lang.ClassCastExceptionclass java.lang.ClassCastExceptionExecution error (ClassCastException) at user/eval7088 (REPL:2).
java.lang.String cannot be cast to java.lang.Number
```

Instead, functions to work with strings are found in the `clojure.string`
namespace. A further discussion of namespaces is needed, but for now we
access their functions and variables by stating a namespace followed by a
slash and the name of what we want to access.

```clojure
(println
 (clojure.string/trim "     trim me    ")
 (clojure.string/lower-case "ABC")
 (clojure.string/reverse "olleh")
 (clojure.string/join ", " ["a" "b" "c"]))
```

```clojure
"trim me"
"abc"
"hello"
"a, b, c"
```


### Keywords {#keywords}

```clojure
:a
:i-am-a-keyword
(keyword "test") ; :test
```

Keywords are new. They are identifiers and you can think of them as fast,
constant strings. Keywords are commonly used as _keys_ in _maps_. They're
great and you will see why later.


### Vectors {#vectors}

We have already seen vectors in a few places, such as defining function
arguments. Like arrays in JavaScript, they are enclosed by square brackets.
They do not need commas by default, but they can be used.

```clojure
[1 2 3]
["a", 4, (fn [x] (+ x 2))]
(vector 5 6 7)
```

There are a host of functions for working with them. Here is a sample:

```clojure
;; index access
(nth [1 2 3] 0)   ; 1
(get [1 2 3] 0)   ; 1
;; conventiently get the first few elements
(first [1 2 3])   ; 1
(second [1 2 3])  ; 2
(rest [1 2 3])    ; [2 3]
;; add an element to the end
(conj [1 2 3] 4)
;; remove an element from the end
(pop [1 2 3])     ; [1 2]
;; get last element
(peek [1 2 3])    ; 3
;; replace element at index
(assoc ["a" "c" "c"] 1 "b") ; ["a" "b" "c"]
;; create a vector
(vector 1 2 3)    ; [1 2 3]
```

Note that all of these operations are **immutable**. For example:

```clojure
(def test-vector [1 2 3])
(println (conj test-vector 4))
(println test-vector)
```

```clojure
[1 2 3 4]
[1 2 3]
```

Modifying a vector, or any of our basic data structures, creates a new
copy and does not change the old value or any variables associated with
it. Your first thought might be that it sounds slow, wasteful or
inconvenient.

Immutable data structures are fast and wonderful. They are fast because
they are implemented using persistent data structures, which is to say
instead of creating an entirely new vector with every operation , Clojure
creates a vector that overlaps with the original and only stores the newly
created or modified elements.

While that does introduce some overhead it makes data incredibly
convenient to reason about. We never have to worry about a function
changing data. We get what is returned. Nothing more, nothing less. If you
have worked with modern JavaScript frameworks, you may realize how annoying
unexpected mutation can be and how frequently you visit [doesitmutate.xyz](https://doesitmutate.xyz).


### Lists {#lists}

We also have lists. They are like our code, but with a `'` in front
indicating it is a literal value.

```clojure
'(1 2 3)
(list 1 2 3) ; '(1 2 3)
```

In fact, it is our code

```clojure
(eval '(inc 1))
```

```clojure
2
```

They are similar to vectors, but each element is internally linked to the
next. We have to traverse the list to access elements, but it is very fast
to add items to the front. They are also _lazy_, which we will explain
later.

```clojure
;; index access (works, but is slow)
(nth '(1 2 3) 2)   ; 3
;; get doesn't work, because it is technically a key lookup
(get '(1 2 3) 0)   ; nil
;; conventiently get the first few elements
(first '(1 2 3))   ; 1
(second '(1 2 3))  ; 2
(rest '(1 2 3))    ; '(2 3)
;; add an element to the front
(conj '(2 3 4) 1)  ; '(1 2 3 4)
(cons 1 '(2 3 4))  ; '(1 2 3 4)
;; remove an element from the front
(pop '(1 2 3))     ; '(2 3)
;; get last element
(last '(1 2 3))    ; 3
```

If you are paying close attention you may have noticed something strange.
`conj` and `pop` add and removes items from the _beginning_ of a list, but add
and remove items from the _end_ of a vector.

This is because it is efficient to add and remove items from the beginning
of a list, and efficient to add and remove items at the end of a vector.

The efficient option is performed by default, and adding items to the end
of a list or the beginning of a vector is made deliberately difficult
because it means you should either pick another data structure or be aware
of the detriment to performance.


### Maps {#maps}

Maps are like Objects, but they lack prototype functions. Unlike
JavaScript, we can use any data type as the key. We can also use commas if
we like to make them easier to read.

```clojure
{:a 1 :b 2}
{:a 1, :b 2}
{1 2, 3 4}
(hash-map :a 1 :b 2)
```

We commonly use keywords as keys in maps because the are fast, and we can
also use them as functions to access their value

```clojure
(:b {:a 1 :b 2})
```

```clojure
2
```

You can think of this as a Clojure equivalent to `Object.property` syntax in
JavaScript. Note that it does not work with other types:

```clojure
("b" {"a" 1 "b" 2})
```

```clojure
class java.lang.ClassCastExceptionclass java.lang.ClassCastExceptionExecution error (ClassCastException) at user/eval7164 (REPL:2).
java.lang.String cannot be cast to clojure.lang.IFn
```

The functions that operate on them have significant overlap with vectors
as maps and vectors are implemented very similarly.

```clojure
;; get value
(get {:a 1 :b 2} :a)             ; 1
(:a {:a 1 :b 2})                 ; 1
(get {"a" 1 "b" 2} "b")          ; 2
;; add or change a value for a given key
(assoc {:a 1 :b 2} :c 3)         ; {:a 1 :b 2 :c 3}
(assoc {:a 1 :b 2} :b 3)         ; {:a 1 :b 3}
;; add a pair
(conj {:a 1 :b 2} [:c 3])        ; {:a 1 :b 2 :c 3}
;; merge maps
(merge {:a 1 :b 2} {:b 3 :c 4})  ; {:a 1 :b 3 :c 4}
;; remove a key and value
(dissoc {:a 1 :b 2} :b)          ; {:a 1}
;; get a subset of the map
(select-keys {:a 1 :b 2 :c 3} [:a :b]) ; {:a 1 :b 2}
```


### More {#more}

Clojure has more built in data structures, like sets, sorted-sets,
sorted-maps and a few more.

Clojure also has a very **large standard library** full of incredibly useful,
specific and well named functions for working with and creating data. You
can find them all on [ClojureDocs](https://clojuredocs.org/core-library). We will use some of them in the
concluding example problems.


## Sequence functions and Laziness {#sequence-functions-and-laziness}

One of the best things to happen to JavaScript was the addition of the
sequence functions `map`, `filter` and `reduce`. Map applies an operation each
element of a sequence and returns the resulting sequence. Filter performs a
test on each element and keeps those that pass. Reduce takes an accumulator
value and folds each element into that accumulator.

In Clojure, they all have the same syntax:

```clojure
(map function collection)
(filter function collection)
(reduce function collection) ;; function is of two variables
```

In JavaScript, if we wanted to double each number of an array:

```js
[1, 2, 3].map(x => x * 2)
```

```js
[2,4,6]
```

In Clojure the following are equivalent:

```clojure
(map (fn [x] (* x 2)) [1 2 3])
(map #(* % 2) [1 2 3])
```

```clojure
(2 4 6)
```

We can use a reducer to sum an array. In JavaScript:

```js
[1, 2, 3].reduce((acc, x) => acc + x)
```

```js
6
```

In Clojure:

```clojure
(reduce (fn [acc x] (+ acc x)) [1 2 3])
(reduce #(+ %1 %2) [1 2 3])
(reduce + [1 2 3])
```

```clojure
6
```

You can also see why it is an advantage to have `+` be a function.

We could use a filter to remove odd elements. In JavaScript:

```js
[1, 2, 3, 4].filter(x => x % 2 === 0)
```

```js
[2,4]
```

In Clojure:

```clojure
(filter (fn [x] (even? x)) [1 2 3 4])
(filter #(even? %) [1 2 3 4])
(filter even? [1 2 3 4])
```

```clojure
(2 4)
```

If you have a sharp eye, you may have noticed that `map` and `filter` returned
lists even though we gave them vectors. Herein lies a powerful difference
with the Clojure functions --- they are lazy.

What this means is that these functions do not actually execute on each
element of a sequence until that value is needed.

For example, the `take` function will take the first n elements from a
sequence. The `range` function will produce a list of numbers from 0 to n.
If we map over a collection, but only take the first 3, then those three
elements are the only ones whose values are calculated.

```clojure
(take 3 (map #(* % 2) (range 1000000000)))
```

```clojure
(2 4 6)
```

Don't believe me? Let's check:

```clojure
(take 3 (map #(or (println %) (* % 2)) (range 1000000000)))
```

```clojure
0
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30
31
(0 2 4)
```

It turns out that for most cases it is actually more efficient to
calculate them in chunks of 32 elements. So it is not a silver bullet for
avoiding needless computation, but it does help and it does let us do some
really cool things. Like the fact that `range`, when given no argument,
produces an infinite sequence of numbers. You read that right. Laziness
allows us to produce infinite sequences and only consume the part we need.

`cycle` takes a sequence and returns an infinitely repeating sequence.

```clojure
(take 10 (cycle [1 2 3]))
```

```clojure
(1 2 3 1 2 3 1 2 3 1)
```

`iterate` applies a function `f` to `x`, then `f` to that result, and so on,
infinitely.

```clojure
(take 10 (iterate #(* % 2) 1))
```

```clojure
(1 2 4 8 16 32 64 128 256 512)
```

If we don't want our sequence functions to be lazy, we use versions that
return vectors, which are not lazy sequences.

```clojure
(mapv #(* % 2) [1 2 3])
```

```clojure
[2 4 6]
```

```clojure
(filterv even? [1 2 3 4])
```

```clojure
[2 4]
```


## Threading Macros {#threading-macros}

Threading Macros are my favorite part of Clojure. Commonly, we perform
sequence functions one after another in a pattern similar to `map`, then
`filter`, then `reduce`.

In JavaScript, we do this via method chaining:

```js
[1, 2, 3, 4]
    .map(x => x * 3)
    .filter(x => x % 2 === 0)
    .reduce((acc, x) => acc + x);
```

```js
18
```

This looks nice and is easy to read. In Lisps, we would traditionally nest
these function calls.

```clojure
(reduce + (filter even? (map #(* % 3) [1 2 3 4])))
```

```clojure
18
```

This isn't too bad, but you can see how this gets out of hand. That is why
Clojure introduced the threading macros. They allow a convenient format for
us to repeatedly apply transformations to our data.

There is the thread-last macro, `->>`, that takes a value and applies it as
the last argument to the first expression, applies the result of that
expression as the last argument to the next, and so on. It is easier to
demonstrate:

```clojure
(->> [1 2 3 4]
     (map #(* % 2)) ;; [3 6 9 12]
     (filter even?) ;; [6 12]
     (reduce +))    ;; 18
```

```clojure
18
```

If you are not sure what is happening, let me illustrate with commas
indicating where the value is applied. (Remember, commas are whitespace, we
can put them anywhere).

```clojure
(->> [1 2 3 4]
     (map #(* % 2) ,,,) ;; [3 6 9 12]
     (filter even? ,,,) ;; [6 12]
     (reduce + ,,,))    ;; 18
```

We map over `[1 2 3 4]`, we `filter` over that result, and then `reduce` it.

The advantage over method chaining is clear as soon as you hit a JavaScript
function that is not a method.

Thread-last is traditionally used on operations that involve transforming
sequences. You will notice those functions take the collection last.

You may have guessed there is also thread-first macro, `->`. It is often used
when transforming singular data types and also when modifying maps. It
applies the data, and intermittent values, as the first argument to each
expression.

```clojure
(-> {:a 1 :b 2}
    (assoc ,,, :c 3)
    (dissoc ,,, :a))
```

```clojure
{:b 2, :c 3}
```

```clojure
(-> "     test string     "
    (clojure.string/trim ,,,)
    (clojure.string/upper-case ,,,)
    (clojure.string/reverse ,,,))
```

```clojure
"GNIRTS TSET"
```


## Loops {#loops}

We haven't talked about for or while loops yet. In most Lisps, we avoid
these by instead using recursion. Lisps tend to have a feature called
tail-call optimization that allows us to recur without allocating memory
for each function call added to the stack. Because Clojure is built on top
of Java it does not have this feature, and likely never will.

Instead, it has a `loop` macro that enables us to perform a sort of
recursion. It takes a vector of variables and expressions, and binds the
variables to the values of those expressions, just like `let`.

Then, in its body, it either evaluates to a value, or calls `recur`. `recur`
executes the loop body again, but binds the variables to the arguments.

```clojure
(loop [variable1 expression1
       variable2 expresssion2
       ...]
  (body))

(loop [variable1 expression1
       variable2 expresssion2
       ...]
  ;; commonly
  (if some-test
    result
    (recur (transform variable1) (transform variable2))))
```

Let's implement summing a list using traditional recursion.

```clojure
(defn sum-recursively [acc nums]
  (if (empty? nums)
    acc
    (sum-recursively (+ acc (first nums)) (rest nums))))

(defn sum [coll]
  (sum-recursively 0 coll))

(sum [1 2 3 4 5])
```

```clojure
15
```

In most other Lisps, this would be fine, but if we try it on a large
collection we will get a stack overflow.

```clojure
(sum (range 10000000))
```

```clojure
class java.lang.StackOverflowErrorclass java.lang.StackOverflowErrorExecution error (StackOverflowError) at user/eval5761$sum-recursively (REPL:5).
```

However, with some minor modifications we can safely use `loop`.

```clojure
(defn sum [coll]
  (loop [acc 0
         nums coll]
    (if (empty? nums)
      acc
      (recur (+ acc (first nums)) (rest nums)))))

(sum [1 2 3 4 5])
```

```clojure
15
```

```clojure
(sum (range 10000000))
```

```clojure
49999995000000
```


## Example Problems {#example-problems}

To give some context to what we have talked about, and some additional
content, here are some coding challenges to attempt and/or read to get a
feel for Clojure.


### Longest String in an Array {#longest-string-in-an-array}

Find the longest string in a given array.

<details>
<summary>
JavaScript
</summary>
<p class="details">

```js
const strings = ["short", "really really long!", "medium"]
strings.reduce((acc, x) => (x.length > acc.length ? x : acc))
```

```js
"really really long!"
```
</p>
</details>

<details>
<summary>
Clojure
</summary>
<p class="details">

```clojure
(def strings ["short" "really really long!" "medium"])
(reduce #(if (> (count %2) (count %1)) %2 %1) strings)
```

```clojure
"really really long!"
```

Advanced Solution: Clojure has [max-key](https://clojuredocs.org/clojure.core/max-key), which applies a function to its
other arguments and returns the value that gives the maximum result.

```clojure
(def strings ["short" "really really long!" "medium"])
(apply max-key count strings)
```

```clojure
"really really long!"
```

We must use [apply](https://clojuredocs.org/clojure.core/apply) because `max-key` expects elements given as arguments and
not a collection. The `apply` macro is similar to using the JS spread
operator, `...`, to the final collection in the argument list.
</p>
</details>


### Reverse a string {#reverse-a-string}

<details>
<summary>
JavaScript
</summary>
<p class="details">

```js
function reverseString(str) {
  return str.split("").reduce(function (a, b) {return b + a});
}
reverseString("Hello, World!")
```

```js
"!dlroW ,olleH"
```
</p>
</details>

<details>
<summary>
Clojure
</summary>
<p class="details">

```clojure
(clojure.string/join (reverse "Hello, World!"))
```

```clojure
"!dlroW ,olleH"
```
</p>
</details>


### Even Occurrences {#even-occurrences}

Find the first item that occurs an even number of times in an array.
Test Array: [1, 7, 2, 4, 5, 6, 8, 9, 6, 4].

<details>
<summary>
JavaScript
</summary>
<p class="details">

```js
const arr = [1, 7, 2, 4, 5, 6, 8, 9, 6, 4];
function evenOccurrence(arr) {
  const even = arr.reduce((a, x) => (Object.assign(a, {[x]: !(a[x] === undefined || a[x])})), {});
  return arr.find(x => even[x]) || null;
}
evenOccurrence(arr);
```

```js
4
```
</p>
</details>

<details>
<summary>
Clojure
</summary>
<p class="details">

```clojure
(def arr [1, 7, 2, 4, 5, 6, 8, 9, 6, 4]);
(defn even-occurrence [coll]
  (let [freqs (frequencies coll)]
    (some #(when (even? (get freqs %)) %) coll)))
(even-occurrence arr)
```

```clojure
4
```

Not that the Clojure solution correctly handles the case where we have both
integer and string elements that have the _same_ value, as both will be
distinct map keys, unlike in the JS solution where they are coerced to
strings.
</p>
</details>


### Sum Digits {#sum-digits}

Write a function that takes a positive integer and returns the sum of its
individual digits.

<details>
<summary>
JavaScript
</summary>
<p class="details">

```js
function sumOfDigits(num) {
    let acc = 0;
    do {
        acc += num % 10;
        num = Math.floor(num / 10);
    } while (num > 0);
    return acc;
}
sumOfDigits(490682);
```

```js
29
```
</p>
</details>

<details>
<summary>
Clojure
</summary>
<p class="details">

```clojure
(defn sum-digits [int]
  (->> int
       (iterate #(quot % 10))
       (take-while pos?)
       (map #(mod % 10))
       (reduce +)))
(sum-digits 490682)
```

```clojure
29
```
</p>
</details>


## Destructuring and Function Arguments {#destructuring-and-function-arguments}

At this point you have all the tools you need to start reading and writing
Clojure. However, there is still a lot about the language we haven't touched
on.

Clojure and JavaScript share the convenient ability to destructure data. It
is most commonly seen in function arguments, in `let` and in `loop`
declarations.

Sequences are destructured intuitively by placing a variable in each
position of the collection:

```clojure
(let [[a b c] [1 2 3]]
  (println a b c))
```

```clojure
1 2 3
```

Instead of the spread operator, an `&` is used to indicate we would like to
collect the rest of elements of a sequence:

```clojure
(let [[x & xs] [1 2 3 4]]
  (println x)
  (println xs))
```

```clojure
1
(2 3 4)
```

Note that the vector was destructured into a list.

We can also destructure while preserving a variable referencing the whole
structure using the `:as` keyword.

```clojure
(let [[x & xs :as coll] [1 2 3 4]]
  (println x)
  (println xs)
  (println coll))
```

```clojure
1
(2 3 4)
[1 2 3 4]
```

Map destructuring is slightly different. We specify a variable name and the
key whose value we would like to associate it with.

```clojure
(let [{a :a, b :b, c :c} {:a 1, :b 2}]
  (println a b c))
```

```clojure
1 2 nil
```

When we try to destructure a value not present in our data, it is assigned
`nil`. With maps, we can specify default data with `:or`.

```clojure
(let [{a :a, b :b, c :c, :or {c 8, b 12}} {:a 1, :b 2}]
  (println a b c))
```

```clojure
1 2 8
```

This is still pretty verbose, so there is an alternate syntax where we do
automatically associate keys with the appropriate variable name.

```clojure
(let [{:keys [abc def ghi]} {:abc 1, :def 2, :ghi 3}]
  (println abc def ghi))
```

```clojure
1 2 3
```

But because we can use anything as a key, there is instead `:strs` and `:syms`
for string and symbol keys respectively.

```clojure
(let [{:strs [abc def ghi]} {"abc" 1, "def" 2, "ghi" 3}]
  (println abc def ghi))
```

```clojure
1 2 3
```

```clojure
(let [{:syms [abc def ghi]} {'abc 1, 'def 2, 'ghi 3}]
  (println abc def ghi))
```

```clojure
1 2 3
```

We can use all of these in function arguments as well.

```clojure
(defn boxed-inc [[x]] [(+ x 1)])
(boxed-inc [2])
```

```clojure
[3]
```

There is a special case of destructuring used for when we want to give
functions keyword arguments.

```clojure
(defn sample-fn [a b c & {:keys [foo bar baz]
                          :or {foo true, bar false}}]
  (println a b c foo bar baz))

(sample-fn 1 2 3 :bar "bar")
```

```clojure
1 2 3 true "bar" nil
```

Keyword arguments are inherently optional and will be assigned `nil` if a
default value is not given.


## Atoms and Transients {#atoms-and-transients}
