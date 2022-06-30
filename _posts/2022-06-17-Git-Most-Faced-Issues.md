
A lot of developers get stuck with version control (specifically git) and spend hours trying to fix a silly mistake or something unimportant. I have tried to compile most viewed git related queries on stackoverflow which are not so straightforward to fix to save your time.

----

## Not so straighforward but all the more commonly faced issus with to the point answers.

1. **I accidentally committed the wrong files to Git but didn't push the commit to the server yet. How can I undo those commits from the local repository?**
    ```
      $ git commit -m "Something terribly misguided" # (0: Your Accident)
      $ git reset HEAD~                              # (1)
      [ edit files as necessary ]                    # (2)
      $ git add .                                    # (3)
      $ git commit -c ORIG_HEAD                      # (4)
    ```

2. **How do I delete a Git branch locally and remotely?**

      ```
      $ git push -d <remote_name> <branchname>
      $ git branch -d <branchname>
      ```

3. **How do I rename a local Git branch?**

    If you want to rename a branch while pointed to any branch, do:
    `git branch -m <oldname> <newname>`
    
    If you want to rename the current branch, you can do:
    `git branch -m <newname>`
    
    If you want to push the local branch and reset the upstream branch:
    `git push origin -u <newname>`
    
    And finally if you want to Delete the remote branch:
    `git push origin --delete <oldname>`
    
    A way to remember this is -m is for "move" (or mv), which is how you rename files. Adding an alias could also help. To do so, run the following:
    
    `git config --global alias.rename 'branch -m'`
  
    If you are on Windows or another case-insensitive filesystem, and there are only capitalization changes in the name, you need to use -M, otherwise,     git will throw branch already exists error:
      `git branch -M <newname>`


4. **How do I undo 'git add' before commit?**
    
    You can undo git add before commit with
    `git reset <file>`
    which will remove it from the current index (the "about to be committed" list) without changing anything else.
    
    You can use
    `git reset`
    without any file name to unstage all due changes. This can come in handy when there are too many files to be listed one by one in a reasonable amount of time.


5. **How do I force an overwrite of local files on a `git pull`?**
    
    If you have any files that are not tracked by Git (e.g. uploaded user content), these files will not be affected.
    
    First, run a fetch to update all `origin/<branch>` refs to latest:
    `git fetch --all`
    
    Backup your current branch:
    
      `git branch backup-master`
    
    Then, you have two options:
        `git reset --hard origin/master`
    
    OR If you are on some other branch:
        `git reset --hard origin/<branch_name>`


6. **How can I make Git "forget" about a file that was tracked, but is now in .gitignore?**
    
    `.gitignore` will prevent untracked files from being added (without an `add -f`) to the set of files tracked by Git. However, Git will continue to track any files that are already being tracked.
    
    To stop tracking a file, you need to remove it from the index. This can be achieved with this command.
    
    `git rm --cached <file>`
    
    If you want to remove a whole folder, you need to remove all files in it recursively.
    
    `git rm -r --cached <folder>`

7. **How do I checkout a remote git branch?**
    
    In both cases, start by fetching from the remote repository to make sure you have all the latest changes downloaded.
    `git fetch`
    
    This will fetch all of the remote branches for you. You can see the branches available for checkout with:
    `git branch -v -a`
    
    The branches that start with remotes/* can be thought of as read only copies of the remote branches. To work on a branch you need to create a local branch from it. This is done with the Git command switch (since Git 2.23) by giving it the name of the remote branch (minus the remote name):
    `git switch test`
    
    In this case Git is guessing (can be disabled with --no-guess) that you are trying to checkout and track the remote branch with the same name.

8. **How do I revert a Git repository to a previous commit?**
      
      ```
      git revert --no-commit <previous commit hash>..HEAD
      git commit
      ```

9. **How to remove local (untracked) files from the current Git working tree?**
      
      ```
      # Print out the list of files and directories which will be removed (dry run)
      git clean -n -d
      ```

      To remove directories, run `git clean -f -d` or `git clean -fd`
      
      To remove ignored files, run `git clean -f -X` or `git clean -fX`
      
      To remove ignored and non-ignored files, run `git clean -f -x` or `git clean -fx`


10. **How can I add a blank directory to a github repository?**
    
    Create a `.gitignore` file inside that directory that contains these four lines:
    
    ```
     # Ignore everything in this directory
    *
    # Except this file
    !.gitignore
     ```

 11. **How do I discard unstaged changes in Git?**
    
   For all unstaged files in current working directory use:
   
    `git restore .`
   
   For a specific file use:
   
    `git restore path/to/file/to/revert`
    
   **Before Git 2.23**
   
   For all unstaged files in current working directory:
    
    `git checkout -- .`
   
   For a specific file:
   
   `git checkout -- path/to/file/to/revert`

12. **Move the most recent commit(s) to a new branch with Git**
    Move your commit(s) to an existing branch
    
    ```
    git checkout existingbranch
    git merge master
    git checkout master
    git reset --hard HEAD~3 # Go back 3 commits. You *will* lose uncommitted work.
    git checkout existingbranch
    ```
13. **How do I push a new local branch to a remote Git repository and track it too?**
    
    ```
    git checkout -b <branch>
    git push -u origin <branch>
    ```
14. **How do I rename my local and remote branches?**
    
    ```
    # Rename the local branch to the new name
    git branch -m <old_name> <new_name>

    # Delete the old branch on remote - where <remote> is, for example, origin
    git push <remote> --delete <old_name>

    # Or shorter way to delete remote branch [:]
    git push <remote> :<old_name>

    # Prevent git from using the old name when pushing in the next step.
    # Otherwise, git will use the old upstream name instead of <new_name>.
    git branch --unset-upstream <new_name>

    # Push the new branch to remote
    git push <remote> <new_name>

    # Reset the upstream branch for the new_name local branch
    git push <remote> -u <new_name>
    ```
15. **How do I clone a specific Git branch?**
    
    ```
    git clone -b <branch> <remote_repo>
    ```
16. **How to squash my last x commits together?**
    
    ```
    git reset --soft HEAD~3 &&
    git commit
    ```
17. **How to change the URL of a git repository?**
    
    ```
    git remote -v
    # View existing remotes
    # origin  https://github.com/user/repo.git (fetch)
    # origin  https://github.com/user/repo.git (push)
    
    git remote set-url origin https://github.com/user/repo2.git
    # Change the 'origin' remote's URL
    
    git remote -v
    # Verify new remote URL
    # origin  https://github.com/user/repo2.git (fetch)
    # origin  https://github.com/user/repo2.git (push)
    ```
18. **How to clone all remote branches in git?**
    
    ```
    $ git clone git://example.com/myproject
    $ cd myproject
    ```
    
    Next, look at the local branches in your repository:
    
    ```
    $ git branch
    * master
    ```
    
    But there are other branches hiding in your repository! You can see these using the -a flag:
    
    ```
    $ git branch -a
    * master
      remotes/origin/HEAD
      remotes/origin/master
      remotes/origin/v1.0-stable
      remotes/origin/experimental
    ```
    
    If you just want to take a quick peek at an upstream branch, you can check it out directly:
    
    ```
    $ git checkout origin/experimental
    ```
    
    But if you want to work on that branch, you'll need to create a local tracking branch which is done automatically by:
    
    ```
    $ git checkout experimental
    ```
    
    and you will see
    >Branch experimental set up to track remote branch experimental from origin.
    Switched to a new branch 'experimental'
    
19. **How to undo git rebase?**
    
    The easiest way would be to find the head commit of the branch as it was immediately before the rebase started in the reflog...
    
    ```
    git reflog
    ```
    and to reset the current branch to it (with the usual caveats about being absolutely sure before reseting with the --hard option).
    
    Suppose the old commit was HEAD@{2} in the ref log:
    
    `git reset --hard HEAD@{2}`

    You can check the history of the candidate old head by just doing a git log HEAD@{2} (Windows: git log "HEAD@{2}").
    If you've not disabled per branch reflogs you should be able to simply do git reflog branchname@{1} as a rebase detaches the branch head before reattaching to the final head.
    
20. **Difference between `git checkout`, `git restore` and `git switch`**
    
    Its sole purpose is to split and clarify the two different uses of git checkout:
    
    `git switch` can now be used to change branches, as `git checkout <branchname>` does
    
    `git restore` can be used to reset files to certain revisions, as `git checkout -- <path_to_file>` does
