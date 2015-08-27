# Course of Getting and Cleaning Data Notes
Version 1.0

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

## data.table vs. dplyr in simple

-|data.table|dplyr
---|---|---
**FORM**|`DT[i, j, by]`<BR>The way to read it (out loud) is:<BR>Take DT, subset rows using i, then calculate j, grouped by by|-
**READ**|`fread()`|-
**ROW**|**Subset rows in i**<BR>`ans <- DT[origin == "JFK" & month == 6L]`<BR>`ans <- DT[1:2] # Get the first two rows from DT`<BR>– Sort DT first by column origin in ascending order,<BR>–– and then by dest in descending order<BR>`ans <- DT[order(origin, -dest)]`|–
**COL-<BR>UMN**|**Select column(s) in j**<BR>– Select arr column, but return it as a *vector*<BR>`ans <- DT[, arr]`<BR>– Select arr column, but return as a *data.table* instead<BR>`ans <- DT[, list(arr)]`<BR>– Select both arr and dep columns<BR>`ans <- DT[, .(arr, dep)]`<BR>: Wrap both columns within `.()`, or `list()`<BR>– Select both arr and dep columns<BR>–– and rename them to _arr and _dep.<BR>`ans <- DT[, .(_arr = arr, _dep = dep)]`<BR>**Compute or *do* in j**<BR>`ans <- DT[, sum((arr + dep) < 0)]`