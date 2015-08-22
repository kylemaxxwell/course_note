# Course of R Programming Notes #
Version 1.0

----------
## Workspace and Files
Command|Explain
---|---
`getwd()`|Current working directory
`list.files()`<br>`dir()`|List all the files in working directory
`args(list.files)`|Determine the arguments
`dir.create()`|Create a directory in the current working directory
`setwd("testdir")`|Set working directory to "testdir"
`file.create("t.R")`|Create a file "t.R" in working directory 
`file.exists("t.R")`|Check to see if "t.R" exists in the working directory
`file.info("t.R")`|Access information about the file "t.R" 
`file.rename("t.R","t2.R")`|Change the name of the file "t.R" to "p.R"
`file.copy("t2.R","t3.R")`|Make a copy of "t2.R" called "t3.R"
`file.path("t3.R")`|Provide the relative path to the file "t3.R"
`file.path("f1","f2")`|Construct file and directory paths
`dir.create(file.path("d2","d3"),recursive=TRUE)`|Create the nested directory
`unlink("d2",recursive=TRUE)`|Delete the "d2" directory

## Looking at Data
NO|FLOW
:-:|---
1|`ls()`
2|`class(objects)`
3|`dim(objects)`
4|`nrow(objects)`
5|`ncol(objects)`
6|`object.size(objects)`
7|`names(objects)`
8|`head(objects)`
9|`head(objects,10)`
10|`tail(objects,15)`
11|`summary(objects)`
12|`table(objects$sex)`
13|`str(objects)`

## Objects

|-|R has five basic or “atomic” classes of objects:|
|---|---|
|character|strings or individual characters, quoted|
|numeric|any real number(s)|
|integer|any integer(s)/whole numbers|
|complex|-
|logical|variables composed of TRUE or FALSE|
|factor|categorical / qualitative variables|

Notes: `identical(vect,vect2)` test two objects for being exactly equal, return TURE or FALSE.

### Mixing Objects & Explicit Coercion
|EXPLICIT COERCION|EXPLICIT COERCION
---|---|---
<P align=center>MIXING OBJECTS</P>|<P align=center>as.* functions</P>|<P align=center>Nonsensical coercion results in  NA s</P>
`> y <- c(1.7, "a")   ## character`<br>`> y <- c(TRUE, 2)    ## numeric`<br>`> y <- c("a", TRUE)  ## character<br>`|`> x <- 0:6`<br>`> class(x)`<br>`[1] "integer"`<br>`> as.numeric(x)`<br>`[1] 0 1 2 3 4 5 6`<br>`> as.logical(x)`<br>`[1] FALSE  TRUE  TRUE  TRUE  TRUE  TRUE  TRUE`<br>`> as.character(x)`<br>`[1] "0" "1" "2" "3" "4" "5" "6"`|`> x <- c("a", "b", "c")`<br>`> as.numeric(x)`<br>`[1] NA NA NA`<br>`Warning message:`<br>`NAs introduced by coercion`<br>`> as.logical(x)`<br>`[1] NA NA NA`<br>`> as.complex(x)`<br>`[1] 0+0i 1+0i 2+0i 3+0i 4+0i 5+0i 6+0i`

## Numbers
-   Numbers == numeric objects (i.e. double precision real numbers)
-   an integer, specify the `L` suffix, Ex: `1` -> numeric object; `1L` -> integer.
-   `Inf` -> infinity; e.g. `1 / 0` == `Inf`; e.g. `1 / Inf` == 0
-   `NaN` -> an undefined value (“not a number”); e.g. 0 / 0; `NaN` can also be thought of as a missing value

## Attributes
**Function: `attributes()`**  
**Object attributes**

- names, dimnames
- dimensions(e.g. matrices, arrays, factors)
- class
- length
- others user-defined attributes/metadata

## Entering Input & Evaluation & Printing & Creating Vectors
SYMBOL NAME|SYMBOL|USED|NOTES
:-:|:-:|---|---
assignment|`<-`<br><br>`=`|`> x <- 5 ## nothing printed`<br>`> print(x) ## explicit printing`<br>`[1] 5 ## auto-printing occurs`|`[1]` indicates `x` is a vector <br> `5` is the first element
comment|`#`|`> x <-  ## Incomplete expression`|-
sequence|`:`|`> x <- 1:5`|create integer sequences<br>`1:4 + 1` is “2,3,4,5”
sequence|`seq()`|`> seq(0,10,by=0.5)`<br>`> my_seq<-seq(5,10,length=30)`<BR>`> seq(along.with=my_seq)`<BR>`> seq_along(my_seq)`|Ranging from 0 to 10, incremented by 0.5<br>A sequence of 30 numbers between 5 and 10
sequence|`rep()`|`> rep(0,times=40)`<BR>`> rep(c(0,1,2),times=10)`<BR>`> rep(c(0,1,2),each=10)`|contains 40 zeros<BR>contain 10 repetitions of the vector (0, 1, 2)<BR>contain 10 zeros, then 10 ones, then 10 twos
Operators|`+ - * /`|`> x + 2`<br>`> x + c(1,2,3,4)`|`x * y` # element-wise multiple<br>(x and y is matrix)
Operators|`%*%`|`> x <- matrix(1:4, 2, 2) `<br>`> y <- matrix(rep(10, 4), 2, 2)`<br>`x %*% y`|true matrix multiple
logical|&&<br>&#124;&#124;|`x && y #true if both x and y are true`<br>`x` &#124;&#124; `y #true if either x or y are true`|&& lazy.<br>It will only evaluate y if x is true
logical|&<br>&#124;|<P align=left>`x & y #bitwise AND, 0101 & 0011 = 0001`<br>`x` &#124; `y #bitwise OR,  0101` &#124; `0011 = 0111`</p>|& not lazy.<br>y will always be evaluated.
logical|`xor()`|`> xor(5==6,!FALSE) ##[1] TRUE`|TRUE and FALSE, return TRUE
logical|`which()`|`> which(ints>7)`|Argument: a logical vector<br> Return: the indices of the vector that are TRUE
logical|`any()`|`> any(ints<0) #[1] FALSE`|if any of the elements of ints are less than 0
logical|`all()`|`> all(ints>0) #[1] TRUE`|if all of the elements of ints are greater than 0
"collects"<br>"combines"<br>"joins"|`c()`|`> x <- c(0.5, 0.6)       ## numeric`<br>`> x <- c(T, F) ## logical`<br>`> x <- c("a", "b", "c") ## character`<br>`> x <- 9:29 ## integer`<br>`> x <- c(1+0i, 2+4i) ## complex`|create vectors of objects

## Factors

Factors are used to represent categorical data. Factors can be unordered or ordered. One can think of a factor as an integer vector where each integer has a _label_.

- Factors are treated specially by modelling functions like `lm()` and `glm()`
- Using factors with labels is _better_ than using integers because factors are self-describing; having a variable that has values “Male” and “Female” is better than a variable that has values 1 and 2.

```r
> x <- factor(c("yes", "yes", "no", "yes", "no")) 
> x
[1] yes yes no yes no
Levels: no yes
> table(x) 
x
no yes 
 2   3
> unclass(x)
[1] 2 2 1 2 1
attr(,"levels")
[1] "no"  "yes"
```

The order of the levels can be set using the `levels` argument to `factor()`. This can be important in linear modelling because the first level is used as the baseline level.  

```r
> x <- factor(c("yes", "yes", "no", "yes", "no"), levels = c("yes", "no"))
> x
[1] yes yes no yes no 
Levels: yes no
```

## Vector *vs* Lists *vs* Matrices *vs* Table *vs* Data Frames

TYPES|INSTRUCTIONS<td colspan=2>**FUNCTIONS**</td>|EXAMPLES
:-:|:-:|---
**vector**|Elements: **same class**<br>Create empty vectors : `vector()`<td rowspan=5>`length()`<br>`head()`<br>`tail()`<br>`table()`<br>`subset()`<br>`[ ]`<br>`unique()`<br>`sum()`<br>`mean()`<br>`median()`<br>`min()`<br>`max()`<br>`which()`</td><td rowspan=2>`paste()`</td>|`> x<-vector("numeric",length=5)`
**list**|Elements: **different classes**|`> x<-list(1,"a",TRUE,1+4i)`
**matrix**|Elements: **same class**<br>Dimension attribute - `dim(m)`:<br>length 2 integer vector<td rowspan=3>`nrow()`<br>`ncol()`<br>`dim()`<br>`colnames()`<br>`rownames()`<br>`cbind()`<br>`rbind()`<br>`attributes()`<br><br>matrix:<br>`dimnames()`</td>|`> m <- matrix(nrow=2,ncol=3) `<br>`> dim(m)`<br>`> attributes(m)`<br>`# fill the rows first`<br>`> matrix(1:6,ncol=3,byrow=TRUE)`
**table**|Elements: **different classes**<br>Basic tabulation function<br>for *character* and *factor* vectors|`> x =c("b","g","g","b","g")`<br>`> table(x)`<br>`> y = c(1,2,1,2,1)`<br>`> table(x,y)`
**data frame**|Elements: **different classes**<br>Special attribute: `row.names`<br>Created by: `data.frame()`<br>`read.table()` `read.csv()`<br> Convert to matrix: `data.matrix()`|`> x<-data.frame(foo=1:2,bar=c(T,F))`<br>`> nrow(x)`<br>`> ncol(x)`

## Missing Values & Removing NA
### Missing values  
Missing values are denoted by `NA` or `NaN` for undefined mathematical operations.  
- `is.na()` is a logical test for which variables are missing.
- `is.nan()` is used to test for `NaN`
- `NA` values have a class also, so there are integer `NA`, character `NA`, etc.
- A `NaN` value is also `NA` but the converse is not true

```r
> x <- c(1, 2, NA, 10, 3)
> is.na(x)
[1] FALSE FALSE  TRUE FALSE FALSE
> sum(is.na(x))
[1] 1
> is.nan(x)
[1] FALSE FALSE FALSE FALSE FALSE
> x <- c(1, 2, NaN, NA, 4)
> is.na(x)
[1] FALSE FALSE  TRUE  TRUE FALSE
> is.nan(x)
[1] FALSE FALSE  TRUE FALSE FALSE
```
### Removing NA Values
A common task is to remove missing values (`NA`s).
```r
> x <- c(1, 2, NA, 4, NA, 5)
> bad <- is.na(x)
> x[!bad]
[1] 1 2 4 5
```
`complete.cases()` function on a data frame, matrix, or vector, which returns a logical vector indicating.
```r
> x <- c(1, 2, NA, 4, NA, 5)
> y <- c("a", "b", NA, "d", NA, "f")
> good <- complete.cases(x, y)
> good
[1]  TRUE  TRUE FALSE  TRUE FALSE  TRUE
> x[good]
[1] 1 2 4 5
> y[good]
[1] "a" "b" "d" "f"
```

## Subseting
### Subseting vector and list
- `[[` operator can be used with _computed_ indices.
 - can take an integer sequence
- `$` can only be used with literal names.

```r
> x <- c("a", "b", "c", "c", "d", "a")
> x[1]         # [1] "a"
> x[1:4]       #[1] "a" "b" "c" "c"
> x[x > "a"]   # [1] "b" "c" "c" "d"
> u <- x > "a"
> u            # [1] FALSE TRUE TRUE TRUE TRUE FALSE 
> x[u]         # [1] "b" "c" "c" "d"
> x[-c(1,2,3)] # drop first 3 # [1] "c" "d" "a"
---------------------------------------------------
> x <- list(foo = 1:4, bar = 0.6, baz = "hello")
> name <- "bar"
> x[2] == x["bar"] means to x[c(1, 3)]
$bar
[1] 0.6

> x$bar == x[["bar"]] == x[[name]]
[1] 0.6

> x[[1]]       # [1] 1 2 3 4
> x$name       # NULL         ## element ‘name’ doesn’t exist
> x$foo        # [1] 1 2 3 4  ## element ‘foo’ does exist

## Subsetting Nested Elements of a List
> x <- list(a = list(10, 12, 14), b = c(3.14, 2.81))
> x[[c(1, 3)]] # [1] 14
> x[[1]][[3]]  # [1] 14
> x[[c(2, 1)]] # [1] 3.14

## Partial Matching
> x <- list(aardvark = 1:5)
> x$a          # [1] 1 2 3 4 5
> x[["a"]]     # NULL
> x[["a", exact = FALSE]]     # [1] 1 2 3 4 5
```
### Subseting matrix and table and data frame
Matrices can be subsetted in the usual way with (_i,j_) type indices.
```r
> x <- matrix(1:6, 2, 3)
> x[1, 2]      # [1] 3
> x[2, 1]      # [1] 2

# Indices can also be missing.
> x[1, ]       # [1] 1 3 5
> x[, 2]       # [1] 3 4

# Default: a single column or a single element. Return: a vector of length 1. Turn off: `drop = FALSE`.
> x <- matrix(1:6, 2, 3)
> x[1, 2]      # [1] 3
> x[1, 2, drop = FALSE]
     [,1]
[1,] 3

> x[1, ]       # [1] 1 3 5
> x[1, , drop = FALSE]
     [,1] [,2] [,3]
[1,]    1    3    5
> x[1:3, c(2,3]
```

## Reading and Writing Data
### Reading
READING|NOTES|
---|---
`data <- read.table("foo.txt")`|-
`initial <- read.table("datatable.txt", nrows = 100)`<br>`classes <- sapply(initial, class)`<br>`tabAll<-read.table("datatable.txt",colClasses=classes)`|Fast: All columns are “numeric” -> `colClasses = "numeric"`<br>Save memory: Set `nrows` but not faster.<br>No commented lines: Set `comment.char = ""`
### Writing
R Objects|dput-ting|Dumping|
---|---|---
**Code**|`> y <- data.frame(a = 1, b = "a")`<br>`> dput(y)`<br>`> dput(y, file = "y.R")`<br>`> new.y <- dget("y.R")`|`> x <- "foo"`<br>`> y <- data.frame(a = 1, b = "a")`<br>`> dump(c("x", "y"), file = "data.R") `<br>`> rm(x, y)`<br>`> source("data.R")`
**Explain**|Another way to pass data around is by deparsing the R object with dput and reading it back in using `dget`.|Multiple objects can be deparsed using the dump function and read back in using `source`.

### Interfaces to the Outside World
Read Files|FILE|GZFILE / BZFILE|URL
---|---|---|---
**Code**|`con <- file("foo.txt", "r")`<br>`data <- read.csv(con)`<br>`close(con)`<br>is the same as<br>`data <- read.csv("foo.txt")`|`> con <- gzfile("words.gz") `<br>`> x <- readLines(con, 10)`|`con <- url("http://www.jhsph.edu", "r")`<br>`x <- readLines(con)`<br>`> head(x)`
**Explain**|1. `description`: the name of the file<br>2. `open`: a code indicating<br>-- “r” read only<br>-- “w” writing (initializing a new file)<br>-- “a” appending<br>-- “rb”, “wb”, “ab” reading, writing, or appending in binary mode (Windows)|`writeLines` takes a character vector and writes each element one line at a time to a text file.|`readLines` can be useful for reading in lines of webpages

## Control Structures  
See ControlStructures.pdf

## Dates & Time

DATE|**TIME**: `POSIXct`|**TIME**: `POSIXlt`|**TIME**: `strptime()`
---|---|---|---
`s<-Sys.Date()`<br>`unclass(s) #since 1970-01-01`<br>`x <- as.Date("1970-01-01")`<br>`unclass(x) ## [1] 0`<br>`unclass(as.Date("1970-1-2"))`<br>`## [1] 1`|`x <- Sys.time() # ‘POSIXct’ format`<br>`x  ## [1] "2013-01-24 22:04:14 EST"`<br>`unclass(x) ## [1] 1359083054`<br>`x$sec ## Error: ...atomic vectors`<br>`p <- as.POSIXlt(x)`<br>`p$sec ## [1] 14.37`|`x <- Sys.time()`<br>`[1] "2013-01-24 22:04:14 EST"`<br>`p <- as.POSIXlt(x)`<br>`names(unclass(p))`<br>`# [1] "sec"...`<br>`p$sec ## [1] 14.34`|`x <- c("1jan1960", "2jan1960",`<br>` "31mar1960", "30jul1960")`<br>`z <- strptime(x, "%d%b%Y")`<br>`## Sys.setlocale("LC_TIME",lct)`<br>`strptime("2/2/06 11:16:16.683",`<br>` "%d/%m/%y %H:%M:%OS")`
representing calendar dates|`Sys.time()` returns an object of class POSIXct<BR>a very large integer under the hood<br>store in something like *data frame*|a list underneath<br>store a bunch of other useful information like:<br>- *day of the week*<br>- *day of the year, month*<br>- *day of the month*|Directly convert character vectors<br>(of a variety of formats) to POSIXlt format.<BR>Similar to `as.POSIXlt()`, except that the input doesn't have to be in a particular format (YYYY-MM-DD).


- **Format**

	```r
	datestring <- c("January 10, 2012 10:40", "December 9, 2011 9:10")
	x <- strptime(datestring, "%B %d, %Y %H:%M")
	x ## [1] "2012-01-10 10:40:00 EST" "2011-12-09 09:10:00 EST"
	class(x) ## [1] "POSIXlt" "POSIXt"
	?strptime
	```
- **Operate**  

	```r
	x <- as.Date("2012-01-01") # Date class
	x <- as.POSIXlt(x) # Change to POSIXlt for x-y
	y <- strptime("9 Jan 2011 11:34:21", "%d %b %Y %H:%M:%S")
	x-y ## Time difference of 356.3 days
	
	x <- as.Date("2012-03-01") 
	y <- as.Date("2012-02-28")
	x-y ## Time difference of 2 days
	x <- as.POSIXct("2012-10-25 01:00:00")
	y <- as.POSIXct("2012-10-25 06:00:00", tz = "GMT")
	y-x ## Time difference of 1 hours
	```

Notes: Use difftime(Sys.time(), t1, units = 'days') to find the amount of time in DAYS that has passed since created t1.
```r
> difftime(Sys.time(),t1,units="days")
Time difference of 0.03565759 days
```

## Functions
see functions.pdf

- Pass functions as arguments to other functions just like pass data to functions

	```r
	evaluate <- function(func, dat){
	    func(dat)
	}
	
	# 1. evaluate(sum, c(2, 4, 6)) evaluate to 12
	# 2. evaluate(median, c(7, 40, 9)) evaluate to 9
	# 3. evaluate(floor, 11.1) evaluate to 11
	# 4. evaluate(function(x){x+1},6) evaluate to 7
	```
- "Unpack" arguments from an ellipses when use the ellipses as an argument in a function. 

	```r
	mad_libs <- function(...){
	  args<-list(...)
	  place<-args[1]
	  adjective<-args[2]
	  noun<-args[3]
	  paste("News from", place, "today where", adjective, "students go to the", noun, ".")
	}
	
	> mad_libs("Kingsland","senior","streets")
	[1] "News from Kingsland today where senior students go to the streets."
	```
- Syntax for creating new binary operators

	```r
	"%p%" <- function(left,right){
	  paste(left,right)
	}
	
	> 'I' %p% "love" %p% 'R!'
	[1] "I love R!"
	```

## Scoping Rules
see Scoping.pdf

## Loop Functions
NAME|HOW|CODE|EXPLAIN
:-:|---|---|---
**lapply**|Loop over a list<br>Evaluate a function on each element<br>Always returns a list|`x <- list(a = 1:5, b = rnorm(10))`<br>`lapply(x, mean)`<br>`> x<-list(a=matrix(1:4,2,2),b=matrix(1:6,3,2))`<br>`> lapply(x, function(elt) elt[,1])`|-
**sapply**|Same as `lapply`<br>But try to simplify the result|`> x <- list(a=1:4,b=rnorm(10),c=rnorm(20,1))`<br>`> sapply(x, mean)`|each length: 1, RE: vector<br>each length: ==&>1, RE: matrix<br>can’t figure out, RE: list
**apply**|Evaluate a function<br>over the margins of an array|`> x <- matrix(rnorm(200), 20, 10)`<br>`> apply(x, 2, mean)`<br>`> apply(x, 1, sum)`<br>`> x <- matrix(rnorm(200), 20, 10)`<br><br>The shortcut functions are _much_ faster<br>- `rowSums` = `apply(x, 1, sum)`<br>- `rowMeans` = `apply(x, 1, mean)`<br>- `colSums` = `apply(x, 2, sum)`<br>- `colMeans` = `apply(x, 2, mean)`<br><br>Quantiles of the rows of a matrix.<br>`> apply(x, 1, quantile, probs =c(0.25,0.75))`<br><br>Average matrix in an array<br>`> a <- array(rnorm(2 * 2 * 10), c(2, 2, 10))`<br>`> apply(a, c(1, 2), mean)`<br>`> rowMeans(a, dims = 2)`|-
**vapply**|Same as `sapply`<br>Specify the format of output<br>Faster than `sapply()` (large datasets)|`vapply(flags,unique,numeric(1))`<br>`vapply(flags,class,character(1))`|Expect each element of the result :<br> a numeric vector of length 1<br>a character vector of length 1
**tapply**|Apply a function<br>over subsets of a vector|`> x <- c(rnorm(10), runif(10), rnorm(10, 1))`<br>`> f <- gl(3, 10)`<br>`> tapply(x, f, mean)`<br><br>`#without simplification`<br>`> tapply(x, f, mean, simplify = FALSE)`<br><br>`#Find group ranges.`<br>`> tapply(x, f, range)`<br><br>#Group by `flags$red`<br>`> tapply(flags$population,flags$red,summary)`|Split data into groups based on the value of some variable, then apply a function to each group
**mapply**|Multivariate version of `lapply`|`list(rep(1,4),rep(2,3),rep(3,2),rep(4,1))`<br>same as:<br>`> mapply(rep, 1:4, 4:1)`<br><br>`noise <- function(n, mean, sd) {`<br>`rnorm(n, mean, sd)`<br>`}`<br><br>`> mapply(noise, 1:5, 1:5, 2)`<br>same as:<br>`> list(noise(1, 1, 2), noise(2, 2, 2),`<br>`noise(3, 3, 2), noise(4, 4, 2),`<br>`noise(5, 5, 2))`|-

### split

`split` takes a vector or other objects and splits it into groups determined by a factor or list of factors.


```r
> x <- c(rnorm(10), runif(10), rnorm(10, 1))
> f <- gl(3, 10)
> split(x, f)

# A common idiom is `split` followed by an `lapply`.
> lapply(split(x, f), mean)

## Splitting a Data Frame
> library(datasets)
> head(airquality)
> s <- split(airquality, airquality$Month)
> lapply(s, function(x) colMeans(x[, c("Ozone", "Solar.R", "Wind")]))
> sapply(s, function(x) colMeans(x[, c("Ozone", "Solar.R", "Wind")])) 

## Splitting on More than One Level
> x <- rnorm(10)
> f1 <- gl(2, 5)
> f2 <- gl(5, 2)
> interaction(f1, f2)

## Splitting on More than One Level
### Interactions can create empty levels.
> str(split(x, list(f1, f2)))

## Empty levels can be dropped.
> str(split(x, list(f1, f2), drop = TRUE))
```

## Debugging
NAME|EXPLAIN|CODE
---|---|---
`traceback`|prints out the function call stack after an error occurs; does nothing if there’s no error|`> lm(y ~ x)`<br>`> traceback()`
`debug`|flags a function for “debug” mode which allows you to step through execution of a function one line at a time|`> debug(lm)`<br>`> lm(y ~ x)`
`browser`|suspends the execution of a function wherever it is called and puts the function in debug mode|-
`trace`|allows you to insert debugging code into a function a specific places|-
`recover`|allows you to modify the error behavior so that you can browse the function call stack|`> options(error=recover)`<br>`> read.csv("nosuchfile")`

## Profiling
### Using `system.time()`
* Returns an object of class `proc_time`
  The first two entries are the total **user** and **system** CPU times of the current R process and any child processes on which it has waited, and the third entry is the ‘real’ **elapsed** time since the process was started.
  - **user time**: the CPU time charged for the execution of user instructions of the calling process (your code)
  - **system time**: the CPU time charged for execution by the system on behalf of the calling process (your code)
  - **elapsed time**: "wall clock" time or "Real time", clock on the wall time taken. This includes the time when your command wasn't running because the computer was running somebody else's program.


* Usually, the **user time** and **elapsed time** are relatively close, for straight computing tasks
* CPU spends a lot of time waiting around(*may be*): **Elapsed time > user time** 
* Your machine has multiple cores/processors (and is capable of using them)(*may be*): **Elapsted time < the user time**

	```r
	## Elapsed time > user time
	system.time(readLines("http://www.jhsph.edu"))
	   user  system elapsed 
	  0.004   0.002   0.431 
	
	## Elapsed time < user time
	hilbert <- function(n) { 
	        i <- 1:n
	        1 / outer(i - 1, i, "+”)
	}
	x <- hilbert(1000)
	system.time(svd(x))
	   user  system elapsed 
	  1.605   0.094   0.742  
	
	## Timing Longer Expressions
	system.time({
	    n <- 1000
	    r <- numeric(n)
	    for (i in 1:n) {
	        x <- rnorm(n)
	        r[i] <- mean(x)
	    }
	})
	    user  system elapsed 
	   0.097   0.002   0.099
	```
### The R Profiler
* The standard approach to profile R code is to use the `Rprof()` to profile and the `summaryRprof()` function to summarize the result.
* **DO NOT** use `system.time()` and `Rprof()` together or you will be sad
* **NOTE**: If your code runs very quickly, the profiler is not useful.

	```r
	Rprof("path_to_hold_output")
	## some code to be profiled
	Rprof(NULL)
	## some code NOT to be profiled
	Rprof("path_to_hold_output", append=TRUE)
	## some code to be profiled
	Rprof(NULL)
	
	# summarize the results
	summaryRprof("path_to_hold_output")
	```

* `Rprof` works by recording at fixed intervals (by default every 20 msecs) which R function is being used, and recording the results in a file. `summaryRprof` will give you a list with four elements:

	- **by.self**: time spent in function alone.
	- **by.total**: time spent in function and callees.
	- **sample.interval**: the sampling interval, by default every 20 msecs.
	- **sampling.time**: total time of profiling run. Remember that profiling does impose a small performance penalty.

* A short default example collected from the help files is
	```r
	Rprof(tmp <- tempfile())
	example(glm)
	Rprof()
	summaryRprof(tmp)
	```
* Packages **profr** and **proftools** provide graphical tools
	```r
	require(profr)
	require(ggplot2)
	x = profr(example(glm))
	ggplot(x)
	
	Rprof(tmp <- tempfile())
	example(glm)
	Rprof()
	plotProfileCallGraph(readProfileData(tmp), score = "total")
	```

## Simulation ##
### Generating Random Numbers
Functions for probability distributions in R
- `rnorm`: generate random Normal variates with a given mean and standard deviation
- `dnorm`: evaluate the Normal probability density (with a given mean/SD) at a point (or vector of points)
- `pnorm`: evaluate the cumulative distribution function for a Normal distribution 
- `rpois`: generate random Poisson variates with a given rate

Probability distribution functions usually have four functions associated with them. The functions are prefixed with a
- `d` for density
- `r` for random number generation 
- `p` for cumulative distribution
- `q` for quantile function
```r
dnorm(x, mean = 0, sd = 1, log = FALSE)
pnorm(q, mean = 0, sd = 1, lower.tail = TRUE, log.p = FALSE)
qnorm(p, mean = 0, sd = 1, lower.tail = TRUE, log.p = FALSE)
rnorm(n, mean = 0, sd = 1)
```
If $\Phi$ is the cumulative distribution function for a standard Normal distribution, then `pnorm(q)` = $\Phi(q)$ and `qnorm(p)` = $Φ^{−1}(p)$.
```r
> x <- rnorm(10) 
> x <- rnorm(10, 20, 2) 
> summary(x)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
  18.32   19.73   20.55   20.67   21.67   23.39
```
`set.seed` ensures reproducibility
```r
> set.seed(1)
> rnorm(4)     #[1] -0.6264538  0.1836433 -0.8356286  1.5952808
> rnorm(4)     #[1] -0.8204684  0.4874291  0.7383247  0.5757814
> set.seed(1)
> rnorm(4)     #[1] -0.6264538  0.1836433 -0.8356286  1.5952808
```
Generating Poisson data
```r
> rpois(10, 1) #[1] 3 1 0 1 0 0 1 0 1 1
> rpois(10, 2) #[1] 6 2 2 1 3 2 2 1 1 2
> rpois(10, 20)#[1] 20 11 21 20 20 21 17 15 24 20
> ppois(2, 2)  ## Cumulative distribution
[1] 0.6766764  ## Pr(x <= 2) #-> ppois(4, 2)
[1] 0.947347   ## Pr(x <= 4) #-> ppois(6, 2)
[1] 0.9954662  ## Pr(x <= 6)
```
### Generating Random Numbers From a Linear Model
Suppose we want to simulate from the following linear model: y = β0+ β1x + ε where $\varepsilon\sim\mathcal{N}(0, 2^2)$. Assume $x\sim\mathcal{N}(0,1^2)$, $\beta_0 = 0.5$ and $\beta_1 = 2$
```r
> set.seed(20)
> x <- rnorm(100)
> e <- rnorm(100, 0, 2)
> y <- 0.5 + 2 * x + e
> summary(y)
   Min. 1st Qu.  Median
-6.4080 -1.5400  0.6789  0.6893  2.9300  6.5050
> plot(x, y)
```
What if `x` is binary?
```r
> set.seed(10)
> x <- rbinom(100, 1, 0.5)
> e <- rnorm(100, 0, 2)
> y <- 0.5 + 2 * x + e
> summary(y)
   Min. 1st Qu.  Median
-3.4940 -0.1409  1.5770  1.4320  2.8400  6.9410
> plot(x, y)
```
### Generating Random Numbers From a Generalized Linear Model
Suppose we want to simulate from a Poisson model where Y ~ Poisson(μ) and log μ = $\beta_0 + \beta_1x$ and $\beta_0 = 0.5$ and $\beta_1 = 0.3$. We need to use the `rpois` function for this
```r
> set.seed(1)
> x <- rnorm(100)
> log.mu <- 0.5 + 0.3 * x
> y <- rpois(100, exp(log.mu))
> summary(y)
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
   0.00    1.00    1.00    1.55    2.00    6.00
> plot(x, y)
```
### Random Sampling
The `sample` function draws randomly from a specified set of (scalar) objects allowing you to sample from arbitrary distributions.
```r
> set.seed(1)
> sample(1:10, 4)    #[1] 3 4 5 7
> sample(1:10, 4)    #[1] 3 9 8 5
> sample(letters, 5) #[1] "q" "b" "e" "x" "p"
> sample(1:10)       ## permutation  # [1] 4 710 6 9 2 8 3 1 5 
> sample(1:10)       #[1]  2  3  4  1  9  5 10  8  6  7
> sample(1:10, replace = TRUE)       ## Sample w/replacement [1] 2 9 7 8 2 8 5 9 7 8
```

<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [['$','$'], ['\\(','\\)']],
      processEscapes: true,
      skipTags: ['script', 'noscript', 'style', 'textarea', 'pre']
    }
  });
</script>
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>