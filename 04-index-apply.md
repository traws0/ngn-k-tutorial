# Part 3: Indexing and Application

K is 0-indexed, which means arrays start at 0.

Indexing into an array takes the element at the given index and returns it.

Indexing into a dictionary takes the value that corresponds to a given key, and returns it.

Passing values to a function will return its corresponding result.

So why Arrays, dictionaries and functions together? Because they all do one single thing: They take a set of values, and return a single value. K hence treats them as the same thing: application.

The first method of application is called **at**. 



### `f@x` At

**Symbol:** `@`

**Args:** `(func/array/dict) @ (array/atom)`

**Description:** Applies a noun `f` given on its left to a single argument `x` given on its right.



For a function, `@` treats `x` like it is a single value, and provides it to `f`.

`(-:) @ 1` gives `-1`.

For a dictionary or an array, `@` can take a single index:

``(`a`b`c!1 2 3) @ `a`` -> `1`

`67 78 89 @ 1` -> `78`

or an array of indices:

``(`a`b`c!1 2 3) @ `a`c`b`` -> `1 3 2`

`67 78 89 @ 0 2` -> `67 89`


all forms of application behave in this manner i.e. they will always index into atomic values and preserve the shape of the indices.

If you index outside of an array's length, you will get a null value corresponding to the type of the array:

`67 78 89 @ 56` -> `0N`

The second form of application is called the M-expression, which may be familiar to people coming from the Lisp language. The M-expression is the most general form of application, and it assumes the form of `noun[args]`.

for a function call, you can do `f[a;b;c]`. Remember that function calls include semicolons.

this means that `+[1;2]` is the same as `1+2`, and you can effectively translate most K programs to use M-expressions only.

For an array, each argument indexes into a different dimension:
```
 :a:3 3#1 2 3 4 5 6 7 8 9
(1 2 3
 4 5 6
 7 8 9)
```
The `:` at the beginning of the line is used to force a return value, so `a` gets printed. Now, with the help of M-expressions, you can do `a[1;2]` to get 6, and `a[0;0]` to get 1.

You can also omit a dimension in reshape using `0N` (a null value), like so:
```
 3 0N#1 2 3 4 5 6 7 8 9
(1 2 3
 4 5 6
 7 8 9)
```
This will automatically split the array to fit in the given dimension constraint.

Like with `@`, you can also use arrays to index into any dimension: `a[0 1;2]` will give you `2 5`. Indexing always covers a rectangular area regardless of number of dimensions.

The final form of application is dot (`.`).



### `f.x` Dot

**Symbol:** `.`

**Args:** `noun . array`

**Description:** Applies a noun `f` to the arguments given in array `x`.



Dot application takes a noun on the left, and an array on the right. Each array element is taken as an argument.

`(+) . 1 2` is the same as `1 + 2`. Since `+` is a primitive, you must put it in parentheses to make it a noun before giving it to `.`.

**Note**: This behaviour where parenthesizing a verb creates a noun is called *nominalization*. It is mainly useful when a verb takes a function as an argument.

from the previous example, `a[1;2]` can be rewritten as `a . 1 2`, and `a[0;0]` is `a . 0 0`.

## Projections

K lets you omit arguments in application. 

For functions, omitting arguments implies *partial application*:

`-[1;]` is a function that does `1-x` given an `x`.

`-[;1]` is a function that does `x-1` given an x. Basically, Projections will fill in values that you have left empty. 

Projections are often used to pass in outside data to a function. This is a common method of omitting global variables, and passing data to inner blocks in K.

For example, this snippet should return a function that adds `a` to its argument.

```
 f:{a:x;{a+x}}[1]
 a[1]
'value
 a[1]
```

here, `{a+x}` can't see the value of a, since `a` is outside of its block, and `a` is not global.

To fix this, we should instead pass `a` via projection:

```
 f:{a:x;{x+y}[a]}[1]
 f[3]
4
```

as compared to assigning to `a` globally (`f:{a::x;{a+y}}`), this keeps data within the function, and makes the function side-effect free. This is not only useful in K, but helps for any programming task in general.


Now, if you remember from the beginning of this chapter, functions, arrays and dictionaries are all treated the same way in K. So projections can *also* apply to arrays and dictionaries!

For an array, a projection will take all elements from the nth dimension of an array:

```
 3 3#!9
(0 1 2
 3 4 5
 6 7 8)
 a:3 3#!9
 a[1;]
3 4 5
 a[;1]
1 4 7
```

For dictonaries, it will index into each nested dictionary within, and pull out the respective values:
```
 (1 2 3!(3#,1 2 3!4 5 6))[;2]
1 2 3!5 5 5
```

## Vocabulary from this lesson
 - Array: A list of elements mapped by indices.
 - Dictionary: A list of elements mapped by K values.
 - Function: A range of elements mapped by a K expression.
 - Application: Getting a corresponding value from a given noun. Can be indexing or calling a function.
 - Projection: A partially applied value, obtained by incomplete indexing.

## Exercises
- Get the third column of the array `4 4#!16` using a projection. Then do it without a projection.
- Create a function that returns another function which always returns 1.
- Write a function that sums each row of a 2 dimensional array.
