
Git Branch Management
---

#### References

https://www.ibm.com/developerworks/cn/java/j-lo-git-mange/index.html</br>
https://www.gratasoftware.com/what-is-each-server-for-development-test-uat-or-staging-demo-and-production/</br>
https://www.cnblogs.com/mark888/p/7222242.html</br>

#### Git repositories for infrastructure as code, configuration as code and experimental demos

```text
http://gitlab.inc.heylinux.com/sre/heylinux-cac
http://gitlab.inc.heylinux.com/sre/heylinux-iac
http://gitlab.inc.heylinux.com/sre/heylinux-exp
```

#### Branches

```bash
develop        # for development, for dev/fat deployment, allow merge from feature/* and hotfix/* branches
release/v0.0.1 # for release with version v0.0.1, created from develop branch, for uat deployment, allow merge from hotfix/*
master         # for staging/pre-prod/prod deployment, allow merge from release/* and hotfix/* branches
```

#### Create your own branches and feature branches even for any small tasks

```bash
# The following are some usage examples, please replace the "Dong Guo" as your own name
# Create a directory for coding if you don't have one
$ cd ~
$ mkdir -p workspace/repo
$ cd workspace/repo

# Generate ssh keypair if you don't have one
$ ssh-keygen

# Add your public key to your GitLab account on http://gitlab.inc.heylinux.csky
# Configure your own git username and email

$ git config --global user.name "Dong Guo"
$ git config --global user.email "dong@heylinux.com"

# Clone the heylinux-cac repo
git clone ssh://git@gitlab.inc.heylinux.com/sre/heylinux-cac.git

# By default only one local branch "develop", three remote branches, all these branches were protected, cannot be pushed directly without a merge request
$ git status
On branch develop
Your branch is up to date with 'origin/develop'.

nothing to commit, working tree clean

$ git branch
* develop

$ git branch -a
* develop
  remotes/origin/HEAD -> origin/develop
  remotes/origin/develop
  remotes/origin/master
  remotes/origin/release/v0.0.1

Create your own branch
$ git branch dong.guo
$ git branch
* develop
  dong.guo

$ git checkout dong.guo

$ git branch
  develop
* dong.guo

# Create your own remote branch
$ git push origin dong.guo
$ git branch --set-upstream-to=origin/dong.guo dong.guo

# Pull latest codes from remote develop branch and merge to your own branch
# Switch to your own branch first, and commit your local changes before the `rebase` if you can
$ git pull
$ git rebase develop
$ git push
```

#### Commit and deploy your codes to your own remote branch

```bash
$ git status
On branch dong.guo
Your branch is up to date with 'origin/dong.guo'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   group_vars/all

no changes added to commit (use "git add" and/or "git commit -a")

$ git add .
$ git commit -m "OPS-985: Update group_vars/all"
 
$ git pull --rebase

$ git push
Enumerating objects: 7, done.
Counting objects: 100% (7/7), done.
Delta compression using up to 4 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 449 bytes | 449.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote:
remote: To create a merge request for dong.guo, visit:
remote:   http://gitlab.inc.heylinux.com/sre/heylinux-cac/merge_requests/new?merge_request%5Bsource_branch%5D=dong.guo
remote:
To ssh://gitlab.inc.heylinux.com/sre/heylinux-cac.git
   f25f80f..619c9e0  dong.guo -> dong.guo
Login the GitLab, create a merge request from your own remote branch to remote develop branch
Ask the Masters to review, comment and apply the merge request
```

#### Fix the conflicts in your own branch

```bash
## Option 1: Keep your local changes and abort the previous merge
$ git merge --abort
$ get reset --merge
$ git pull

## Option 2: Undo your local changes by sync from your own remote branch
$ git fetch --all
$ git reset --hard origin/dong.guo
$ git fetch
```

#### Remove a local branch

```
$ git checkout dong.guo
$ git branch -D feature/OPS-211
```

#### Remove a remote branch which has been deleted

```
$ git remote show origin
* remote origin
  Fetch URL: ssh://git@gitlab.inc.heylinux.com/sre/heylinux-cac.git
  Push  URL: ssh://git@gitlab.inc.heylinux.com/sre/heylinux-cac.git
  HEAD branch: develop
  Remote branches:
    dong.guo           tracked
    develop            tracked
    master             tracked
    refs/remotes/origin/feature/OPS-211 stale (use 'git remote prune' to remove)
    release/v0.0.1     tracked
  Local branches configured for 'git pull':
    dong.guo  merges with remote dong.guo
    develop   merges with remote develop
  Local refs configured for 'git push':
    dong.guo pushes to dong.guo  (up to date)
    develop  pushes to develop   (up to date)

$ git remote prune origin
Pruning origin
URL: ssh://git@gitlab.inc.heylinux.com:2022/sre/heylinux-cac.git
 * [pruned] origin/feature/OPS-211

$ git remote show origin
* remote origin
  Fetch URL: ssh://git@gitlab.inc.heylinux.com/sre/heylinux-cac.git
  Push  URL: ssh://git@gitlab.inc.heylinux.com/sre/heylinux-cac.git
  HEAD branch: develop
  Remote branches:
    dong.guo           tracked
    develop            tracked
    master             tracked
    release/v0.0.1     tracked
  Local branches configured for 'git pull':
    dong.guo  merges with remote dong.guo
    develop   merges with remote develop
  Local refs configured for 'git push':
    dong.guo  pushes to dong.guo  (up to date)
    develop   pushes to develop   (up to date)
```

#### Roll back the local and remote branch to a specific commit to resolve the conflicts

```bash
$ git fetch --all
$ git reset --hard 0035061eaca2287fcd76d02061125419e7851b3f
$ git fetch

$ git push --force
```

#### Undo your local changes from your own remote branch

```bash
# Undo group_vars/all only
$ git checkout group_vars/all

# Undo all files
$ git checkout .              
```

#### Switch to your own existing branch within a new git clone directory

```bash
$ git branch -a
* develop
  remotes/origin/HEAD -> origin/develop
  remotes/origin/dong.guo
  remotes/origin/develop
  remotes/origin/master
  remotes/origin/release/v0.0.1

$ git checkout dong.guo
Branch 'dong.guo' set up to track remote branch 'dong.guo' from 'origin'.
Switched to a new branch 'dong.guo'

$ git branch -a
* dong.guo
  develop
  remotes/origin/HEAD -> origin/develop
  remotes/origin/dong.guo
  remotes/origin/develop
  remotes/origin/master
  remotes/origin/release/v0.0.1
```

#### Check the git log

```bash
$ git log
```

#### Create an alias in your ~/.bashrc and always run it to sync the codes from develop branch latest code to your own branch

```bash
$ vim ~/.bashrc
alias brsync='git fetch origin develop:develop && git pull && git rebase develop && git push'
```
