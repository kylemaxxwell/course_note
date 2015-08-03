# Course notes of the Data Scientist’s Toolbox #
Version 1.0

----------

## Getting Help ##
```R
?rnorm               # access help file
help("rnorm")        # access help file
help.search("rnorm") # search help files
args("rnorm")        # get arguments
rnorm                # see code
example(rnorm)       # show examples
class(rnorm)         # show rnorm object class
str(rnorm)           # see structure of the object
```
## CLI Commands - command flags arguments ##


- ***command*** is the CLI command which does a specific task
- ***flags*** are options we give to the ***command*** to trigger certain behaviors, preceded by a -
- ***arguments*** can be what the ***command*** is going to modify, or other options for the ***command***

|-|COMMAND|EXPLAIN
|:-:|:---:|----
|-|**pwd**| displays the path to the current working directory
|-|**clear**| will clear out the commands in your current CLI window
|-|**ls**| lists files and folders in the current directory
|-|**ls -a**| lists hidden and unhidden files and folders
|-|**ls -al**| lists details for hidden and unhidden files and folders
|-|**cd**| stands for "change directory"
|-|**cd ..**| allows you to change directory to one level above your current directory
|-|**mkdir**| stands for "make directory"
|-|**touch**| creates an empty file
|-<td rowspan=2 align="center">**cp**</td>| stands for "copy"
|-| `cp -r Documents More_docs` copies the contents of `Documents` into `More_docs`
|-|**rm**| stands for "remove"
|-|**rm -r**| delete entire directories and their contents
|-|**mv**| stands for "move" and rename files
|-|**echo**| will print whatever arguments you provide
|-|**date**| will print today's date



```bash
jeff$ pwd
/Users/jeff
jeff$ cp test_file Documents
jeff$ cd Documents
jeff$ ls 
test_file
jeff$ mkdir More_docs
jeff$ cp -r Documents More_docs
jeff$ echo Hello World!
Hello World!
```

## Git ##
### Basic
* Two methods of creating a GitHub repository:
  1. Start a repository from scratch
  2. "Fork" another user's repository
- Configure Username and Email
```
$ git config --global user.name "Your Name Here"
$ git config --global user.email "your_email@example.com"
```
- Checking Your Settings
```
$ git config --list
```
- Getting Help
```
$ git help <verb>
$ git <verb> --help
$ man git-<verb>
$ git help config	# example
```
- Ignoring Files
```
$ cat .gitignore
*.[oa]	# ignore any files ending in “.o” or “.a”
*~		# ignore all files that end with a tilde ( ~ )
```
- Close Git Bash
```
$ exit
```

### Create a Local copy
* Create a directory on your computer where you will store your copy of the repo: 
	- (The tilde (`~`) symbol refers to your "home" directory)
```
$ mkdir ~/test-repo
```
* Navigate to this new directory using the following command:
```
$ cd ~/test-repo
```
* Initialize a local Git repository in this directory
```
$ git init
```
* Point your local repository at the remote repository
```
$ git remote add origin https://github.com/your-user-name-goes-here/test-repo.git
```

###Basic Git Commands
|-|GIT COMMAND|EXPLAIN
|:-:|-----------|---
|-|**git init**| Initializes a Git repo in our current directory
|-|**git add .**| Adds all new files (note the period after `add`, which represents "all files")
|-|**git add -u**| Updates tracking for files that changed names or were deleted
|-|**git add -A** / **git add --all**| Does both of the previous No. 1 and 2
|-|**git status**| Does exactly what is sounds like -- gives a "status report" for our local repo
|-|**git status -s**|  `git status -s` or  `git status --short` you get a far more simplified output from the command
|-|**git commit -m "your message"**| Commit changes
|-|**git log**| Shows a history of all commits. Spacebar advances page by page. Typing the letter "Q" exits the log. P58
|-|**git remote -v**| Shows that our GitHub repo is now set up as a "remote" repository for our local repo
|-|**git remote add [shortname] [url]**| `git remote add pb https://github.com/paulboone/ticgit`
|-|**git remote show [remote-name]**| lists the URL for the remote repository as well as the tracking branch information
|-|**git remote rename pb paul**| rename  pb to  paul
|-|**git remote rm paul**| remote name will turn back to "origin"
|-|**git push -u [remote-name][branch-name]**| Tells Git to push our changes to the "master" (branch-name) branch of the "origin" (remote-name) remote. `-u origin master` only once added.
|-|**git pull**| "pull" changes from a remote repository to our local repository, but it's  equals `git fetch` + `git merge`
|-|**git fetch [remote-name]**| To fetch all changes from the "pb" remote repo and all branch.
|-|**git branch -a**| Shows that we now have two "branches" stored on our computer: `master` and `remotes/origin/master`
|-|**git branch -D [branch]**| Delete local branch.
|-|**git push origin --delete [branch]**| Delete remote branch.
|-|**git merge origin/master**| Incorporate the changes from our remote repo
|-|**git checkout [branch]**| Switched to branch 'master' local branch automatically pull from  origin/master, and update local repository
|-|**git checkout -b [branch] [remotename]/[branch]**| `git checkout -b sf origin/serverfix` local branch  sf will automatically pull from  origin/serverfix


### VIM Commands
####Opening a New File

- Step 1	type	vim filename	(create a file named filename)
- Step 2	type	i	( switch to insert mode)
- Step 3	enter text	(enter your Ada program)
- Step 4	hit	Esc key	(switch back to command mode)
- Step 5	type	:wq	(write file and exit vim)

####Editing the Existing File

- Step 1	type	vim filename	(edit the existing file named filename)
- Step 2	move around the file using h/j/k/l key or any appropriate command
	1. h Moves the cursor one character to the left
	2. l Moves the cursor one character to the right
	3. k Moves the cursor up one line
	4. j Moves the cursor down one line
	5. nG or :n Cursor goes to the specified (n) line
	6. (ex. 10G goes to line 10)
- Step 3	edit required text (replace or delete or insert)
- Step 4	hit Esc key (exit from insert mode if you insert or replace text)
- Step 5	type	:wq

## R Package
- Obtain information about the available packages on CRAN with the `available.packages()` function
```r
a <- available.packages()
head(rownames(a), 3)  ## Show the names of the first few packages
```
- A list of some topics is available through the [Task Views](http://cran.r-project.org/web/views/) link, which groups together many R packages related to a given topic
- Packages can be installed with the `install.packages()` function in R
```r
install.packages("slidify")
install.packages(c("slidify", "ggplot2", "devtools"))
```
- Loading R Packages and list packages that have been installed
```r
library(ggplot2)
search()
ls("package:ggplot2")
library(help=ggplot2)
```
###Rtools
* A collection of tools necessary for building R packages in Windows
* If you aren't sure, enter ```find.package("devtools")``` in the console
* To install devtools, use ```install.packages("devtools")```
* About Rtools see more in 02_10_rtools.pdf