#Chapter 1 Building Abstractions with Procedures#

Computational **Process** - This is refers to the evolution of the execution of a procedure.

**Program** or **Procedure** - A description of a process, or the rules that governs its evolution.

**Interpreter** - Carries out the evolution of a process, or the execution of a procedure.

**Procedures and Data** - Data is the *stuff* we want to manipulate, and procedures are descriptions of the rules for manipulating them. Even though procedures are implemented as data in lisp, they still might be different in some conceptions of computation.



##1.1 The Elements of Programming##

**Primitive Expressions** - The simplest entities that the language is concerned with. I believe that this is equivalent to **atomic symbols** in John McCathy's conception of Lisp.

Scheme provides us with **means of combination** and **means of abstraction** to build and name (respectively) **compound elements** from primitive expressions.

###1.1.1  Expressions###

**Expression** - Anything that can be evaluated by an interpreter, e.g., `486`, `(print 9)`, `print`.

**Primitive Procedure** - A procedure includes with the programming language. Primitive procedures are primitive expressions.

**Combination** - A list of expressions delimited by parentheses. Members of the combinations are called **elements**. The leftmost element of a combination is called the **operator**, the remaining elements are called **operands**. The value of an operand is called the **argument** of the associated procedure.

**Prefix Notation** - The convention of placing an operator to the left of all its operands.

**Compound Expression** - An expression that is a combination of other expressions.

**Nesting** - When a combination has one or more elements which are themselves combinations.

**Pretty-printing** - When we write combinations such that there is a linebreak between adjacent operands, and operands of the same combination align vertically.

A **name** identifies a **variable** whose **value** is some computational **object**.

`define` is the operator that we use to name things. It is the simplest means of abstraction provided by scheme.


###1.1.3 Evaluating combinations###

Elements of a combination are also called subexpressions. The two types of subexpressions are operators and operands.

To evaluate a combination, first evaluate all the subexpressions of the combinations (in principle it shouldn't matter what order these are evaluated in). Second, apply the value of the leftmost subexpression (the operator) to the values of the other subexpressions (the operands).

This rule (procedure) for evaluating combinations is recursive, which means that in order to evaluate the rule, you invoke the rule itself. Recursive procedures allow us to economically express a deeply nested procedure in very few steps.

Recursion is often a very useful way to deal with tree-like objects. Combinations are one example of such objects. The technique used to deal with combinations is a "percolate values upward" technique called tree accumulation.

Notice that we need rules for dealing with the most primitive objects in a combination. We do this using the axioms:
1. The value of a numeral is the number it names
2. The value of a built-in operator is the machine instruction that it names
3. The values of other names in the environment are the objects they name

but the second rule is really just a special case of the third when we say that built-in operators are included in what's called the global environment.

Notice that it is meaningless to speak of the value of a name without specifying which environment that we are working in.

One combination that does NOT get evaluated using the above rule is a combination whose operator is the `define` operator. `define` is an operator which applies a procedure to one element which has NO value in the environment (the first operand) and one element that does (the second operator). Exceptions to the evaluation rule are called special forms. Scheme has a small number of special forms compared to other lisps.

###1.1.4 Compound Procedures###

A much more powerful way to use the define operator is to use it to define a procedure.

To name the procedure that squares a number, we do

```scheme
(define (square x) (* x x))
```

In general, we do

```scheme
(define (<name> <formal parameters>) <body>)
```

There are two kinds of parameters. Formal parameters are contained in a function definition, and actual parameters are the arguments passed to a function. Calling a function temporarily binds a formal parameter to an actual parameter

###1.1.5  The Substitution Model for Procedure Application###

A procedure which is defined using other procedures is called a compound procedure. It can be defined using primitive or compound procedures.

A primitive procedure is a built-in procedure.

Evaluating compound procedures is done in the same way as evaluating primitive procedures:

*To apply a compound procedure to arguments, evaluate the body of the procedure with each formal parameter replaced by the corresponding arguments (or each formal parameter bound to the corresponding actual parameter).*

Notice that this rule (or procedure) for evaluating procedures ask us to evaluate the operands of a combination before evaluating the operator, because it asks us to evaluate the body of the procedure using the arguments (value of operands). This is somewhat different than the "percolate upwards" algorithm described above.

Note that this is just a model that is meant to give meaning to the concept of procedure application. It does not actually describe how the interpreter carries out procedures.

**normal-order evaluation:** substitute operand expressions for formal parameters when applying procedures (i.e. evaluate operators before operands).

**applicative-order evaluation:** substitute arguments (operand values) for formal parameters when applying procedures (i.e. evaluate operands before operators). Used in the substitution model as well as by the interpreter.

For procedures that can be modeled using the substitution model, these two evaluation orders will produce the same value.

Note that if a formal parameter appears multiple times in an operator body, it will have to be evaluated for each time it appears if we are using normal-order evaluation. This is one reason to prefer applicative-order evaluation.

###1.1.6 Conditional Expressions and Predicates###

Lisp supports constructs for conditional expressions, also known as case analysis. One such construct is the `cond` operator. The general form is 

```scheme
(cond (<p1> <e1>)
      (<p2> <e2>)
      (<p3> <e3>))
```

and so on. 

`(<p> <e>)` is called a clause
`<p>` is called a predicate, and its value is interpreted as true of false
`<e>` is a consequent expression

`cond` supports a fall-through operator, `else`, used as follows

```scheme
(cond (<p1> <e1>)
      (<p2> <e2>)
      (<p3> <e3>)
      (else <e4>))
```

Another operator for case analysis is the special form `if`, used as follows

```scheme
(if <predicate> <consequent> <alternative>)
```

Note that `if` cannot be modeled using the substitution model, because it only evaluates one of `<consequent>` and `<alternative>`.

We also have logical operators `and`, `or` and `not` used like

```scheme
(and <e1> <e2> <e3>)

(or <e1> <e2> <e3>)

(not <e>)
```

Note that we make the distinction between special forms and procedures. Both are a kind of operator, but special forms cannot be modeled with any of the procedure models that we will come up with. `and` and `or` are special forms rather than procedures, whereas `not` is a procedure.

###1.1.7 Example: Square Roots by Newton's Method###

```scheme
(define (average x y)
        (/ (+ x y) 2))
      
(define (improve guess x)
        (average guess (/ x guess)))

(define (good-enough? guess x)
        (< (abs (- (square guess) x)) 0.001))
      
(define (sqrt-iter guess x)
        (if (good-enough? guess x)
            guess
            (sqrt-iter (improve guess x)
                       x)))
(define (sqrt x)
        (sqrt-iter 1.0 x))
```
  
###1.1.8  Procedures as Black-Box Abstractions###

**Free and Bound Variables:** A procedure definition will bind a free variable. Bound variables are exactly analogous to dummy variables.

**Lexical Scoping:**
AKA static scoping, as opposed to dynamic scoping. 
In SICP, lexical scoping is when a variable is bound by a procedure definition, but within nested procedure definitions, it becomes a free variable whose value is given by the outer procedure argument.
In a lexically scoped language, the scope of an identifier is fixed at compile time to some region in the source code containing the identifier's declaration. This means that an identifier is only accessible within that region (including procedures declared within it). 

This contrasts with dynamic scope where the scope depends on the nesting of procedure and function calls at run time.
Statically scoped languages differ as to whether the scope is limited to the smallest block (including begin/end blocks) containing the identifier's declaration (e.g. C, Perl) or to whole function and procedure bodies, or some larger unit of code.
Also note that in Lisp, a symbol's binding has lexical scope, not the mapping from an identifier to a symbol. That mapping transcends scope. For instance in `(let ((x 3)) (let ((x 4)) ..))` the identifier `x` in the source is mapped to a symbol object when the source is scanned and turned into a nested list, at what is known as "read time". Both occurrences of `x` map to the same symbol object, even though each one denotes a local variable in a different lexical scope. A symbol object is a concrete data structure in the address space; every `x` in the internal representation of the form is a pointer to that object.

**Block Structure:**
Refers to the nesting of definitions within other definitions, so that inner definitions are only accessible from within the outer definition. Embedded definitions must come first in a procedure body.
