# Midterm Exam Preparation

The problems below are similar to what you can expect for the midterm exam.


## Regular Expressions and Grammars

1. Write a regular expression that denotes the set of all strings of
   two lower-case letters that are not the same.

   *Hint:* You can "brute-force" this one, but there are 650 pairs of distinct letters. A better
   idea is to do some grouping. For example, the relatively short expression

   `(a | ... | m) (n | ... | z)`

   covers 169 of the 650 pairs.

   <details><summary>Solution</summary>
     <p>
     
     `
     (a | ... | m) (n | ... | z) | (n | ... | z) (a | ... | m) |
     (a | ... | f) (g | ... | m) | (g | ... | m) (a | ... | f) |
     (a | b | c) (d | e | f) | (d | e | f) (a | b | c) |
     (a | b) c | c (a | b) | ab | ba |
     (d | e) f | f (d | e) | de | ed |
     (g | h | i) (j | ... | m) | (j | ... | m) (g | h | i) |
     (g | h) i | i (g | h) | gh | hg |
     (j | k) (l | m) | (l | m) (j | k) | jk | kj | lm | ml |
     (n | ... | s) (t | ... | z) | (t | ... | z) (n | ... | s) |
     (n | o | p) (q | r | s) | (q | r | s) (n | o | p) |
     (n | o) p | p (n | o) | no | on |
     (q | r) s | s (q | r) | qr | rq |
     (t | u | v) (w | ... | z) | (w | ... | z) (t | u | v) |
     (t | u) v | v (t | u) | tu | ut |
     (w | x) (y | z) | (y | z) (w | x) | wx | xw | yz | zy
     `
     </p></details>

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

   <details><summary>Solution</summary>
     <p>
     
     ```scala
     3 * (4 - (7 + 2 - 1)) / 2 ** 3 ** 4
     ```
     </p></details>

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

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     6
     ```
     </p></details>

1. Under dynamic scoping, what value does `g()` on line 14 return?

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     6
     ```
     </p></details>

1. Under static scoping, what value does `h()` on line 15 return? 

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     6
     ```
     </p></details>

1. Under dynamic scoping, what value does `h()` on line 15 return? 

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     9
     ```
     </p></details>

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

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     3 2
     ```
     </p>
   </details>


2. `x` is call-by-reference and `y` is call-by-value

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     3 2
     ```
     </p>
   </details>


3. `x` is call-by-value and `y` is call-by-name

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     4 3
     ```
     </p>
   </details>

4. `x` is call-by-reference and `y` is call-by-name

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     4 3
     ```
     </p>
   </details>

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

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     0 1
     ```
     </p>
   </details>

1. assuming dynamic scoping and shallow binding

   <details><summary>Solution</summary>
     <p>
     
     ``` 
     1 2
     ```
     </p>
   </details>

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

<details><summary>Solution</summary>
<p>
     
The problem is that the function `merge` returns a pointer to an array
`b` that is allocated on the stack within the activation record of
`merge`. Hence, when `merge` returns, this pointer will be dangling.
When `merge_sort` executes the `for` loop after `merge_sort` returns
and dereferences the returned pointer `b`, then this will have
undefined behavior. In particular, the code may crash with a
segmentation fault. However, the error may go undetected because the
old contents of the array `b` from the call to `merge` may still
reside in memory and so the `for` loop may actually copy the correct
values back into the array `a`.

The problem can be solved by moving the `for` loop from `merge_sort`
to the end of `merge`. In this case, `merge` does not need
a return value and its return type can be changed to `void`.

</p>
</details>


   
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
   
   <details><summary>Solution</summary>
   <p>
     
   ```ocaml
   (* Tail-recursive solution (preferable in this case) *)
   let rec find_nth n = function
   | [] -> None
   | x :: xs -> if n = 1 then Some x else find_nth (n - 1) xs
     
   (* Solution with List.fold_left (slightly convoluted) *)
   let find_nth n xs =
     let helper (n, r) x = function
       if n = 1 then (0, Some x) else (n - 1, r)
     in
     let _, r = List.fold_left helper (n, None) xs in
     r
   ```
   </p>
   </details>

   
   
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
   
   <details><summary>Solution</summary>
   <p>
     
   ```ocaml
   (* Tail-recursive solution *)
   let pack xs =
     let rec pack_helper xs curr_pack packed_xs = 
       match xs, curr_pack with
       | x :: xs1, y :: _ -> 
         if x = y 
         then pack_helper xs1 (y :: curr_pack) packed_xs
         else pack_helper xs1 [x] (curr_pack :: packed_xs)
       | x :: xs1, [] -> 
         pack_helper xs1 [x] packed_xs
       | [], _ -> List.rev (curr_pack :: packed_xs)
     in
     pack_helper xs [] []
       
   ; Solution with fold_left
   let pack xs =
     let helper res x = match res with
     | (y :: _ as curr_pack) :: packed_xs -> 
       if x = y 
       then (x :: curr_pack) :: packed_xs
       else [x] :: curr_pack :: packed_xs
     | _ -> [[x]]
     in
     xs |>
     List.fold_left helper [] |>
     List.rev
   ```
   </p>
   </details>
   
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
   
   <details><summary>Solution</summary>
   <p>
     
   ```ocaml
   let encode xs = 
     List.map (fun pack -> (List.hd pack, List.len pack)) (pack xs)
   ```
   </p>
   </details>
   
   
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
      
   <details><summary>Solution</summary>
   <p>
      
   ```ocaml
   
   let rec max_opt = function
   | Leaf -> None
   | Node (x, l, Leaf) -> Some x
   | Node (x, l, r) -> max_opt r
   ```
   </p>
   </details>
      
      
1. Write a function
    
   ```ocaml
   split: int -> tree -> tree * bool * tree
   ```
       
   such that `split x t` returns `(ts, present, tg)` where `ts` is
   a tree that contains the set of all values in `t` that are
   strictly smaller than `x`, `tg` is a tree that contains all
   values in `t` that are strictly greater than `x`, and `present`
   is `true` iff `x` is contained in `t`.
       
   <details><summary>Solution</summary>
   <p>
      
   ```ocaml
   let rec split x = function
   | Leaf -> (Leaf, false, Leaf)
   | Node (y, l, r) ->
     if x < y then 
       let ls, present, lg = split x l in
       (ls, present, Node (y, lg, r))
     else if x > y then
       let rs, present, rg = split x r in
       (Node (y, l, rs), present, lg)
     else (l, true, r)
   ```
   </p>
   </details>
