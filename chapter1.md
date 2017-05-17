---
title       : Introduction
description : Some example exercises

--- type:NormalExercise lang:r xp:100 skills:1 key:e66525b8e1
## Using evalCpp()

`evalCpp()` can be used to evaluate very simple chunks of C++ code from R. Pass it a string of C++ code.

```{r}
evalCpp('1 + 1')
## [1] 2
```

The first time you run this code, there will be a delay of a few seconds while the code compiles. The second time, it should run instantaneously. Ideally, this caching works in Campus!

*** =instructions

- Call `library()` to load the Rcpp package.
- Call `evalCpp()` with the six times seven passed in a string.
- Run the last line again, to see if it runs quicker.

*** =hint

- Six times seven is written the same in C++ as in R.

*** =pre_exercise_code
```{r}

```

*** =sample_code
```{r}
# Load Rcpp package
___

# Evaluate some simple C++ code
___
```

*** =solution
```{r}
# Load Rcpp package
library(Rcpp)

# Evaluate some simple C++ code
evalCpp('6 * 7')
```

*** =sct
```{r}
test_library_function("Rcpp")
ex() %>% {
  check_function(., "evalCpp") %>% {
    check_arg(., "code") %>% 
      check_equal()
  }
}
```


--- type:NormalExercise lang:r xp:100 skills:1 key:d3837f8c96
## Using separate R and C++ files 

The typical way to write non-trivial Rcpp code is to have separate R (`.R`) and C++ (`.cpp`) files.  Here's an example C++ function taken from section 1.2.5 of [Seamless R and C++ Integration with Rcpp](https://www.springer.com/us/book/9781461468677).

```{cpp}
#include <Rcpp.h>
using namespace Rcpp;

// [[Rcpp::export]]
int fibonacci(const int x) {
  if(x < 2) {
    return x;
  }
  else {
    return(fibonacci(x - 1) + fibonacci(x - 2));
  }
}
```

Let's step through the code.

- `#include <Rcpp.h>` gives the C++ file access to the functionality described in the `Rcpp.h` header file. This includes C++ equivalents of R objects. For example `NumericVector` is the Rcpp equivalent of R's `numeric` vector.
- `using namespace Rcpp;` means that the code in this file can access the contents of the `Rcpp` namespace without explicitly mentioning it. For example, you can write `NumericVector` rather than `Rcpp::NumericVector`.
- `// [[Rcpp::export]]` tells Rcpp that the function following it needs to be exported. That is, an R function that calls this C++ function must be created when this file is sourced.
- `int fibonacci(const int x) { ... }` defines a C++ function that calculates Fibonacci numbers. 

*** =instructions

- Pass `"fibonacci.cpp"` to `sourceCpp()` to source the C++ code.
- Run the R-level fibonacci function that is generated with 10 as an input.

*** =hint

- This exercise doesn't work yet, because there is nowhere to write the C++ code.

*** =pre_exercise_code
```{r}
'___BLOCK_SOLUTION_EXEC___'
library(Rcpp)
```

*** =sample_code
```{r}
# Source the cpp file
___

# Call the R-level fibonacci() fn
___
```

*** =solution
```{r}
# Source the cpp file
sourceCpp("fibonacci.cpp")

# Call the R-level fibonacci() fn
fibonacci(20)
```

*** =sct
```{r}
ex() %>% {
  check_function(., "sourceCpp") %>% {
    check_arg(., "file") %>% 
      check_equal()
  }
  check_function(., "fibonacci") %>% {
    check_arg(., "x") %>% 
      check_equal()
  }
}
```
