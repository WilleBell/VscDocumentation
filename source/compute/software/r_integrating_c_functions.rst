.. _R integrating C functions:

R integrating C functions
=========================

Purpose
-------

Although R is a nice and fairly complete software package for
statistical analysis, there are nevertheless situations where it
desirable to extend R. This may be either to add functionality that is
implemented in some C library, or to eliminate performance bottlenecks
in R code. In this how-to it is assumed that the users wants to call his
own C functions from R.

Prerequisites
-------------

It is assumed that the reader is familiar with the use of R as well as R
scripting, and is a reasonably proficient C programmer. Specifically the
reader should be familiar with the use of pointers in C.

Integration step by step
------------------------

Before all else, first load the appropriate R module to prepare your
environment, e.g.,

::

   $ module load R

If you want a specific version of R, you can first check which versions
are available using

::

   $ module av R

and then load the appropriate version of the module, e.g.,

::

   $ module load R/3.1.1-intel-2014b

A first example
~~~~~~~~~~~~~~~

No tutorial is complete without the mandatory 'hello world' example. The
C code in file 'myRLib.c' is shown below:

::

   #include <R.h>
   void sayHello(int *n) {
       int i;
       for (i = 0; i < *n; i++)
           Rprintf(\"hello world!\\n\");
   }

Three things should be noted at this point

#. the 'R.h' header file has to be included, this file is part of the R
   distribution, and R knows where to find it;
#. function parameters are *always* pointers; and
#. to print to the R console, 'Rprintf' rather than 'printf' should be
   used.

From this 'myRLib.c' file a shared library can be build in one
convenient step:

::

   $ R CMD SHLIB myRlib.c

If all goes well, i.e., if the source code has no syntax errors and all
functions have been defined, this command will produce a shared library
called 'myRLib.so'.

To use this function from within R in a convenient way, a simple R
wrapper can be defined in 'myRLib.R':

::

   dyn.load(\"myRLib.so\");
   sayHello <- function(n) {
       .C(\"sayHello\", as.integer(n))
   }

In this script, the first line loads the share library containing the
'sayHello' function. The second line defines a convenient wrapper to
simplify calling the C function from R. The C function is called using
the '.C' function. The latter's first parameter is the name of the C
function to be called, i.e., 'sayHello', all other parameters will be
passed to the C function, i.e., the number of times that 'sayHello' will
say hello as an integer.

Now, R can be started to be used interactively as usual, i.e.,

::

   $ R

In R, we first source the library's definitions in 'myRLib.R', so that
the wrapper functions can be used:

::

   > source(\"myRLib.R\")
   > sayHello(2)
   hello world!
   hello world!
   [[1]]
   [1] 2

Note that the 'sayHello' function is not particularly interesting since
it does not return any value. The next example will illustrate how to
accomplish this.

A second, more engaging example
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Given R's pervasive use of vectors, a simple example of a function that
takes a vector of real numbers as input, and returns its components' sum
as output is shown next.

::

   #include <R.h>

   /* sayHello part not shown */

   void mySum(double *a, int* n, double *s) {
       int i;
       *s = 0.0;
       for (i = 0; i < *n; i++)
           *s += a[i];
   }

Note that both 'a' and 's' are declared as pointers, the former being
used as the address of the first array element, the second as an address
to store a double value, i.e., the sum of array's compoments.

To produce the shared library, it is build using the R appropriate
command as before:

::

   $ R CMD SHLIB myRLib.c

The wrapper code for this function is slightly more interesting since it
will be programmed to provide a convenient \\"function-feel\".

::

   dyn.load("myRLib.so");

   # sayHello wrapper not shown

   mySum <- function(a) {
       n <- length(a);
       result <- .C("mySum", as.double(a), as.integer(n), s = double(1));
       result$s
   }

Note that the wrapper functions is now used to do some more work:

#. it preprocesses the input by calculating the length of the input
   vector;
#. it initializes 's', the parameter that will be used in the C function
   to store the result in; and
#. it captures the result from the call to the C function which contains
   all parameters passed to the function, in the last statement only
   extracting the actual result of the computation.

From R, 'mySum' can now easily be called:

::

   > source("myRLib.R")
   > mySum(c(1, 3, 8))
   [1] 12

Note that 'mySum' will probably not be faster than R's own 'sum'
function.

A last example
~~~~~~~~~~~~~~

Function can return vectors as well, so this last example illustrates
how to accomplish this. The library is extended to:

::

   #include <R.h>

   /* sayHello and my_sum not shown */

   void myMult(double *a, int *n, double *lambda, double *b) {
       int i;
       for (i = 0; i < *n; i++)
           b[i] = (*lambda)*a[i];
   }

The semantics of the function is simply to take a vector and a real
number as input, and return a vector of which each component is the
product of the corresponding component in the original vector with that
real number.

After building the shared libary as before, we can extend the wrapper
script for this new function as follows:

::

   dyn.load("myRLib.so");

   # sayHello and mySum wrapper not shown

   myMult <- function(a, lambda) {
       n <- length(a);
       result <- .C("myMult", as.double(a), as.integer(n),
                    as.double(lambda), m = double(n));
       result$m
   }

From within R, 'myMult' can be used as expected.

::

   > source("myRLib.R")
   > myMult(c(1, 3, 8), 9)
   [1]  9 27 72
   > mySum(myMult(c(1, 3, 8), 9))
   [1] 108

Further reading
---------------

Obviously, this text is just for the impatient. More `in-depth
documentation <http://cran.r-project.org/doc/manuals/R-exts.html>`_
can be found on the nearest CRAN site.
