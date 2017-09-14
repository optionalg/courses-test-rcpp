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

The first time you run this code, there will be a delay of a few seconds while the code compiles. The second time, it should run instantaneously. 

I think everything is OK with this exercise.

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



--- type:NormalExercise lang:r xp:100 skills:1 key:bb6ca8dc4d
## Demonstrating C++ concepts

In order to work with Rcpp, it is useful to know some C++.  For an introductory exercise that teaches students about asigning diferent types of variable, I'd like a C++ script pane like this.

```{cpp}
// Define an integer x, equal to 6
___ x = ___;

// Define a double y, equal to 3.33
___ y = ___;
```

The current workaround uses `cppFunction()`, which is less than ideal since there is a lot of code the the student has to ignore, and because their code is being written insdie a string, which makes it hard to debug.

I'm also seeing an error message `Error: Error 1 occurred building shared library.` when calling `cppFunction()` that I've not quite figured out yet.

*** =instructions

You are going to define variables using C++. To make them available in R, they are being wrapped in a call to `cppFunction()`. This will be discussed later in the course, along with other ways of writing C++ code. For now, focus only on the variable assignment.

- Define an integer named `x`, setting the value equal to `6`.
- Define an double precision floating point number named `y`, setting the value equal to `3.33`.


*** =hint

- In C++, integers have type `int`, and double-precision floating point numbers have type `double`.

*** =pre_exercise_code
```{r}
library(Rcpp)
```

*** =sample_code
```{r}
cppFunction('

List defineVariables(){
    // Define an integer x, equal to 6
    ___ x = ___;
    
    // Define a double y, equal to 3.33
    ___ y = ___;
    
    return List::create( 
        _["x"] = x, _["y"] = y
    ) ;
}

')
defineVariables()
```

*** =solution
```{r}
cppFunction('

List defineVariables(){
    // Define an integer x, equal to 6
    int x = 6;
    
    // Define a double y, equal to 3.33
    double y = 3.33;
    
    return List::create( 
        _["x"] = x, _["y"] = y
    ) ;
}

')
defineVariables()
```

*** =sct
```{r}

```
--- type:RCppExercise lang:r xp:100 key:d3837f8c96
## Using a C++ file

A common way to write non-trivial Rcpp code is to write it in a C++ (`.cpp`) file.  By using an Rcpp export tag, `// [[Rcpp::export]]` the function is exported to R. This can then be called from the same file, by using an `/*** R` comment block. The pattern is as follows.

```{cpp}
#include <Rcpp.h>
using namespace Rcpp;

// [[Rcpp::export]]
double some_function(double x) {
  //do something here ...
  return(y)
}

/*** R
  # Call the R-level some_function() fn 
  some_function(123) 
*/
```

Here's an example C++ function taken from section 1.2.5 of [Seamless R and C++ Integration with Rcpp](https://www.springer.com/us/book/9781461468677).

The trick is that the Submit Answer button should call `sourceCpp()` to run the code.
``

*** =instructions
- Call `#include <Rcpp.h>` to include the Rcpp header file.
- Call `using namespace Rcpp;` to access the contents of the `Rcpp` namespace without explicitly mentioning it.
- Complete the definition of the `fibonacci()` function.
    - If `x` is less than 2, return `x`.
    - Otherwise, recursively call `fibonacci()` on `x - 1` and on `x - 2`, then return the sum of the results.
- Run the R-level `fibonacci()` function that is generated with 20 as an input.

*** =hint

- This exercise doesn't work yet, because there is nowhere to write the C++ code.

*** =pre_exercise_code
```{r}
'___BLOCK_SOLUTION_EXEC___'
library(Rcpp)
```

*** =sample_code
```{cpp}
// Include the Rcpp header file
___

// Use the Rcpp namespace
___

// [[Rcpp::export]]
int fibonacci(const int x) {
  // If x is less than 2, return x
  ___
  /* 
  otherwise recursively call fibonacci()
  on x - 1 and x - 2, then return the sum
  */
  ___
}

/*** R
  # Call the R-level fibonacci() fn 
  # to get the 20th fibonacci number
  ___ 
*/
```

*** =solution
```{cpp}
// Include the Rcpp header file
#include <Rcpp.h>

// Use the Rcpp namespace
using namespace Rcpp;

// [[Rcpp::export]]
int fibonacci(const int x) {
  // If x is less than 2, return x
  if(x < 2) {
    return x;
  }
  /* 
  otherwise recursively call fibonacci()
  on x - 1 and x - 2, then return the sum
  */
  else {
    return(fibonacci(x - 1) + fibonacci(x - 2));
  }
}

/*** R
  # Call the R-level fibonacci() fn 
  # to get the 20th fibonacci number
  fibonacci(20) 
*/
```

*** =sct
```{r}
# Unclear how to test the C++ code, other than seeing if it compiles.
parse_rcpp <- function(state) {
  childState <- ChildState$new(state)
  childState$student_code <- extract_r_code_from_rcpp(state$student_code)
  childState$solution_code <- extract_r_code_from_rcpp(state$solution_code)
  childState$set(
    student_pd = build_pd(childState$student_code),
    solution_pd = build_pd(childState$solution_code)
  )
  childState
}

seq_int <- function(lo, hi) {
  if(hi < lo) return(integer())
  seq.int(lo, hi, by = 1)
}

extract_r_code_from_rcpp <- function(code_lines, flatten = TRUE) {
  start_line <- which(grepl(" */\\*{3} +R", code_lines))
  end_line <- which(grepl(" *\\*/", code_lines))
  r_chunks <- Map(seq_int, start_line + 1, end_line - 1) %>% 
    lapply(function(x) code_lines[x])
  if(flatten) {
    r_chunks <- unlist(r_chunks, use.names = FALSE)
  }
  r_chunks
}

build_pd <- function(code) {
  tryCatch(getParseData(parse(text = code, keep.source = TRUE), includeText = TRUE),
           error = function(e) return(NULL))
}

ex() %>% 
  parse_rcpp() %>% {
  check_function(., "fibonacci") %>% {
    check_arg(., "x") %>% 
      check_equal()
  }
}
```
