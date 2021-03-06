# Course of Getting and Cleaning Data Notes
Version 1.0

1. [The Tidy Data](#the-tidy-data)
2. [The Code Book](#the-code-book)
3. [Downloading Files](#downloading-files)
4. [Common transforms](#common-transforms)
5. [Sorting and ordering and Creating categorical variables](#sorting-and-ordering-and-creating-categorical-variables)
6. [Summarizing Data](#summarizing-data)
7. [Reshaping Data](#reshaping-data)
8. [Reading Files](#reading-files)
9. [data.table vs dplyr in simple](#datatable-vs-dplyr-in-simple)
10. [Merging Data](#merging-data)
	- [Overview of joins](#overview-of-joins)
	- [Merge in basic data.table and plyr](#merge-in-basic-data-table-and-plyr)

----------
## The Tidy Data 
1. Each variable forms a column.
2. Each observation forms a row.
3. Each type of observational unit forms a table.

## The Code Book
1. Information about the variables (including units!) in the data set not contained in the tidy data
2. Information about the summary choices you made
3. Information about the experimental study design you used

## Downloading Files
- SET working directory  
	Relative - `setwd("./data"); setwd("../")`  
	Absolute - `setwd("/Users/kyle/data/")`  
	Note: Windows - `setwd("C:\\Users\\Downloads")`
- Check directory  

	```r
	if ( !file.exists("data")){ # Check to see if the directory   exists  
		dir.create("data")      # Create a directory if it doesn't   exist  
	}
	```
- Download file  

	```r
	url <- "https://....."
	## Url starts with *https* on Mac -- *method = "curl"*	
	download.file(url, destfile = "./data/some.csv", method = "curl")
	list.files("./data")
	dataDownloaded <- date()
	```
	- Deal with https download on Windows

	```r
	## Method 1
	fileURL <- sub('https', 'http', url)
	## Method 2
	library(RCurl)
	```

## Common transforms
FUNCTION|DESCRIPTION
---|---
`abs(x)`|absolute value
`sqrt(x)`|square root
`ceiling(x)`|`ceiling(3.475)` is 4
`floor(x)`|`floor(3.475)` is 3
`trunc(x)`|`trunc(5.99)` is 5
`round(x, digits=n)`|`round(3.475, digits=2)` is 3.48
`signif(x, digits=n)`|`signif(3.475, digits=2)` is 3.5
`cos(x)`, `sin(x)`, `tan(x)`|also `acos(x)`, `cosh(x)`, `acosh(x)`, etc.
`log(x)`|natural logarithm
`log2(x)`,`log10(x)`|common logarithm
`exp(x)`|ex
OTHERS|[Built-in Functions](http://statmethods.net/management/functions.html)

## Sorting and ordering and Creating categorical variables
**`sort(x, decreasing=FALSE)`**: returns an **object** that has been sorted/ordered.
```r
> head(sort(dat2$daily, decreasing = TRUE)) 
```
**`order(...,decreasing=FALSE)`**: returns the **indices** corresponding to the sorted data.
```r
> datSorted = dat2[order(dat2$daily, decreasing = TRUE), ]

# Fix the row names not refer to their previous values
> rownames(datSorted) = NULL
```
**`ifelse(test, yes, no)`**: returns a value with the same shape as test which is filled with elements selected from either yes or no
depending on whether the element of test is TRUE or FALSE.
```r
> hi_rider = ifelse(dat$daily > 10000, 1, 0)
> table(hi_rider)
> riderLevels = ifelse(dat$daily < 10000, "low", ifelse(dat$daily > 20000, "high", "med"))
```
**`cut()`**: divides the range of x into intervals and codes the values in x according to which interval they fall.
```r
> x = 1:100
> cx = cut(x, breaks = c(0, 10, 25, 50, 100))
> head(cx)
[1] (0,10] (0,10] (0,10] (0,10] (0,10] (0,10]
Levels: (0,10] (10,25] (25,50] (50,100]
> table(cx)
cx
  (0,10] (10,25] (25,50] (50,100]
      10      15      25       50

# leave off the labels
# Note that you have to specify the endpoints(100) of the data, otherwise some of the categories will not be created
> cx = cut(x, breaks = c(0, 10, 25, 50, 100), labels = FALSE)
> head(cx)
[1] 1 1 1 1 1 1
> table(cx)
cx
  1  2  3  4
 10 15 25 50
```
**`cut2()`**: Easier cutting
```r
library(Hmisc)
# SAME TO restData$zipGroups = cut(restData$zipCode,breaks=quantile(restData$zipCode))
restData$zipGroups = cut2(restData$zipCode,g=4)
table(restData$zipGroups)
```

## Summarizing Data
### 03_02_summarizingData
TITLE|SCRIPT
---|---
1. Getting the data from the web|`if(!file.exists("./data")){dir.create("./data")}`<BR>`fileUrl <- "https://data.baltimorecity.gov/api/views/k5ry-ef3g/rows.csv?accessType=DOWNLOAD"`<BR>`download.file(fileUrl,destfile="./data/restaurants.csv",method="curl")`<BR>`restData <- read.csv("./data/restaurants.csv")`
2. Look at a bit of the data|`head(restData,n=3)`<BR>`tail(restData,n=3)`
3. Make summary|`summary(restData)`
4. More in depth information|`str(restData)`
5. Quantiles of quantitative variables|`quantile(restData$councilDistrict,na.rm=TRUE)`<BR>`quantile(restData$councilDistrict,probs=c(0.5,0.75,0.9))`
6. Make table|`table(restData$zipCode,useNA="ifany")`<BR>`table(restData$councilDistrict,restData$zipCode)`
7. Check for missing values|`sum(is.na(restData$councilDistrict))`<BR>`any(is.na(restData$councilDistrict))`<BR>`all(restData$zipCode > 0)`
8. Row and column sums|`colSums(is.na(restData))`<BR>`all(colSums(is.na(restData))==0)`
9. Values with specific characteristics|`table(restData$zipCode %in% c("21212"))`<BR>`table(restData$zipCode %in% c("21212","21213"))`<BR>`restData[restData$zipCode %in% c("21212","21213"),]`
10. Cross tabs|`data(UCBAdmissions)`<BR>`DF = as.data.frame(UCBAdmissions)`<BR>`xt <- xtabs(Freq ~ Gender + Admit,data=DF)`
11. Flat tables|`warpbreaks$replicate <- rep(1:9, len = 54)`<BR>`xt = xtabs(breaks ~.,data=warpbreaks)`<BR>`ftable(xt)`
12. Size of a data set|`print(object.size(fakeData),units="Mb")`
13. Basic statistical summarization|`mean(x)`: takes the mean of x<BR>`sd(x)`: takes the standard deviation of x<BR>`median(x)`: takes the median of x<BR>`quantile(x)`: displays sample quantities of x. Default is min, IQR, max<BR>`range(x)`: displays the range. Same as `c(min(x), max(x))`<BR>`rowMeans(x)`: takes the means of each row of x<BR>`colMeans(x)`: takes the means of each column of x<BR>`rowSums(x)`: takes the sum of each row of x<BR>`colSums(x)`: takes the sum of each column of x<BR>`summary(x)`: for data frames, displays the quantile information
14. Basic summarization plots|`plot(x,y)`: scatterplot of x and y<BR>`boxplot(y~x)`: boxplot of y against levels of x<BR>`hist(x)`: histogram of x<BR>`density(X)`: kernel density plot of x<BR>`matplot(x,y)`: scatterplot of two matrices, x and y<BR>`pairs(x,y)`: plots pairwise scatter plots of matrices x and y, column by column

## Reshaping Data
### 03_04_reshapingData
- **melt** to make long
- ***cast** to go back to wide

TITLE|SCRIPT
---|---
1. Start with reshaping|`library(reshape2); head(mtcars)`
2. Melting data frames|`mtcars$carname <- rownames(mtcars)`<BR>`carMelt <- melt(mtcars,id=c"carname","gear","cyl"),measure.vars=c("mpg","hp"))`<BR>`mtop = melt(top, id.vars='state', variable.name='year', value.name='population')`
3. Casting data frames|`acast()` → arrays/matrices, `dcast()` → data.frames<BR>`cylData <- dcast(carMelt, cyl ~ variable)`<BR>`cylData <- dcast(carMelt, cyl ~ variable,mean)`
4. Averaging values|`tapply(InsectSprays$count,InsectSprays$spray,sum)`
5. Another way - split|`spIns =  split(InsectSprays$count,InsectSprays$spray)`
6. Another way - apply|`sprCount = lapply(spIns,sum)`
7. Another way - combine|`unlist(sprCount); sapply(spIns,sum)`
8. Another way - plyr package|`ddply(InsectSprays,.(spray),summarize,sum=sum(count))`
9. Creating a new variable|`spraySums <- ddply(InsectSprays,.(spray),summarize,sum=ave(count,FUN=sum))`

### More information
> - http://had.co.nz/reshape/
> - But *cast() can replace table() for computing frequency/contingency tables and crosstabs.
> - Formula notation allows you to pick out specificcolumns so wide data can look molten.
> - reshape2 package on [CRAN](http://cran.r-project.org/web/packages/reshape2/)
>- Hadley's [github](https://github.com/hadley/reshape) (bleeding edge)
>- Decision Stats: [“Using Reshape2 for transposing datasets in R”](http://decisionstats.com/2010/11/06/using-reshape2-for-transposing-datasets-in-r/)
>- Recology: [“Good riddance to Excel pivot tables”](http://r-ecology.blogspot.com/2011/01/good-riddance-to-excel-pivot-tables.html)
>- Stack Overflow discussions: [“[r] reshape2”](http://stackoverflow.com/search?tab=votes&q=[r]%20reshape2)
>- A good [plyr primer](http://www.r-bloggers.com/a-quick-primer-on-split-apply-combine-problems/)

## Reading Files
TYPE|USAGE
:-:|---
**Flat**|`read.table("./data.csv",sep=",",header=TRUE)`<BR>`read.csv()`<BR>`read.csv2()`<BR>**Important parameters**<BR>`quote=""` means no quotes<BR>`na.strings="$"` set "$" to missing value<BR>`nrows=10` reads 10 lines<BR>`skip=2` don't read line 2
**Excel**|`library(xlsx)`<BR>`read.xlsx("./data.xlsx",sheetIndex=1,colIndex=2:3,rowIndex=1:4)`<BR>`write.xlsx()` write out an Excel file<BR>`read.xlsx2()` faster than *read.xlsx* for reading subsets of rows and slightly unstable<BR>`library(XLConnect)` [XLConnect](http://cran.r-project.org/web/packages/XLConnect/index.html) package has more options for writing and manipulating Excel files<BR>[XLConnect vignette](http://cran.r-project.org/web/packages/XLConnect/vignettes/XLConnect.pdf)
**XML**|**Read the file into R**<BR>`library(XML)`<BR>`url<-"https://d396qusza40orc.cloudfront.net/getdata%2Fdata%2Frestaurants.xml"`<BR>`url<-sub("https","http",url)`<BR>`doc<-xmlTreeParse(url,useInternal=TRUE) #OR doc<-xmlInternalTreeParse(url)`<BR>`rootNode<-xmlRoot(doc)`<BR>`xmlName(rootNode)`<BR>**Directly access parts of the XML document**<BR>`rootNode[[1]] #OR xmlChildren(rootNode)[[1]]`<BR>`rootNode[[1]][[1]]`<BR>**Programatically extract parts of the file**<BR>`xmlSApply(rootNode[[1]],function(x) xmlValue(x[["zipcode"]])) # With attribute "names"`<BR>**XPath**<BR>`xpathSApply(rootNode[[1]],"//response/row/row/zipcode",xmlValue) # No attribute`<BR><BR>`"/node"` top level node<BR>`"//node"` node at any level<BR>`"node[@attr-name]"` node with attribute name<BR>`"node[@attr-name='bob']"` node with attribute name attr-name='bob'<BR>**Extract content by attributes**<BR>`fileUrl <- "http://espn.go.com/nfl/team/_/name/bal/baltimore-ravens"`<BR>`doc <- htmlTreeParse(fileUrl,useInternal=TRUE)`<BR>`scores <- xpathSApply(doc,"//li[@class='score']",xmlValue)`<BR>`teams <- xpathSApply(doc,"//li[@class='team-name']",xmlValue)`<BR>Official XML tutorials [short](http://www.omegahat.org/RSXML/shortIntro.pdf), [long](http://www.omegahat.org/RSXML/Tour.pdf)<BR>**Notes and further resources**<BR>[An outstanding guide to the XML package](http://www.stat.berkeley.edu/~statcur/Workshop2/Presentations/XML.pdf)
**JSON**|**Reading data from JSON {jsonlite package}**<BR>`library(jsonlite)`<BR>`fromJSON(url)`<BR>**Writing data frames to JSON**<BR>`json<-toJSON(iris,pretty=TRUE)`<BR>`cat(json)`<BR>**Convert back to JSON**<BR>`iris2 <- fromJSON(myjson)`<BR>**Further resources**<BR>[http://www.json.org/](http://www.json.org/)<BR>A good [tutorial](http://www.r-bloggers.com/new-package-jsonlite-a-smarter-json-encoderdecoder/) on jsonlite<BR>[jsonlite vignette](http://cran.r-project.org/web/packages/jsonlite/vignettes/json-mapping.pdf)
**MySQL**|**Connecting and listing databases**<BR>`library(RMySQL)`<BR>`ucscDb<-dbCOnnect(MySQL(),user="genome",host="genome-mysql.cse.ucsc.edu")`<BR>`result<-dbGetQuery(ucscDb,"show databases;")`<BR>`dbDisconnect(ucscDb)`<BR>**Connecting to hg19 and listing tables**<BR>`hg19<-dbCOnnect(MySQL(),user="genome",db="hg19",host="genome-mysql.cse.ucsc.edu")`<BR>`dbLIstTables(hg19)`<BR>`dbLIstFields(hg19,"affyU133Plus2")`<BR>`dbGetQuery(hg19,"select count(*) from affyU133Plus2"`<BR>**Read from the table**<BR>`affyData<-dbReadTable(hg19,"affyU133Plus2")`<BR>**Select a specific subset**<BR>`query <- dbSendQuery(hg19, "select * from affyU133Plus2 where misMatches between 1 and 3")`<BR>`affyMis <- fetch(query)`<BR>`affyMisSmall <- fetch(query,n=10); dbClearResult(query)`<BR>`dbDisconnect(hg19)`<BR>**Further resources**<BR>RMySQL vignette [RMySQL.pdf](http://cran.r-project.org/web/packages/RMySQL/RMySQL.pdf)<BR>[List of commands](http://www.pantz.org/software/mysql/mysqlcommands.html)<BR>A nice [blog](http://www.r-bloggers.com/mysql-and-r/) post summarizing some other commands
**HDF5**|**R HDF5 package**<BR>`source("http://bioconductor.org/biocLite.R")`<BR>`biocLite("rhdf5")`<BR>`library(rhdf5)`<BR>`created = h5createFile("example.h5")`<BR>[rhdf5 tutorial](http://www.bioconductor.org/packages/release/bioc/vignettes/rhdf5/inst/doc/rhdf5.pdf)<BR>**Create groups**<BR>`created = h5createGroup("example.h5","foo")`<BR>`created = h5createGroup("example.h5","baa")`<BR>`created = h5createGroup("example.h5","foo/foobaa")`<BR>`h5ls("example.h5")`<BR>**Write to groups**<BR>`A = matrix(1:10,nr=5,nc=2)`<BR>`h5write(A, "example.h5","foo/A")`<BR>`B = array(seq(0.1,2.0,by=0.1),dim=c(5,2,2))`<BR>`attr(B, "scale") <- "liter"`<BR>`h5write(B, "example.h5","foo/foobaa/B")`<BR>`h5ls("example.h5")`<BR>**Write a data set**<BR>`df = data.frame(1L:5L,seq(0,1,length.out=5), c("ab","cde","fghi","a","s"), stringsAsFactors=FALSE)`<BR>`h5write(df, "example.h5","df")`<BR>`h5ls("example.h5")`<BR>**Reading data**<BR>`readA = h5read("example.h5","foo/A")`<BR>`readB = h5read("example.h5","foo/foobaa/B")`<BR>`readdf= h5read("example.h5","df")`<BR>**Writing and reading chunks**<BR>`h5write(c(12,13,14),"example.h5","foo/A",index=list(1:3,1))`<BR>`h5read("example.h5","foo/A")`<BR>**Notes and further resources**<BR>primarily used for genomics but also has good "big data" packages<BR>* hdf5 can be used to optimize reading/writing from disc in R<BR>The rhdf5 tutorial: [rhdf5.pdf](http://www.bioconductor.org/packages/release/bioc/vignettes/rhdf5/inst/doc/rhdf5.pdf)<BR>* The HDF group has informaton on HDF5 in general [http://www.hdfgroup.org/HDF5/](http://www.hdfgroup.org/HDF5/)
**Web**|**Getting data off webpages - readLines()**<BR>`con = url("http://scholar.google.com/citations?user=HI-I6C0AAAAJ&hl=en")`<BR>`htmlCode = readLines(con)`<BR>`close(con)`<BR>**Parsing with XML**<BR>`library(XML)`<BR>`url <- "http://scholar.google.com/citations?user=HI-I6C0AAAAJ&hl=en"`<BR>`html <- htmlTreeParse(url, useInternalNodes=T)`<BR>`xpathSApply(html, "//title", xmlValue)`<BR>`xpathSApply(html, "//td[@id='col-citedby']", xmlValue)`<BR>**GET from the httr package**<BR>`library(httr); html2 = GET(url)`<BR>`content2 = content(html2,as="text")`<BR>`parsedHtml = htmlParse(content2,asText=TRUE)`<BR>`xpathSApply(parsedHtml, "//title", xmlValue)`<BR>**Accessing websites with passwords**<BR>`pg2 = GET("http://httpbin.org/basic-auth/user/passwd",authenticate("user","passwd"))`<BR>**Using handles**<BR>`google = handle("http://google.com")`<BR>`pg1 = GET(handle=google,path="/")`<BR>`pg2 = GET(handle=google,path="search")`<BR>**Notes and further resources**<BR>* R Bloggers has a number of examples of web scraping [Web Scraping](http://www.r-bloggers.com/?s=Web+Scraping)<BR>* The httr help file has useful examples [httr.pdf](http://cran.r-project.org/web/packages/httr/httr.pdf)
**APIs**|**Find OAuth settings for [github](http://developer.github.com/v3/oauth/)**<BR>`oauth_endpoints("github")`<BR>**To make your own application, register at at [here](https://github.com/settings/applications). Use any URL for the homepage URL(http://github.com is fine) and http://localhost:1410 as the callback url**<BR>`myapp <- oauth_app("My Application",key = "-Client ID-",secret = "-Client Secret-")`<BR>**Get OAuth credentials & Use API**<BR>`github_token <- oauth2.0_token(oauth_endpoints("github"), myapp)`<BR>`gtoken <- config(token = github_token)`<BR>`req <- GET("https://api.github.com/users/jtleek/repos", gtoken)`<BR># OR:<BR>`req <- with_config(gtoken, GET("https://api.github.com/users/jtleek/repos"))`<BR>`stop_for_status(req)`<BR>**With Token**<BR>`sig = sign_oauth1.0(myapp,token = "yourTokenHere",token_secret = "yourTokenSecretHere")`<BR>`req = GET("https://api.twitter.com/1.1/statuses/home_timeline.json", sig)`<BR><BR>`json1<-content(req)`<BR>`json2<-fromJSON(toJSON(json1))`<BR>`json2[json2$name=="datasharing","created_at"]`
**Others**|**Package "foreign"**<BR>Basic functions `read.foo()` [details](http://cran.r-project.org/web/packages/foreign/foreign.pdf)<BR>**Database packages**<BR>*RPostresSQL* provides a DBI-compliant database connection from R [Tutorial](https://code.google.com/p/rpostgresql/), help file-[RPostgreSQL.pdf](http://cran.r-project.org/web/packages/RPostgreSQL/RPostgreSQL.pdf)<BR>*RODBC* provides interfaces to multiple databases including PostgreQL, MySQL, Microsoft Access and SQLite. Tutorial - [RODBC.pdf](http://cran.r-project.org/web/packages/RODBC/vignettes/RODBC.pdf), help file - [RODBC.pdf](http://cran.r-project.org/web/packages/RODBC/RODBC.pdf)<BR>*RMongo* [RMongo.pdf](http://cran.r-project.org/web/packages/RMongo/RMongo.pdf) ([example of Rmongo](http://www.r-bloggers.com/r-and-mongodb/)) and [Interfaces to MongoDb](http://cran.r-project.org/web/packages/rmongodb/rmongodb.pdf)<BR>**Reading images**<BR>[jpeg](http://cran.r-project.org/web/packages/jpeg/index.html) [readbitmap](http://cran.r-project.org/web/packages/readbitmap/index.html) [png](http://cran.r-project.org/web/packages/png/index.html) [EBImage (Bioconductor)](http://www.bioconductor.org/packages/2.13/bioc/html/EBImage.html)<BR>**Reading GIS data**<BR>[rgdal](http://cran.r-project.org/web/packages/rgdal/index.html) [rgeos](http://cran.r-project.org/web/packages/rgeos/index.html) [raster](http://cran.r-project.org/web/packages/raster/index.html)<BR>**Reading music data**<BR>[tuneR](http://cran.r-project.org/web/packages/tuneR/) [seewave](http://rug.mnhn.fr/seewave/)

## data.table vs dplyr in simple

-|data.table|dplyr
:-:|---|---
**F<BR>O<BR>R<BR>M**|`DT[i, j, by]`<BR>The way to read it (out loud) is:<BR>Take DT, subset rows using i, then calculate j, grouped by by|-
**R<BR>E<BR>A<BR>D**|**a) Use cran**<BR>`install.packages("data.table")`<BR>`library(data.table)`<BR>**b) Use Development**<BR>`install.packages("devtools")`<BR>`library(devtools)`<BR>`install_github("Rdatatable/data.table", build_vignettes = FALSE)`<BR><BR>`flights <- fread("https://raw.githubusercontent.com/wiki/`<BR>`arunsrinivasan/flights/NYCflights14/flights14.csv")`|Any data frame<BR>`chicago <- readRDS("chicago.rds")`
**R<BR>O<BR>W**|**a) Subset rows in i**<BR>`ans <- DT[origin == "JFK" & month == 6L]`<BR>`ans <- DT[1:2] # Get the first two rows from DT`|**filter**: extract a subset of rows from a data frame based on logical conditions<BR>`chic.f <- filter(chicago, pm25tmean2 > 30)`<BR>`chic.f <- filter(chicago, pm25tmean2 > 30 & tmpd > 80)`
**S<BR>O<BR>R<BR>T**|– Sort DT first by column origin in ascending order,<BR>–– and then by dest in descending order<BR>`ans <- DT[order(origin, -dest)]`|**arrange**: reorder rows of a data frame<BR>`chicago <- arrange(chicago, date)`<BR>`chicago <- arrange(chicago, desc(date))`<BR>
**C<BR>O<BR>L<BR>U<BR>M<BR>N**|**a) Select column(s) in j**<BR>– Select arr column, but return it as a *vector*<BR>`ans <- DT[, arr]`<BR>– Select arr column, but return as a *data.table* instead<BR>`ans <- DT[, list(arr)]`<BR>– Select both arr and dep columns<BR>`ans <- DT[, .(arr, dep)]`<BR>: Wrap both columns within `.()`, or `list()`<BR>– Select both arr and dep columns<BR>–– and rename them to _arr and _dep.<BR>`ans <- DT[, .(_arr = arr, _dep = dep)]`<BR><BR>**b) Compute or *do* in j**<BR>`ans <- DT[, sum((arr + dep) < 0)]`<BR><BR>**c) Subset in i *and* do in j**<BR>`ans <- DT[origin == "JFK" & month == 6L,`<BR>`.(m_arr=mean(arr), m_dep=mean(dep))]`<BR>– How many trips from “JFK” airport in the month of June?<BR>– Compute the number of rows used any other column<BR>`ans <- DT[origin == "JFK" & month == 6L, length(dest)]`<BR>`ans <- DT[origin == "JFK" & month == 6L, .N] #same to above`<BR>– Select both arr and dep columns the *data.frame* way<BR>–– using `with = FALSE`.<BR>`ans <- flights[, c("arr", "dep"), with=FALSE]`<BR>`# returns all columns except arr and dep`<BR>`ans <- DS[, !c("arr", "dep"), with=FALSE]`<BR>`# or`<BR>`ans <- DS[, -c("arr", "dep"), with=FALSE]`<BR>–– Select by specifying start and end column names<BR>`# returns year,month and day`<BR>`ans <- DS[, year:day, with=FALSE]`<BR><BR>**d) The := operator**<BR>a. The LHS := RHS form<BR>`DT[, c("colA", "colB", ...) := list(valA, valB, ...)]`<BR>`DT[, colA := valA] # only one column to assign`<BR>b. The functional form<BR>`DT[, `:=`(colA = valA, # valA is assigned to colA`<BR>`colB = valB, # valB is assigned to colB`<BR>`...`<BR>`)]`<BR><BR>**e) Add columns by reference**<BR>`DT[, `:=`(speed = distance / (air_time/60), # speed in km/hr`<BR>`delay = arr + dep)]   # delay in minutes`<BR>`## alternatively, using the 'LHS := RHS' form`<BR>`DT[,c("speed","delay"):=list(distance/(time/60),arr+dep)]`<BR><BR>**f) Update some rows of columns by reference**<BR>– Replace those rows where hour == 24 with the value 0<BR>`DT[hour == 24L, hour := 0L]`<BR>`:=` returns the result invisibly. adding an empty `[]` to see the result after the assignment:<BR>`DT[hour == 24L, hour := 0L][]`<BR><BR>**g) Delete column by reference**<BR>`DT[, c("delay") := NULL]`<BR>`## or using the functional form`<BR>`DT[, `\``:=`\``(delay = NULL)]`<BR>– When there is just one column to delete<BR>`DT[, delay := NULL]`<BR><BR>**h) `:=` along with grouping using `by`**<BR>– Contains for each orig,dest pair the maximum speed<BR>`DT[, max_speed := max(speed), by=.(origin, dest)]`<BR><BR>**i) Multiple columns and `:=`**<BR>– Add two columns computing max() of dep_delay and arr_delay for each month<BR>`in_col  = c("dep", "arr")`<BR>`out_col = c("max_dep", "max_arr")`<BR>`DT[,c(out_col):= lapply(.SD,max),by=month,.SDcols=in_col]`<BR><BR>**j) `:=` and `copy()`**<BR>**j.a) `:=` for its side effect**<BR>`foo <- function(DT) {`<BR>`--DT[, speed := distance / (air_time/60)]`<BR>`--DT[, .(max_speed = max(speed)), by=month]`<BR>`}`<BR>`ans = foo(flights) #modifying DT also reflects on flights`<BR><BR>**j.b) The `copy()` function**<BR>`foo <- function(DT) {`<BR>`--DT <- copy(DT) ## deep copy`<BR>`--DT[,speed:=distance/(air_time/60)] ## doesn't affect flights`<BR>`--DT[, .(max_speed = max(speed)), by=month]`<BR>`}`<BR>`ans <- foo(flights)`|**select**: return a subset of the columns of a data frame<BR>`select(chicago, 1:5)`<BR>`names(chicago)[1:3]`<BR>`## [1] "city" "tmpd" "dptp"`<BR>`select(chicago, city:dptp)`<BR>`select(chicago, -(city:dptp))`<BR><BR>**Equivalent base R**<BR>`i <- match("city", names(chicago))`<BR>`j <- match("dptp", names(chicago))`<BR>`chicago[, -(i:j)]`<BR><BR>**mutate**: add new variables/columns or transform existing variables<BR>`chicago <- mutate(chicago, `<BR>`------------------pm25detrend = pm25-mean(pm25, na.rm=TRUE))`<BR><BR>**Rename**: rename variables in a data frame<BR>`chicago <- rename(chicago, dewpoint = dptp,pm25 = pm25tmean2)`
**A<BR>G<BR>G<BR>R<BR>E<BR>G<BR>A<BR>T<BR>I<BR>O<BR>N<BR>S**|**a) Grouping using *by***<BR>– Get the number corresponding to each origin<BR>`ans <- DT[, .(.N), by=.(origin)]`<BR>`# or`<BR>`ans <- DT[, .(.N), by="origin"]`<BR>`# only one column/expression to refer to in j and by,`<BR>`# can drop the .() notation`<BR>`ans <- DT[, .N, by=origin]`<BR>– Calculate the number for each origin for carrier == “AA”<BR>`ans <- DT[carrier == "AA", .N, by=origin]`<BR>– Total number each origin, dest pair for carrier == “AA”<BR>`ans <- DT[carrier == "AA", .N, by=.(origin,dest)]`<BR>`## or equivalently using a character vector in 'by'`<BR>`ans <- DT[carrier == "AA", .N, by=c("origin", "dest")]`<BR>– Average *arr* & *dep* for each orig,dest pair for each month<BR>–– for `carrier == "AA"`<BR>`ans <- DT[carrier == "AA", .(mean(arr), mean(dep)),`<BR>` by=.(origin, dest, month)]`<BR><BR>**b) keyby**<BR>– Order by all the grouping variables<BR>`ans <- DT[carrier == "AA", .(mean(arr), mean(dep)),`<BR>`keyby=.(origin, dest, month)]`<BR><BR>**c) Chaining**<BR>`ans <- DT[carrier == "AA", .N,`<BR>` by=.(origin, dest)][order(origin, -dest)]`<BR>`DT[ ... ][ ... ][ ... ]`<BR><BR>**d) Expressions in `by`**<BR>`ans <- DT[, .N, .(dep>0, arr>0)]`<BR><BR>**e) Multiple columns in `j` - `.SD`**<BR>Special symbol .SD: stands for **S**ubset of **D**ata<BR>`#.SD contains all the columns`<BR>` # except the grouping columns by default.`<BR>`DT[, print(.SD), by=ID]`<BR>`# Compute on (multiple) columns`<BR>`DT[, lapply(.SD, mean), by=ID]`<BR>Specify just the columns we would like to compute<BR>`DT[carrier == "AA", ## Only on trips with carrier "AA"`<BR>`lapply(.SD, mean), ## compute the mean`<BR>`by=.(origin, dest, month), ## for every 'origin,dest,month'`<BR>`.SDcols=c("arr", "dep")] ## just those specified in .SDcols`<BR><BR>**f) Subset .SD for each group:**<BR>– How can we return the first two rows for each month?<BR>`ans <- DT[, head(.SD, 2), by=month]`<BR>– Concatenate columns a and b for each group in ID<BR>`DT = data.table(ID = c("b","b","b","a","a","c"),`<BR>` a = 1:6, b = 7:12, c=13:18)`<BR>`DT[, .(val = c(a,b)), by=ID]`<BR>– Have all the values of column a and b concatenated,<BR>–– but returned as a list column<BR>`DT[, .(val = list(c(a,b))), by=ID]`<BR>`## (1) look at the difference between`<BR>`DT[, print(c(a,b)), by=ID]`<BR>`## (2) and`<BR>`DT[, print(list(c(a,b))), by=ID]`<BR>In (1),for each group,a *vector* is returned,length = 6,4,2 here.<BR>However (2) returns a *list* of length 1 for each group, <BR>with its first element holding vectors of length 6,4,2.<BR>Therefore (1) results in a length of 6+4+2 = 12,<BR> whereas (2) returns 1+1+1=3.|**group_by**<BR>Generating summary statistics by stratum<BR>`chicago <- mutate(chicago,tempcat = factor(1 * (tmpd > 80),`<BR>`----------------labels = c("cold", "hot")))`<BR>`hotcold <- group_by(chicago, tempcat)`<BR>`summarize(hotcold, pm25 = mean(pm25, na.rm = TRUE),`<BR>`----------------o3 = max(o3tmean2),no2 = median(no2tmean2))`<BR><BR>`chicago <-mutate(chicago,year=as.POSIXlt(date)$year + 1900)`<BR>`years <- group_by(chicago, year)`<BR>`summarize(years, pm25 = mean(pm25, na.rm = TRUE),`<BR>`----------------o3 = max(o3tmean2, na.rm = TRUE),`<BR>`-----------no2 = median(no2tmean2, na.rm = TRUE))`<BR><BR>**`%>%`**<BR>`chicago %>% mutate(month = as.POSIXlt(date)$mon + 1)`<BR>`--------%>% group_by(month)`<BR>`--------%>% summarize(pm25 = mean(pm25, na.rm = TRUE),`<BR>`------------------------o3 = max(o3tmean2, na.rm = TRUE),`<BR>`-----------------------no2 = median(no2tmean2, na.rm = TRUE))`
**S<BR>U<BR>M<BR>M<BR>A<BR>R<BR>Y**|The general form of *data.table* syntax is:<BR>`DT[i, j, by]`<BR>**Using i:**<BR>We can subset rows similar to a data.frame - except you don’t have to use `DT$` repetitively since columns within the frame of a *data.table* are seen as if they are *variables*.<BR>We can also sort a *data.table* using `order()`, which internally uses data.table’s fast order for performance.<BR>We can do much more in `i` by keying a *data.table*, which allows blazing fast subsets and joins. We will see this in the “*Keys and fast binary search based subsets*” and “*Joins and rolling joins*” vignette.<BR><BR>**Using j:**<BR>1. Select columns the data.table way: `DT[, .(colA, colB)]`.<BR>2. Select columns the data.frame way: `DT[, c("colA", "colB"), with=FALSE]`.<BR>3. Compute on columns: `DT[, .(sum(colA), mean(colB))]`.<BR>4. Provide names if necessary: `DT[, .(sA =sum(colA), mB = mean(colB))]`.<BR>5. Combine with i: `DT[colA > value, sum(colB)]`.<BR><BR>**Using by:**<BR>Using `by`, we can group by columns by specifying a *list of columns or a character vector of column names or even expressions*. The flexibility of `j`, combined with `by` and `i` makes for a very powerful syntax.<BR>`by` can handle multiple columns and also *expressions*.<BR>We can `keyby` grouping columns to automatically sort the grouped result.<BR>We can use `.SD` and `.SDcols` in j to operate on multiple columns using already familiar base functions. Here are some examples:<BR>1.` DT[, lapply(.SD, fun), by=., .SDcols=...]` - applies `fun` to all columns specified in `.SDcols` while grouping by the columns specified in `by`.<BR>2. `DT[, head(.SD, 2), by=.]` - return the first two rows for each group.<BR>`DT[col > val, head(.SD, 1), by=.]` - combine `i` along with `j` and `by`.<BR>|-
**S<BR>U<BR>B<BR>S<BR>E<BR>T**|**1. Keys**<BR>**a) What is a *key*?**<BR>`DF = data.frame(ID1 = sample(letters[1:2], 10, TRUE),`<BR>`ID2 = sample(1:3, 10, TRUE),val = sample(10),`<BR>`stringsAsFactors = FALSE,row.names = sample(LETTERS[1:10]))`<BR>`rownames(DF)`<BR>`#  [1] "C" "D" "E" "G" "B" "H" "I" "F" "J" "A"`<BR>Each row is limited to *exactly* one row name.<BR>And row names should be *unique*.<BR>`rownames(DF) = sample(LETTERS[1:5], 10, TRUE) #Error`<BR>`DT = as.data.table(DF)`<BR>`rownames(DT)`<BR>`#  [1] "1"  "2"  "3"  "4"  "5"  "6"  "7"  "8"  "9"  "10"`<BR>Use `keep.rownames = TRUE` in `as.data.table()` - this will create a new column called `rn` and assign row names to this column.<BR><BR>**b) Set, get and use keys on a `data.table `**<BR>– Set the column origin as key in the data.table flights<BR>`setkey(DT, origin)`<BR>`## OR provide character vectors to the function 'setkeyv()'`<BR>`setkeyv(DT, "origin") # useful to program with`<BR>– Use the key column origin to subset all rows where the origin airport matches “JFK”<BR>`DT[.("JFK")]`<BR>`## alternatively`<BR>`DT[J("JFK")]` or `DT[list("JFK")]`<BR>`## On single column key of character type`<BR>`DT["JFK"] ## same as DT[.("JFK")]`<BR>`DT[c("JFK", "LGA")]    ## same as DT[.(c("JFK", "LGA"))]`<BR>+ setX and `:=` :<BR>In data.table, the `:=` operator and all the setX (e.g., `setkey`, `setorder`, `setnames` etc..) functions are the only ones which modify the input object by reference.<BR>– Get the column(s) a data.table is keyed by<BR>`key(DT)`<BR>`# [1] "origin"`<BR><BR>**c) Keys and multiple columns**<BR>– Set keys on both origin and dest columns<BR>`setkey(DT, origin, dest)`<BR>`## OR provide a character vector of column names`<BR>`setkeyv(DT, c("origin", "dest"))`<BR>+ It sorts the *data.table* first by the column `origin` and then by `dest` by reference.<BR>– Subset all rows using key columns where first key column origin matches “JFK” and second key column dest matches “MIA”<BR>`DT[.("JFK", "MIA")]`<BR>– Subset all rows where just the first key column origin matches “JFK”<BR>`DT[.("JFK")]`<BR>– Subset all rows where just the second key column `dest` matches “MIA”<BR>`DT[.(unique(origin), "MIA")]`<BR><BR>**2) Combining keys with `j` and `by`**<BR>**a) Select in j**<BR>– Return arr column as a *data.table* corresponding to origin = "LGA" and dest = "TPA".<BR>`DT[.("LGA", "TPA"), .(arr)]`<BR>`DT[.("LGA", "TPA"), "arr", with=FALSE] # return result`<BR><BR>**b) Chaining**<BR>– Use chaining to order the column in decreasing order.<BR>`DT[.("LGA", "TPA"), .(arr)][order(-arr)]`<BR><BR>**c) Compute or do in j**<BR>– Find the maximum arrival delay correspondong to origin = "LGA" and dest = "TPA".<BR>`DT[.("LGA", "TPA"), max(arr)]`<BR>`# [1] 486`<BR><BR>**d) sub-assign by reference using `:=` in j**<BR>`# get all 'hours' in flights`<BR>`DT[, sort(unique(hour))]`<BR>`#  [1]  0  1  2  3 ... 21 22 23 24`<BR>`setkey(DT, hour)`<BR>`key(DT)`<BR>`# [1] "hour"`<BR>`DT[.(24), hour := 0L]`<BR>`key(DT)`<BR>`# NULL`<BR>+ Since we have replaced values on the key column, the *data.table* DT isn’t sorted by hour anymore. Therefore, the key has been automatically **removed** by setting to NULL.<BR><BR>**e) Aggregation using by**<BR>`setkey(DT, origin, dest)`<BR>– Get the maximum departure delay for each month corresponding to origin = "JFK". Order the result by month<BR>`ans <- DT["JFK", max(dep), keyby=month]`<BR>`key(ans)`<BR>`# [1] "month"`<BR>+ Also sets month as the key column<BR><BR>**3) Additional arguments - *mult* and *nomatch* **<BR>**a) The *mult* argument**<BR>– Subset only the **first matching** row from all rows where origin matches “JFK” and dest matches “MIA”<BR>`DT[.("JFK", "MIA"), mult="first"]`<BR>– Subset only the **last matching **row of all the rows where origin matches “LGA”, “JFK”, “EWR” and dest matches “XNA”<BR>`DT[.(c("LGA", "JFK", "EWR"), "XNA"), mult="last"]`<BR>+ The query “JFK”, “XNA” **doesn’t match** any rows in flights and therefore returns **NA**.<BR>+ Once again, the query for second key column dest, “XNA”, is **recycled** to fit the length of the query for **first key column origin**, which is of length 3.<BR><BR>**b) The nomatch argument**<BR>– From the previous example, Subset all rows only if there’s a match<BR>`DT[.(c("LGA", "JFK", "EWR"), "XNA"), mult="last", nomatch = 0L]`<BR>+ Default value for nomatch is NA. Setting nomatch = 0L **skips queries** with no matches.<BR><BR>**4) binary search vs vector scans**<BR>`# key by origin,dest columns`<BR>`DT[.("JFK", "MIA")]`<BR>`# we could have done:`<BR>`DT[origin == "JFK" & dest == "MIA"]`<BR>Keying a data.table result in blazing fast susbets|-
**R<BR>E<BR>S<BR>H<BR>A<BR>P<BR>I<BR>N<BR>G**|**1. Default functionality**<BR>**a) melting *data.tables* (wide to long)**<BR>`DT = fread("https://raw.githubusercontent.com/wiki/`<BR>`Rdatatable/data.table/data/melt_default.csv")`<BR>– Convert DT to *long* form where each dob is a separate observation.<BR>`DT.m1 = melt(DT, id.vars = c("family_id", "age_mother"), `<BR>`measure.vars = c("dob_child1", "dob_child2", "dob_child3"))`<BR>+ `measure.vars` specify the set of columns collapse (or combine) together.<BR>+ Also specify column indices instead of names.<BR>+ By default, variable column is of type factor. Set `variable.factor=  FALSE` return a character vector instead. `variable.factor` is only available in melt from data.table and not in the reshape2 package.<BR>+ By default, the molten columns are automatically named variable and value.<BR>– Name the variable and value columns to child and dob respectively<BR>`DT.m1 = melt(DT,`<BR>`--------measure.vars = c("dob_child1","dob_child2","dob_child3"),`<BR>`--------variable.name = "child", value.name = "dob")`<BR>+ By default, when one of `id.vars` or `measure.vars` is missing, the rest of the columns are automatically assigned to the missing argument.<BR>+ When neither `id.vars` nor `measure.vars` are specified, all non-numeric, integer, logical columns will be assigned to **`id.vars`**.<BR><BR>**b) Casting *data.tables* (long to wide)**<BR>– Get back to the original *data table* DT from DT.m<BR>`dcast(DT.m1, family_id + age_mother ~ child, value.var = "dob")`<BR>– Starting from DT.m, how can we get the number of children in each family?<BR>`dcast(DT.m1, family_id ~ .,`<BR>`------fun.agg = function(x) sum(!is.na(x)),`<BR>`------value.var = "dob")`<BR><BR>**2. Limitations in current melt/dcast approaches**<BR>`DT = fread("https://raw.githubusercontent.com/wiki/`<BR>`Rdatatable/data.table/data/melt_enhanced.csv")`<BR>`## 1 = female, 2 = male`<BR>– Combine (melt) all the **dob** columns together, and **gender** columns together<BR>`DT.m1 = melt(DT, id = c("family_id", "age_mother"))`<BR>`DT.m1[, c("variable" , "child") :=`<BR>`--------------------tstrsplit(variable, "_", fixed=TRUE)]`<BR>`DT.c1 = dcast(DT.m1, family_id + age_mother + child ~ variable,`<BR>`value.var = "value")`<BR>+ `tstrsplit` need *data.table* 1.9.5+<BR><BR>**3. Enhanced (new) functionality**<BR>**a) Enhanced melt(same to 2 above)**<BR>– melt multiple columns simultaneously<BR>`colA = paste("dob_child", 1:3, sep="")`<BR>`colB = paste("gender_child", 1:3, sep="")`<BR>`DT.m2 = melt(DT, measure = list(colA, colB),`<BR>`value.name = c("dob", "gender"))`<BR>– Using patterns()<BR>`DT.m2 = melt(DT, measure = patterns("^dob", "^gender"),`<BR>`value.name = c("dob", "gender"))`<BR><BR>**b) Enhanced dcast**<BR>- Casting multiple `value.vars` simultaneously<BR>`## new 'cast' functionality - multiple value.vars`<BR>`DT.c2 = dcast(DT.m2, family_id + age_mother ~ variable,`<BR>`value.var = c("dob", "gender"))`|-

## Merging Data
### Overview of joins
![](http://i.imgur.com/71MgaPO.jpg)
### Merge in basic data table and plyr
BASIC MERGE|DATA.TABLE MERGE|PLYR MERGE
---|---|---
**Natural join**<BR>To keep only rows that match from the data frames, specify `all=FALSE`.|**An inner join**<BR>`DT1<-data.table(x=c('a','b','dt1'),y=1:3)`<BR>`DT2 <- data.table(x=c('b', 'dt2'), z=4:5)`<BR>`setkey(DT1, x)`<BR>`setkey(DT2, x)`<BR>`merge(DT1, DT2, all=FALSE)`<BR><BR>**Subset joins**(faster) **x[y, nomatch=0]**|`library(plyr)`<BR>`join(x, y, by = NULL, type = "inner")`<BR>`join_all(a_list, by = NULL, type = "inner")`<BR><BR>`library(dplyr)`<BR>`inner_join(x, y)`
**Full outer join**<BR>To keep all rows from both data frames, specify `all=TRUE`.<BR>`mD=merge(DF1,DF2,by.x="id1",by.y="id2",all=TRUE)`|**An outer join**<BR>`merge(DT1, DT2, all=TRUE)`|`library(plyr)`<BR>`join(x, y, by = NULL, type = "full")`<BR>`join_all(a_list, by = NULL, type = "full")`<BR><BR>`library(dplyr)`<BR>`full_join(x, y)`
**Left outer join**<BR>To include all the rows of your data frame x and only those from y that match, specify `all.x=TRUE`.|**A left join**<BR>`merge(DT1, DT2, all.x=TRUE)`<BR><BR>**Subset joins**(faster) **x[y]**<BR>`DT2[DT1]`|`library(plyr)`<BR>`join(x, y, by = NULL)`<BR>`join_all(a_list, by = NULL)`<BR><BR>`library(dplyr)`<BR>`left_join(x, y)`
**Right outer join**<BR>To include all the rows of your data frame y and only those from x that match, specify `all.y=TRUE`.|**A right join**<BR>`merge(DT1, DT2, all.y=TRUE)`<BR><BR>**Subset joins**(faster) **y[x]**<BR>`DT1[DT2]`<BR>See more [here](https://github.com/raphg/Biostat-578/blob/master/Advanced_data_manipulation.Rmd#subset-joins)|`library(plyr)`<BR>`join(x, y, by = NULL, type = "right")`<BR>`join_all(a_list, by = NULL, type = "right")`<BR><BR>`library(dplyr)`<BR>`right_join(x, y)`
**Semi join**<BR>To return all rows from x where there are matching values in y, keeping just columns from x.|`x <- data.table(x = 1:2, y = c("a", "b"))`<BR>`setkey(x, x)`<BR>`y <- data.table(x = c(1, 1), z = 10:11)`<BR>`#The "nrow" in x which have a match from y`<BR>`w = unique(x[y,which=TRUE])`<BR>`# If there are duplicate key values in x`<BR>`w = unique(x[y,which=TRUE,`<BR>`-----allow.cartesian=TRUE])`<BR>`x[w]`<BR>*Or, the other way around* :<BR>`setkey(y,x)`<BR>`w = !is.na(y[x,which=TRUE,mult="first"])`<BR>`x[w]`<BR>See more [here](http://stackoverflow.com/questions/18969420/perform-a-semi-join-with-data-table)|`library(dplyr)`<BR>`semi_join(x, y)`
**Anti join**<BR>To return all rows from x where there are not matching values in y, keeping just columns from x<BR>`table1[!table1$id%in%table2$id,]`|`table1[is.na(match(table1$id,table2$id)),]`<BR>See more [here](http://zevross.com/blog/2014/08/05/using-the-r-function-anti_join-to-find-unmatched-records/)|`library(dplyr)`<BR>`anti_join((x, y)`

*NOTE*: merge all common column names
```r
intersect(names(solutions),names(reviews))
```