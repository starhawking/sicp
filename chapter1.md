# Chapter 1

Special forms are things that don't follow the general evaluation rules. The example given is `define` which isn't a combination as outlined in the chapter. It has different rules about how the arguments get evaluated

I didn't know Peter Landin is who came up with the term syntactic sugar. I wanna look up more about who that is later. Alan Perlis was also mentioned in this footnoot.

Is the distinction of "compound procedures" vs primitive procedures going to become important later?

It calls out the term formal parameters. I've generally used the term parameters and arguments to a function interchangably, but I guess that's a bit fast and loose.

'( define (<name> <formal parameters>) (<body>))'


I didn't expect to feel mild whiplash from seeing `-` in variable names.

Look up "Story 1977" as well for more about the substitution model

I almost missed an interesting footnote about Normal order vs Applicative order! Reading about the evaluation modes brought up some memories about reading about doing Hadoop. Then, in footnote 16, there's a comment about how Normal Order can be useful for stream processing. 

## Evaluation Order

```
(define (square x) (* x x))
(define (sum-of-squares x y)
	(+ (square x) (square y)))
(define (f a)
	(sum-of-squares (+ a 1) (* a 2)))
```

### Substitution Model (Normal Order) - Example is Wrong!!!
I stepped through this, but it turns out it isn't actually representative of the substitution model! This evaluates things in the midst of substituting them!
This seems to actually be an example of applicative-order!

```
(f 8)
```

```
(define a 8)
(sum-of-squares (+ a 1) (* a 2))
```

Consume `a`
```
(sum-of-sqares (+ 8 1) (* 8 2))
```

```
(sum-of-squares 9 16)
```

Evaluate `sum-of-squares`
```
(define x 9)
(define y 16)
(+ (square x) (square y))
```

Consume variables
```
(+ (square 9) (square 16))
```

Expand `square` compound procedure
```
(+ (* 9 9) (* 16 16))
```

Evaluate the `*` primitive procedures that can now be evaluated
```
(+ 81 256)
```

Evaluate the `+` primitive procedure
```
337
```
Again, this is not the substitution method!!!
I guess I accidentally did applicative-order evaluation instead!





# 1.1.6
"Case Analysis" is a new term to me for conditionals and case statements.


`if` is another special form

predicate, consequent, and alternative. 

Consequent is so much more concise than saying something like "the thing to do if this is true"!

Hmmm, I assume that means `cond` is also a special form too, but I'm not seeing it explicitly mentioned. I don't think there would be a way to _not_ evaluate stuff if it wasn't a special form, but special forms have generally been called out.
Err, never mind, the previous page mentioned `cond` is a special form and I just missed it

More special forms! `and` and `or` are special forms, but `not`` is a regular procedure.

This footnote mentions that each consequent for a cond can be a sequence of expressions, but the concequent for an if statement can only be a single statement. I'm curious to see how this plays out practically. Most languages I've dealt with, it felt a lot more natural to have an bulkier concequent for an if statement. The similar styled structure of switch statements always felt more natural to have smaller concequents. 

Just checking to make sure that it only actually evaluates the first consequent
```
(cond 
	(#t (display "first\n"))
	((< 1 2) (display "second\n"))
)
```

```
scheme@(guile-user) [6]> (cond 
	(#t (display "first\n"))
	((< 1 2) (display "second\n"))
)
first
scheme@(guile-user) [6]>
```



# Exercises
> Define a procedure that takes three numbers as arguments and returns the sum of the squares of the two larger integers

I don't think I've ever thought how I'd solve this without some sort of looping mechanism or a sorting mechanism. My initial thought was to manually come up with the various conditions for a giant `cond` block, but I suppose I can still manually sort the sequence and take the 2 largest elements. Well, except I don't think I've been introduced to how to actually access elements of a list. 

Ok, I guess I at least know about `cadr` and `car` so lets see if that behaves like I expect it to.

```
scheme@(guile-user) [7]> '(1 2 3)
$17 = (1 2 3)
scheme@(guile-user) [7]> (cadr '(1 2 3))
$18 = 2
scheme@(guile-user) [7]> (car '(1 2 3))
$19 = 1
```
Not quite! I expected `(cadr '(1 2 3)` to give me `(2 3)` instead of just 2

I keep thinking there's something I can do without resorting to sorting this or explicitly doing every case, but I don't think I can. It feels like I should be able to have a function that accepts 2 numbers  and a function to do something with the results of that, but it feels like I would still loose something if the values I needed were the first two and I didn't have a way to get back to the third value.

```
(define (display-2 l r)
	(display l)
	(display "\n")
	(display r)
	(display "\n")
)
(define (apply-to-largest func x y z)
	(cond
		; the first two are the largest
		((and (>= x z) (>= y z))
			(display "first two are biggest\n")
			(func x y)
			(display "end first\n")
		)

		; the second pair is the largest
		((and (>= y x) (>= z x))
			(display "second pair are largest\n")
			(func y z)
		)

		; otherwise the first and last are the largest
		(else (
			(display "else clause\n")
			(func x z)
		))
	)
	(display "end\n")
)
(display "pasted\n")
(apply-to-largest display-2 3 2 1)

(define (square x) (* x x))
(define (sum-of-squares x y) (+ (square x) (square y)))
(define (display-sum-of-squares x y) (display (sum-of-squares x y)) (display "\n"))
(apply-to-largest sum-of-squares 3 2 1)
(apply-to-largest display-sum-of-squares 3 2 1)
```

```
(define (why-not l r) 
	(display l)
	(display r)
	(display "\n")
)

;; It turns out I had too many layers of parens. This doesn't work
(define (too-deep-bad x)
	(cond 
		((<= x 1) (
			(display "cond 1\n")
			(display "next line\n")
		))
		(else (display "else\n"))))
;; while this does
(define (too-deep x)
	(cond 
		((<= x 1) 
			(display "cond 1\n")
			(display "next line\n")
		)
		(else (display "else\n"))))
	
```



# 1.2.1
Tail Recursion Mentioned!

## Exercise 1.9

Which is iterative and which is recursive?
```
(define (+ a b)
  (if (= a 0)
      b
	  (inc (+ (dec a) b))))
```
I think this is the recursive one because we have to do additional work with the results once we've evaluated the primitive procedures. It looks like a chain of deferred operations to me


```
(define (+ a b)
  (if (= a 0)
      b
	  (+ (dec a) (inc b))))
```
I think this second function is the iterative one. Once we've done the first round of substitutions and evaluations, we don't need to continue any previous computations. Everything is immediately back in terms of the compound procedure `+`

## Exercise 1.10

What's the Ackerman function do?

```
(define (A x y)
  (cond ((= y 0) 0)
        ((= x 0) (* 2 y))
		((= y 1) 2)
		(else (A (- x 1)
		         (A x (- y 1))))))
```

```
scheme@(guile-user) [4]> (A 1 10)
$5 = 1024
scheme@(guile-user) [4]> (A 2 4)
$6 = 65536
scheme@(guile-user) [4]> (A 3 3)
$7 = 65536
scheme@(guile-user) [4]> 
```

Apparently (A 4 3) isn't exactly quick to calculate! My machine has been sitting here spinning for over 15 mins with one of the cores at 100% usage!


`(define (f n) (A 0 n))` This seems like `n^0` but actually testing it out seems to be `2 * n`

`(define (g n) (A 1 n))` This seems like `n^1` but actually testing it out seems to be `2^n`

`(define (h n) (A 2 n))` I guess I spoiled it for myself by looking at the Wikipedia page and seeing this is repeated exponeniation. 
