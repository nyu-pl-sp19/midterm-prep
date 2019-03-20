# Midterm Exam Preparation

The problems below are similar to what you can expect for the midterm exam.


## Regular Expressions and Grammars

1. Write a regular expression that denotes the set of all strings of
   two lower-case letters that are not the same.

   *Hint:* You can "brute-force" this one, but there are 650 pairs of distinct letters. A better
   idea is to do some grouping. For example, the relatively short expression

   `(a | ... | m) (n | ... | z)`

   covers 169 of the 650 pairs.

1. Consider the following context-free grammar for describing an
   expression language built from the binary infix operators `-`, `+`,
   `*`, `/`, and `**`:
 
   ```scala
   E ::= E O E | (E) | N
   O ::= + | - | * | / | **
   N ::= 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 
   ```

   Remove all redundant parenthesis from the following expression,
   making the following assumptions:
   
   1. `**` has precedence 1
   1. `*`, `/` have precedence 2
   1. `+`,`-` have precedence 3
   1. `**` is right-associative, and
   1. all other operators are left-associative.

   ```scala
   ((3 * (4 - ((7 + 2) - 1))) / (2 ** (3 ** 4)))
   ```

## Static vs. Dynamic Scoping

Consider the following program:

```scala
 1: var b = 2

 2: def f(): Unit = {
 3:   var b = 3
 4:   ()
 5: }

 6: def g(): Int = {
 7:   f()
 8:   b + 4
 9: }

10: def h(): Int = {
11:   var b = 5
12:   g()
13: }

14: g()
15: h()

```

1. Under static scoping, what value does `g()` on line 14 return?

1. Under dynamic scoping, what value does `g()` on line 14 return?

1. Under static scoping, what value does `h()` on line 15 return? 

1. Under dynamic scoping, what value does `h()` on line 15 return? 

## Parameter Passing Modes

Consider the following program:

```scala
def f(x: Int, y: Int) {
  x = y + 1
  println(x + y)
}

var z = 1
f(z, {var x = z; z = x + 1; x})
println(z)
```

What does this program print if we make the following assumptions about
the parameter passing modes for the parameters `x` and `y` of
`f`:

1. both `x` and `y` are call-by-value parameters

2. `x` is call-by-reference and `y` is call-by-value

3. `x` is call-by-value and `y` is call-by-name

4. `x` is call-by-reference and `y` is call-by-name

## Deep vs. Shallow Binding

```scala
def f(x: Int, h: () => Unit): () => Unit = {
  def g(): Unit = println(x)
  
  if (x == 0) f(1, g)
  else {
    h()
    g
  }
}

var x = 2

def h(): Unit = ()

f(0, h)()
```

What does this program print:

1. assuming static scoping and deep binding

1. assuming dynamic scoping and shallow binding

## Call Stacks and Memory Management

Consider the following (faulty) implementation of a merge sort
function in C. The function `merge_sort` takes a pointer `a` to the
first element of an array of integers and the length of the array `a`
as input, and is supposed to sort the array in-place in ascending
order. A few test runs of the compiled code on a test machine produce
correct results, which seems to indicate that the implementation is
correct. However, there is a problem in the code that may cause
`merge_sort` to produce unexpected results or even crash. What is this
problem? Explain why some simple tests may not reveal this
issue. Can you suggest a fix that solves it?

Hint: you don't really need to understand merge sort to spot the
problem in the code.

```c
int* merge(int* a, int mid, int length) {
  int b[length];
  int i = 0;
  int j = 0;
  int k = mid;

  while (i < length) {
    if (k >= length || j < mid && a[j] <= a[k]) {
      b[i++] = a[j++];
    } else {
      b[i++] = a[k++]; 
    }
  }
  
  return b;
}

void merge_sort(int* a, int length) {
  int mid = length / 2;

  if (mid == 0) return;

  merge_sort(a, mid);
  merge_sort(a + mid, length - mid);

  int* b = merge(a, mid, length); 

  for (int i = 0; i < length; i++) a[i] = b[i];
}
```


   
## OCaml Programming with Lists

1. Write an OCaml function 

   ```ocaml
   find_nth: int -> 'a list -> 'a option
   ``` 
   
   that returns the `n`-th element of a list if it exists. Examples:
   
   ```ocaml
   # find_nth 1 ["a"; "b"; "c"; "d"] ;;
   - : string option = Some "a"
   # find_nth 2 ["a"; "b"; "c"; "d"] ;;
   - : string option = Some "b"
   # find_nth 4 ["a"; "b"; "c"; "d"] ;;
   - : string option = Some "d"
   # find_nth 5 ["a"; "b"; "c"; "d"] ;;
   - : string option = None
   ```
   
   
   
1. Write an OCaml function 

   ```ocaml
   pack: 'a list -> 'a list list
   ```
   
   that packs consecutive elements of a list into sublists:
   
   ```ocaml
   # pack [1; 1; 1; 2; 3; 3; 3; 3; 4; 4] ;;
   - : int list list = [[1; 1; 1]; [2]; [3; 3; 3; 3]; [4; 4]]
   > pack [] ;;
   # - : 'a list list
   ```
      
1. Write an OCaml function 

   ```ocaml
   encode: 'a list -> ('a * int) list
   ```
   
   that computes the length encoding of a list (you can use the `pack`
   function as a subroutine).
   
   ```ocaml
   # encode ["a"; "a"; "a"; "b"; "c"; "c"; "c"; "c"; "d"; "d"] ;;
   -: (string * int) list = [("a", 3); ("b"; 1); ("c", 4); ("d"; 2)]
   ```   
   
Challenge yourself and try to implement these functions as efficiently
as possible both with respect to run-time/space complexity as well as
code complexity (e.g. use tail-recursion, use implementations based on
`fold_left`/`fold_right` etc.)

## OCaml Programming with ADTs

Consider the following ADT definition for binary search trees

 ```ocaml
type tree = 
  | Leaf
  | Node of int * tree * tree
```
   
In the following, we assume that we are operating on trees that are
strictly sorted in increasing order of values stored in the tree. This
property should be maintained by all the considered operations.
   
1. Write a function
   
   ```ocaml
   max_opt: tree -> int option
   ```
      
   that returns the maximal element of a tree if it exists.      
      
1. Write a function
    
   ```ocaml
   split: int -> tree -> tree * bool * tree
   ```
       
   such that `split x t` returns `(ts, present, tg)` where `ts` is
   a tree that contains the set of all values in `t` that are
   strictly smaller than `x`, `tg` is a tree that contains all
   values in `t` that are strictly greater than `x`, and `present`
   is `true` iff `x` is contained in `t`.
