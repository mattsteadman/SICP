1.2.1  Linear Recursion and Iteration

Linear Processes:

linear recursive process - Uses deferred operations. Information that the interpreter keeps track of grows linearly, just like the number of steps.

linear iterative process - Uses state variables. Information that the interpreter keeps track of is fixed.


An recursive process is NOT the same thing as a recursive procedure.
A recursive procedure is any procedure whose definition refers to itself.
A recursive process is a process which *evolves* recursively, no matter how it is written.

fact-iter is a iterative process which is implemented as a recursive procedure.

This recursive procedure/recursive process distinction is confusing because many common languages are implemented in such a way that an recursive procedure will consume memory that growly linearly with the number of procedure calls. Therefore, in these languages, iterative processes can only be described using looping consructs (for, etc). In Scheme, this is not a problem, because it is designed to be tail-recursive, that is it executes iterative processes in constant space even if they are written as recursive procedures.

1.2.2  Tree Recursion