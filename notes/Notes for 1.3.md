##1.3  Formulating Abstractions with Higher-Order Procedures##

###1.3.1  Procedures as Arguments###

A procedure can be passed and returned to and from another procedure. The latter procedure is referred to as a **higher-order procedure**.

###1.3.2  Constructing Procedures Using Lambda###

The special form `lambda` is useful for defining procedures without needing to name them with `define`. `lambda` takes two combinations as arguments. The first combination contains the formal parameters to the procedure that it returns, and the second combination contains it's body.

```lisp
(lambda (<formal parameters>) (<procedure body>))
```

We can use `lambda` to assign local variables to expressions by doing the following:

1. Decide what variables (e.g. `v1`, `v2`) you want to bind.
2. Create a `lambda` procedure whose *formal parameters* are those variables and whose body uses those variables however you want (e.g. apply procedure `f`).
3. Pass the expressions (e.g. `e1`, `e2`) to which your variables should be bound as *actual parameters* of your `lambda` procedure.

```lisp
(lambda (v1 v2) 
        (f v1 v2)) 
(e1 e2)
```

This pattern is so useful that it has been given a special form, called `let`. The following `let` combination can be constructed as follows:

```lisp
(let ((v1 e1) 
      (v2 e2))
     (f v1 v2))
```


*Nota Bene: The first argument of `let`, as well as `lambda` does not fit the pattern that we expect from a combination, i.e. the combination is a list rather than an operator being applied to operands*.

###1.3.3  Procedures as General Methods###

###1.3.4  Procedures as Returned Values###

