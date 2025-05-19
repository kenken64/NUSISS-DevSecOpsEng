# S-DOEA - Workshop 1 - Github Workshop

## Pre-requisites 
* Individual workshop
* [Github](https://github.com/)/[Gitlab](https://about.gitlab.com/) account
* Download & Install Git for Windows https://git-scm.com/download/win
* Github SSH Windows 10 Setup https://dev.to/bdbch/setting-up-ssh-and-git-on-windows-10-2khk
* Github SSH MacOS Setup https://bit.ly/2Gndh54

### For mac users please use brew 

Homebrew (http://brew.sh/) is another alternative to install Git. If you have Homebrew installed, install Git via

```
brew install git
```

## Instructions

1. Create/Sign up for a Github account
<br>
<img style="width:350px;height:100px; float: center;" src="./screens/git1.png"/>
<br>

2. Create a working directory on your machine

```
mkdir Projects

cd Projects

mkdir ProjectA

cd ProjectA

```
3. Initialize the working directory as git enable project

```
git init
```

4. Go to the Github dashboard, add a new repository
<br>
<img style="width:350px;height:100px; float: center;" src="./screens/git2.png"/>
<br>


5. Create a repo on Github, do not initialize README.md
<br>
<img style="width:350px;height:100px; float: center;" src="./screens/git3.png"/>
<br>

6. Associate the local working directory with the remote repository

```
git remote add origin https://github.com/<your github username>/ProjectA.git
```

<br>
<img style="width:350px;height:100px; float: center;" src="./screens/git4.png"/>
<br>

7. Issue the following command to to verify the association is correct

```
git remote -v

origin	https://github.com/<your github username>/ProjectA.git (fetch)
origin	https://github.com/<your github username>/ProjectA.git (push)
```

8. Create a README.md documentation as the initial file for your newly created repository

```
echo "# ProjectA" >> README.md
```

9. Create a html file index.html file with following content place on the project A directory

```
<!DOCTYPE html>
<html>
<body>

<h1>My First Heading</h1>
<p>My first paragraph.</p>

</body>
</html>
```

10. Add the files to the staging index

```
git add .
```

11. Confirm all the changes made to the working directory and ready to commit to the remote repository by issuing the following command. argument -m is comment to every check in of the source codes.

```
git commit -m "new"
```

12. Check in the changes to the remote repository

```
git push origin master

Counting objects: 7, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 510.18 KiB | 17.59 MiB/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Resolving deltas: 100% (4/4), completed with 4 local objects.
To https://github.com/<your github username>/ProjectA.git
   84e356d..b6c5251  master -> master
```

13. Verify all the files are pushed to the github repo after the above steps. Use your browser navigate to the https://github.com/kenken64/ProjectA.git (replace the github userid with yours)

14. Next on your terminal/command prompt let us create a branch in git.

```
git checkout -b enhancementA
```

15. In order to check the current branch run the below command, the terminal should see * pointed to the newly created branch name

```
git branch
```

16. Create a new file for this this newly created branch, index2.html

```
<!DOCTYPE html>
<html>
<body>

<h1>My First Heading</h1>
<p>My first paragraph.</p>

</body>
</html>
```

17. Add the new file created for the new branch to the remote repository

```
git add .
```

```
git commit -m "new enhancement"
```

```
git push origin enhancementA -u
```

18. Navigate to the project A github url https://github.com/kenken64/ProjectA.git check the branch is created. (Replace the github userid with yours)

19. Create a pull request for your newly created branch

<img style="width:350px;height:100px; float: center;" src="./screens/git6.png"/>

20. Merge your newly created branch with your master branch.


** Whenever there is new files created or any changes to the current codebase if the developer would like to check in their codes please run step 8,9,10 again. For those of you not very comfortable with command line there are many IDE Git integration software out there. See below screen capture for Visual Studio Code Git Plugin. Also Git GUI Standalone software by Atlassian https://www.sourcetreeapp.com/

<br>
<img style="width:350px;height:100px; float: center;" src="./screens/git5.png"/>
<br>


For branching, tagging and etc please visit the following link https://github.com/kenken64/NUSISS-DevOpsEng/blob/master/git/README.md

## Optional Workshop

21. Let's try out git merge with a typical combining multiple branches of commits from master and feature branches



<img style="width:350px;height:100px; float: center;" src="./screens/git7.png"/>

22. Fork the following repository https://github.com/kenken64/gitmerge-workshop to your own github account

23. The outcome of this optional workshop is to merge all the feature and master branches changes to a new master combined version

```
git branch

*master
```
* Make sure your current branch is master, make changes to the index.html for commit 1

```
nano index.html
```

```
......
commit 1
```

* Push the commit 1 to the master branch

```
git add .
git commit -m "commit 1"
git push origin master
```

Make changes to the index.html for commit 2

```
nano index.html
```

```
......
commit 1
commit 2
```

* Push the commit 2 to the master branch

```
git add .
git commit -m "commit 2"
git push origin master
```

Branch out from commit 2


* Push the master branch's commit 2 to the feature1 branch

```
git checkout -b feature1
git add .
git commit -m "create feature 1 branch"
git push origin feature1
```

* Create another feature2 branch from commit 2 

```
git checkout -b feature2
```

* Create new index5.html with any content. Save the file

```
nano index5/d
.html
```

* Push the master branch's commit 2 to the feature2 branch

```
git add .
git commit -m "feature 2"
git push origin feature2
```


Switch back to the master branch to the latest commit

```
git checkout master
```

Merge feature2 branch into master branch, where feature1 is being ignored. 
```
git merge --squash feature2
```

24. Issue a commit command to stage the changes

```
git commit -m "feature2 and master combined with commit 1,2,3"
```

25. Push the final changes to the master branch

```
git push origin master -u
```

26. Edit the index2.html on the master branch, add a new paragraph right below hello world

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <p>Hello World</p>
    <p>Hello World 2</p>
</body>
</html>
```


27. Undo the mistakes done on the index2.html

```
git status
```

```
git restore index2.html
```

```
git status
```

28. Setup Claude desktop with GenAI Github Model Context Protocol server. try out this prompt "Under my GitHub account, there's a repository named ProjectAA. Is it possible to merge the test branch into the main branch? Create the pull request and merged it"

https://claude.ai/download

https://github.com/github/github-mcp-server

https://www.deeplearning.ai/short-courses/mcp-build-rich-context-ai-apps-with-anthropic/


## Reference
* Software sourcetree - https://www.sourcetreeapp.com/
* Eclipse with Git - https://www.vogella.com/tutorials/EclipseGit/article.html
* Visual Studio Code - https://scotch.io/tutorials/git-integration-in-visual-studio-code
* Git Tower - https://www.git-tower.com/windows
* Undo your git mistakes - https://www.youtube.com/watch?v=lX9hsdsAeTk
