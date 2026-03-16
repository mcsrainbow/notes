
Git Notes
---

```ini
# configure global git user info
## ~/.gitconfig
[user]
    name = mcsrainbow
    email = guosuiyu@gmail.com
[includeIf "gitdir:~/SwireProperties/"]
    path = .gitconfig-swireproperties

## ~/.gitconfig-swireproperties
[user]
    name = Damon Guo
    email = damonguo@swireproperties.com
```

```bash
# show diff
git show                           # show the latest commit (the current HEAD)
git show HEAD~2                    # show the commit 2 steps before HEAD (the 3rd most recent commit)
git diff HEAD~4 HEAD~2             # compare file changes between the 5th and 3rd most recent commits

git log --oneline                  # compact commit list
git show e5c3e6572                 # show details of a specific commit
git diff 41526f75e e5c3e6572       # diff between two commits

# show changes staged for next commit
git diff --staged

# change message of last local commit not pushed
git commit --amend

# safely undo the latest commit without rewriting history
git revert HEAD

# abort rebase and discard local changes to match remote
git rebase --abort                 # stop an in-progress rebase safely
git fetch origin                   # update remote tracking branches
git reset --hard origin/main       # forcefully align to remote main

# magic time machine
# see a list of every thing have done in git, across all branches
# each one has an index HEAD@{index}
# find the one before you broke everything
git reflog --oneline               # view all HEAD movements (branch switches, resets)
# reset without --hard keeps accumulated changes in working directory after the selected reflog state
git reset HEAD@{index}             # move HEAD back to a previous state
# push with --force-with-lease checks that the remote branch has not changed since last fetch before overwriting
git push --force-with-lease        # safer than --force, avoids overwriting others' work

# delete a branch
git branch -a
git switch develop
git branch -d feature/init         # a. delete the local branch only if the current branch already contains all its commits
git branch -D feature/init         # b. force delete the local branch even if the current branch does not contain its commits
git push origin -d feature/init    # delete the remote branch with the same name

# create a new branch env/ask-prod from local env/ask-uat
git checkout env/ask-uat           # switch to local branch env/ask-uat
git pull origin env/ask-uat        # update local branch env/ask-uat to latest remote
git checkout -b env/ask-prod       # create and switch to new branch env/ask-prod from local env/ask-uat
git push -u origin env/ask-prod    # push the new branch to remote and set upstream tracking

# create a new branch env/ask-prod from remote env/ask-uat
git fetch origin env/ask-uat                   # make sure remote env/ask-uat is updated
git switch -c env/ask-prod origin/env/ask-uat  # create new branch env/ask-prod from remote env/ask-uat
git push -u origin env/ask-prod                # push the new branch to remote and set upstream tracking

# rename branch ask-prod to env/ask-prod
git branch -m ask-prod env/ask-prod            # rename the local branch from ask-prod to env/ask-prod
git push origin -u env/ask-prod                # push the renamed branch to remote and set upstream tracking
git push origin -d ask-prod                    # remove the old remote branch ask-prod after confirming it's no longer in use

# git graph
git log --graph --all --oneline --decorate
```

```bash
# show commits that exist in origin/main but not in origin/develop
git log origin/develop..origin/main --oneline

# create a feature branch from a commit compatible with both main and develop
git fetch --all --prune                         # fetch all remotes and clean stale branches
git checkout develop                            # create/switch to local develop branch
git checkout main                               # create/switch to local main branch
lca_commit=$(git merge-base main develop)       # find the latest common ancestor commit shared by main and develop
git log --oneline | grep -C20 $lca_commit       # show the commits around latest_common_ancestor_commit
git checkout -b feat/security-scan $lca_commit  # create and switch to a new branch feat/security-scan based on latest_common_ancestor_commit

# show commits in the current branch that do not exist in origin/main
git fetch origin develop:develop     # update local develop branch
git fetch origin main:main           # update local main branch
git log origin/main..HEAD --oneline  # show commits in the current branch that do not exist in origin/main
git diff origin/main HEAD            # diff the current branch and origin/main

# merge back
# merge latest develop into current branch safely
git fetch origin develop:develop      # fetch latest develop branch from remote
git diff HEAD..origin/develop         # show differences between current branch and develop
git merge --no-commit origin/develop  # merge develop without committing yet
git commit                            # a. commit if everything looks good
git merge --abort                     # b. abort merge if needed

# find troublemaker
git log --follow --find-renames -- path/to/file
git show $(git log -1 --format="%H" --follow --find-renames -- path/to/file)
```