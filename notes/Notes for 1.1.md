**1.1.3 Evaluating combinations**

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

**1.1.4 Compound Procedures**

A much more powerful way to use the define operator is to use it to define a procedure.

To name the procedure that squares a number, we do

    (define (square x) (* x x)). 
    
In general, we do

    (define (<name> <formal parameters>)) <body>)

There are two kinds of parameters. Formal parameters are contained in a function definition, and actual parameters are the arguments passed to a function. Calling a function temporarily binds a formal parameter to an actual parameter

**1.1.5  The Substitution Model for Procedure Application**

A procedure which is defined using other procedures is called a compound procedure. It can be defined using primitive or compound procedures.

A primitive procedure is a built-in procedure.

Evaluating compound procedures is done in the same way as evaluating primitive procedures:

*To apply a compound procedure to arguments, evaluate the body of the procedure with each formal parameter replaced by the corrosponding arguments (or each formal parameter bound to the corrosponding actual parameter).*

Notice that this rule (or procedure) for evaluating procedures ask us to evaluate the operands of a combination before evaluating the operator, because it asks us to evaluate the body of the procedure using the arguments (value of operands). This is somewhat different than the "percolate upwards" algorithm described above.

Note that this is just a model that is meant to give meaning to the concept of procedure application. It does not actually describe how the interpreter carries out procedures.

*normal-order evaluation:* substitute operand expressions for formal parameters when applying procedures (i.e. evaluate operators before operands).

*applicative-order evaluation:* substitute arguments (operand values) for formal parameters when applying procedures (i.e. evaluate operands before operators). Used in the substitution model as well as by the interpreter.

For procedures that can be modeled using the substitution model, these two evaluation orders will produce the same value.

Note that if a formal parameter appears multiple times in an operator body, it will have to be evaluated for each time it appears if we are using normal-order evalutation. This is one reason to prefer application-order evaluation.

**1.1.6 Conditional Expressions and Predicates**

Lisp supports constructs for conditional expressions, also known as case analysis. One such construct is the `cond` operator. The general form is 

    (cond (<p1> <e1>)
          (<p2> <e2>)
          (<p3> <e3>))

and so on. 

`(<p> <e>)` is called a clause
`<p>` is called a predicate, and it's value is interpretted as true of false
`<e>` is a consequent expression

`cond` supports a fall-through operator, `else`, used as follows

    (cond (<p1> <e1>)
          (<p2> <e2>)
          (<p3> <e3>)
          (else <e4>))

Another operator for case analysis is the special form `if`, used as follows

    (if <predicate> <consequent> <alternative>)

Note that `if` cannot be modeled using the substitution model, because it only evaluates one of `<consequent>` and `<alternative>`.

We also have logical operaters `and`, `or` and `not` used like

    (and <e1> <e2> <e3>)

    (or <e1> <e2> <e3>)

    (not <e>)

Note that we make the distinction between special forms and procedures. Both are a kind of operator, but special forms cannot be modeled with any of the procedure models that we will come up with. `and` and `or` are special forms rather than procedures, whereas `not` is a procedure.

**1.1.7 Example: Square Roots by Newton's Method**

    (define (sqrt-iter guess x)
      (if (good-enough? guess x)
          guess
          (sqrt-iter (improve guess x)
                     x)))

    (define (improve guess x)
      (average guess (/ x guess)))

    (define (average x y)
      (/ (+ x y) 2))

    (define (good-enough? guess x)
      (< (abs (- (square guess) x)) 0.001))

    (define (sqrt x)
      (sqrt-iter 1.0 x))
  
**1.1.8  Procedures as Black-Box Abstractions**

*Free and Bound Variables:* A procedure definition will bind a free variable. Bound varibles are exactly analogous to dummy variables.

*Lexical Scoping:*
AKA static scoping, as opposed to dynamic scoping. 
In SICP, lexical scoping is when a variable is bound by a procedure definition, but within nested procedure definitions, it becomes a free variable whose value is given by the outer procedure argument.
In a lexically scoped language, the scope of an identifier is fixed at compile time to some region in the source code containing the identifier's declaration. This means that an identifier is only accessible within that region (including procedures declared within it). 

This contrasts with dynamic scope where the scope depends on the nesting of procedure and function calls at run time.
Statically scoped languages differ as to whether the scope is limited to the smallest block (including begin/end blocks) containing the identifier's declaration (e.g. C, Perl) or to whole function and procedure bodies, or some larger unit of code.
Also note that in Lisp, a symbol's binding has lexical scope, not the mapping from an identifier to a symbol. That mapping transcends scope. For instance in `(let ((x 3)) (let ((x 4)) ..))` the identifier X in the source is mapped to a symbol object when the source is scanned and turned into a nested list, at what is known as "read time". Both occurrences of X map to the same symbol object, even though each one denotes a local variable in a different lexical scope. A symbol object is a concrete data structure in the address space; every X in the internal representation of the form is a pointer to that object.

*Block Structure:*
Refers to the nesting of definitions within other defintions, so that inner defintions are only accessible from within the outer defintion. Embedded definitions must come first in a procedure body.
