# Version Control with Git

Git and GitHub make it easy to track changes over time, implement features without sacrificing the integrity of the source, and have a well-established version control system with rollback capabilities.

[Git](https://en.wikipedia.org/wiki/Git_(software) refers to a popular source code management system. GitHub provides a GUI for reviewing the code managed by Git. We call this codebase a "repository".

I'll outline a typical workflow. You'll need to make sure that you have Git [installed](https://git-scm.com/download) on your machine. You also need to have created a GitHub account. Finally, this outline also assumes basic working knowledge of the command line.

---

### Forking a Repository

In your browser, navigate to [this repository](https://github.com/sensutec/temp) and click "Fork". You should then select your own GitHub username. 

![Git Fork](http://i.imgur.com/dmMdSPl.jpg)

You have now effectively made an exact copy the repository, but one that's hosted under your username. This is the copy you will make modifications to via the command line.

### Cloning (creating a copy of a repository on your local machine)

Open your terminal and navigate to the directory where you would like to install this demonstration project. I always just use the `projects` folder. It's not important what you call this folder, but it _is_ important that you place it somewhere within reach of your `$HOME` directory. (You can navigate to your home directory by typing `cd $HOME` or more simply, `cd ~`).

So in my case, I would type
```
cd ~/projects
```

Now you're going to "clone" a repository that exists online. Start by copying either the SSH or the HTTPS link for **your recently forked repository**. It should exist at `github.com/myusername/temp`.

![Git Clone](http://i.imgur.com/loE7kEp.jpg)

Once this URL is copied to your clipboard, type the following in the terminal
```
git clone https://github.com/myusername/temp
```

This will create a folder named after the repository, in our case `temp`. Navigate to the temp directory.
```
cd temp
```

### Setting Up Remotes

Right now, there are a total of _three_ different copies of the same repository.
- The original repository that we forked
- The forked copy that exists under our username
- The local version on your computer that you just cloned

The first two of these are our "remotes", because they exist remotely from our computer.

Let's check out our remotes
```
git remote
```

We should see only one response from this command, "origin"
![git remote, origin](http://i.imgur.com/Ob6ACly.png)

If we type `git remote -v` (short for `git remote --verbose`) we will see a more detailed view of our remote.

![git remote, verbose](http://i.imgur.com/HijYk8p.png)

We can safely ignore the nuances between these two listings of "origin", but we should take note of the fact that the URL corresponds to our "fork", the version that we cloned earlier.

---

Let's add the original version as one of our remotes
```
git remote add upstream https://github.com/sensutec/temp
```

Now type in `git remote -v`, you should now see both of our "remotes" listed: one titled "origin" (our fork) and one titled "upstream" (the original version). Moving forward, we will refer to these remotes by these assigned titles.

This concludes the portion of this outline geared towards project setup.

### Making Changes to a Repository

---
**Important**

Before making any changes to a repository, it is important to make sure that your local copy reflects all of the changes that may have happened since you last touched your files. You need to "pull" all changes from "upstream" to update your local copy.
```
git pull upstream master
```
---

Open your newly created folder and its contents in your favorite text editor. Let's create a new file called `test.txt`. Add a few words to `test.txt` and make sure to save the file. (Note: the file should be located inside of the folder that you cloned, in our case `temp`).

Move back into your terminal and type the following
```
git status
```

You should see that your newly created file is now listed under "Untracked files".

![git status](http://i.imgur.com/oj63TN6.jpg)

#### Adding a File

We will now add this file to be tracked by Git.

```
git add test.txt
```

Let's make sure we've correctly added the file by typing `git status`.

![git status new file added](http://i.imgur.com/RDw7Iru.png)

We should see our newly added file under the heading "Changes to be committed".

#### Committing a File

Once the changes to a file have been finalized and we want to stage the file to be uploaded to the repository, we "commit" the file and provide a brief message about our changes.

```
git commit -m "Added a test file to learn git"
```

*This moves the file into a "staging" phase that consists of only the file changes we want to be pushed (added, changed) to the repository. It's the final step before the final step.

#### Pushing a File

Let's take the final step and push the change to the repository. We are going to use the "push" command, followed by the "remote" we want to push *to*, followed by the branch that we want to push, in our case, "master". (We'll follow-up with branching) 

```
git push origin master
```

If the push is successful, we should see some of Git's logging of the file tree merge.

![git tree merge log](http://i.imgur.com/FdIKspd.png)

We should now be see the changes reflected in the forked version of the repository (origin) in our browser (`github.com/myusername/temp`).

### Creating a Pull Request

If we've pushed successfully, we should see a notification below the green "New pull request" button on our forked repository that reads "This branch is 1 commit ahead of master". 

![1 commit ahead of master](http://i.imgur.com/UPgPmV5.png)

If we want to make these changes to the *original* repository (upstream), we can then click "New pull request". We should then summarize the particular changes that we've made, and then submit our pull request. (Note: This is usually when code reviews are done).

After we've submitted our pull request, we have the option to "Merge pull request" (Note: It is usually incumbent upon someone who did **not** submit the pull request to accept the changes). This final step modifies the original repository with our changes.

### Creating a New Branch

If we are creating a new feature or changing the code drastically in any way, it's common procedure to create a new "branch". So far, our example has only reflected the "master" branch, but we can create another, local copy of our repository by creating a new branch. This gives us room to experiment and make changes without affecting our local copy of our remote branches (origin and upstream).

Let's start by making sure that our local copy is caught up with the origin repository.
```
git pull upstream master
```

Let's see what branches currently exist.
```
git branch
```

We should only see one branch listed, "master".

![master branch](http://i.imgur.com/yYq02qN.png)

The star next to master means that it is our current, working branch.

Let's now create a new branch entitled "my-feature". To do this, we'll use the "checkout" command along with the "-b" flag (for branch" to create a new branch.
```
git checkout -b my-feature
```

When we type `git branch` again we should see our new feature branch and also see that it is the active, working branch.

![new branch](http://i.imgur.com/51YGmh3.png)
(Note: The "my-feature" branch inherits the files and changes that exist when the branch was created)

We can now start implementing a new feature or making any changes to the "my-feature" branch without fear of affecting the status of master. In practice, this is a good way to roll out a small version update or to implement a new application feature. We can switch between branches by using `git checkout master` or `git checkout my-feature`. Only the `-b` flag causes us to create a new branch.

**Any files being tracked by Git (using `git add`) will follow us from branch to branch, *but all commits will only affect the active branch*.**

Once we've commited changes to a branch, we can push these changes to our origin with `git push origin my-feature`. GitHub allows us to toggle between branches to view different versions of the repo.

### Merging Branches

Let's say we've committed changes to a branch called "my-feature" that we then want to be merged into the "master" branch. We can use the "merge" command to update the master branch.

First we need to switch to the master branch with `git checkout master`. (Note: It's good practice here to make sure that the master branch is caught up with upstream using `git pull upstream master`). Then we use `git merge` to merge our specified branch into our current one. So, since we're on the master branch
```
git branch merge my-feature
```

The master branch should now have incorporated the changes made in the "my-feature" branch.
