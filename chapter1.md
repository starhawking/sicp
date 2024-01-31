# Chapter 1

Special forms are things that don't follow the general evaluation rules. The example given is `define` which isn't a combination as outlined in the chapter. It has different rules about how the arguments get evaluated

I didn't know Peter Landin is who came up with the term syntactic sugar. I wanna look up more about who that is later. Alan Perlis was also mentioned in this footnoot.

Is the distinction of "compound procedures" vs primitive procedures going to become important later?

It calls out the term formal parameters. I've generally used the term parameters and arguments to a function interchangably, but I guess that's a bit fast and loose.

'( define (<name> <formal parameters>) (<body>))'


I didn't expect to feel mild whiplash from seeing `-` in variable names. 

```
(define (square x) (* x x))
(define (sum-of-squares x y)
	(+ (square x) (square y)))
(define (f a)
	(sum-of-squares (+ a 1) (* a 2)))
```


