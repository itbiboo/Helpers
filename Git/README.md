# Git handbook
---
## Basics
```bash
# init, clone, fetch, pull
git init #to initialize a local git repo
git clone https://github.com/tgogos/[name].git
git remote update
git pull origin master

# check status, add changes, commit, push
git status
git add filename
git add . #commonly used to add everything that was changed to the staging area
git commit -m "short message goes here..."
git commit -m "short message goes here..." -m "long description goes here (optional)..."

#(The -u tells Git to remember the parameters, so that next time we can simply run "git push" and Git will know what to do.)
git push -u origin master
REMOVE FILE: git rm [filename]         (wildcards can also be used, for example '*.txt' /don't forget using quotes)
```


OTHER
-----
STATUS:		  	
LOG:			  	git log

DIFF:			  	git diff HEAD	          (diff of the most recent commit)
              git diff --staged       (see the changes you just staged)
RESET Stage	  git reset [FILE]        (unstage files)


BEFORE STAGING
--------------
DISCARD CHANGES	      git checkout -- [FILE]  (File will be changed back to how it was at the last commit)
DISCARD CHANGES (ALL) git checkout -- .

DISCARD UNTRACKED FILES/FOLDERS git clean -df
  # If an untracked directory is managed by a different Git repository,
  # it is not removed by default. Use -f option twice if you really want to remove such a directory.


BRANCHES
--------
SHOW BOTH REMOTE & LOCAL BRANCHES: git branch -a
SHOW ONLY REMOTE BRANCHES: git branch -r
SHOW ONLY LOCAL  BRANCHES: git branch
SHOW CURRENT BRANCH git show-branch --current
CREATE BRANCH		git branch [NAME]
SWITCH BRANCH		git checkout [NAME]
MERGE  BRANCH		git merge [NAME]        (for example go to 'master' branch with '$ git checkout master' and merge 'develop' branch into it with '$ git merge develop')
DELETE BRANCH		git branch -d [NAME]


STOP TRACKING	    	git update-index --assume-unchanged [path]
UNDO STOP TRACKING	git update-index --no-assume-unchanged filename
		
git config user.name (shows your username)





HOW TO ADD A LOCAL .GIT REPOSITORY TO BITBUCKET
-------------------------------------------------

after you have added files and done your first commit...
	-	cd /path/to/my/repo git remote add origin https://[user]@bitbucket.org/[user]/[name].git
	-	git push -u origin --all # pushes up the repo and its refs for the first time
	-	git push -u origin --tags # pushes up any tags


HOW TO REMOVE FROM STAGING AREA (WHAT IS THE OPPOSITE OF GIT ADD)
-------------------------------------------------------------------
Let's say that you have made changes to a couple of files, used "git add ." and now you are ready to commit your changes.
But then you think that it might be better to use more than one commits and group your files according to the reason they were changed. You can remove files from the staging area by using "git reset <file>"