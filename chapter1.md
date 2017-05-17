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

```
