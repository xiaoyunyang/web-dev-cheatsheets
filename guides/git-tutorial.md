# Github / Git Tutorial

#### This is a tutorial for Unix and Git newbies.

### Assume: 
* userA, userB, and userC all setup their computers with a Github account.

### Suppose: 
* userA wants to create an original repo called `ProjectName`.
* userB wants to fork the `ProjectName` repo from usernameB.
* userC wants to fork the `ProjectName` repo from userB.

## Git Tutorial For userA
Go to https://<span></span>github.com/`userA`/ and click the "New" button. Name the new repo `project-name`.

1. On your computer, go to terminal and `cd` into the directory you want to host the local repo, then do:
	
	```
	$ git clone https://github.com/userA/project-name.git
	```
2. Check that you have remote:

	```
	$ git remote
	origin
	```
3. Open the project in finder and in atom to edit the content of the folder.
	
	```
	$ open .
	$ atom .
	```

Alternatively...

On your computer, in Finder, make a new folder called `project-name`. Copy all the files you want to the `ProjectName` folder.

In the Terminal, do the following:

1. Type `cd` into the terminal then drag the `project-name` folder  into the terminal. You’ll see `directoryPath` being auto-completed in Terminal.

	`$ cd directoryPath` 

3. Initialize the `project-name` folder as a git repo. This essentially makes this folder a "local git repo".

	`$ git init`

4. Link your "local git repo" to a "remote git repo" that you own. Call this remote git repo `origin`.

	```
	$ git remote add origin ssh://git@github.com/userA/project-name.git
	```

5. Make sure `origin` is an available remote and check the path.

	```
	$ git remote
	# git remote -v
	```

5. Sync your local repo to your remote repo.

	```
	$ git fetch origin
	$ git add --all
	$ git commit -m 'Initial Commit!'
	$ git push origin master
	```
	
	Note, if you added a LICENSE file or a README file from Github, make sure to do a `git pull origin master` before you do a `git add --all`.

7. Now open a file within `ProjectName` in [Atom](https://atom.io/) or another text editor. Make some changes to the code. To sync your local repo with your remote repo, do this:
	
	```
	$ git fetch origin
	$ git add --all
	$ git commit -m 'I made some changes!'
	$ git push origin master
	```

6. After you accept a pull request from userB, your remote repo is updated. Now you want to sync your local repo with your remote repo:

	$`git pull origin master`
	

<br>

## Git Tutorial For userC
Go to https://<span></span>github.com/`userB`/`project-name` and click the "Fork" button.

In the Terminal, do the following:

1. Type `cd` into the terminal and drag and drop the folder where you want to create the project folder from Finder into Terminal. You’ll see directoryPath being auto-completed in Terminal.

	`$ cd directoryPath` 

2. Make a new folder called `project-name`.

	`$ mkdir project-name`

3. Go into the `project-name` folder

	`$ cd project-name`

4. Initialize the `project-name` folder as a git repo. This essentially makes this folder a "local git repo".

	`$ git init`

5. Link your "local git repo" to a "remote git repo" that you own. Call this remote git repo `origin`.

	`$ git remote add origin ssh://git@github.com/userC/project-name.git`

6. Populate the `project-name` folder on your computer (i.e., local repo) with the files from the remote repo's master branch.


	`$ git pull origin master`

7. List the files in your `project-name` folder to make sure the pull was successful. You should see this folder populated with files downloaded from the remote repo

	`$ ls`

8. Link your "local git repo" to another remote git repo that userB owns. Recall you forked the `project-name` repo from userB. The upstream user's (i.e., userB) repo. Call this `upstream`.
	
	`$ git remote add upstream https://github.com/userB/project-name`

9. Make sure `origin` and `upstream` are both available remote.

	`$ git remote`
	
10. Now open a file within `project-name` in [Atom](https://atom.io/) or another text editor. Make some changes to the code. To sync your local repo with your remote repo, do this:

	```
	$ git fetch origin
	$ git add --all
	$ git commit -m ‘I made some changes!'
	$ git push origin master
	```
	
11. To sync your local repo with userB's repo, do the following:

	`$ git pull upstream master` 
	
	

## Other Useful Commands
Some useful things you can do when things go haywire or just to get the most usefulness out of git:

1. Revert your local repo to the previously committed version when everything still worked:

	```
	$ git log
	
	# git will display the following:
	# commit: <commitID>
	# ...
	
	# type q to exit the log
	: 
	
	# force your local to the commitID
	$ git reset --hard <commitID>
	
	# force your remote to the commitID
	$ git push --force origin master
	
	```

2. Switching between branches:
	
	```
	# checkout the alias of the remote e.g., origin
	$ git remote -v 
	
	# switch to a new branch called boilerplate
	$ git checkout -b boilerplate 
	
	# look at all your branches
	$ git branch 
	# you should see git display the following branches for origin:
	# b1 = master and b2 = boilerplate

	# push local repo to boilerplate branch
	$ git push origin boilerplate  
	
	```
3. Your local repo should never go out of sync with your remote repo. Git prevents you from pushing changes from your local repo to your remote repo if there are changes in your remote repo that you don't have locally. If you don't want to sync with your local repo with the remote and you just want to overwrite the remote repo with whatever you have locally, this is a useful command:

	```
	$ git push origin master --force
	```
 

4. If you accidentally initiated a directory as a git repo, this undos the `git init`:

	```
	$ rm -rf .git
	```

5. 	If you don't want to have to enter your password everytime when you push to a remote, then in the project local repo, do the following:
	
	* Per [**the official guide from Github**](https://help.github.com/articles/changing-a-remote-s-url/), you want to switch your remote URLs from SSH to HTTPS:

	```
	$ git remote set-url origin https://github.com/USERNAME/REPOSITORY.git
	```
	

## Notes on .gitignore
The `.gitignore` file should be included in your local repo folder, which tells git to not add files and folders that you don't want to share publically on Github to your remote repo. What kind of files are these?

* `DS_Store` and other metadata files that macOS create
* `.env` files which includes configuration for your application that may include secret keys that you don't want people to know about.
* Transient files from your working directory that aren't useful to other collaborators, such as compilation products, temporary files IDEs create, etc. Prime example is the `node_modules` which tend to be large and people downloading your project can get from running `npm install` if they have the right dependencies listed in `package.json`.

In general, you should include the following in your `.gitignore`

```
#.gitignore

lib-cov
*.seed
*.log
*.csv
*.dat
*.out
*.pid
*.gz
*.swp

pids
logs
results
tmp
coverage

# API keys
.env
auth.js

# Dependency directory
node_modules
bower_components
build

# Editors
.idea
*.iml

# OS metadata
.DS_Store
Thumbs.db

```