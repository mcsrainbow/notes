
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
git show HEAD                      # show the latest commit (the current HEAD)
git show HEAD~2                    # show the commit 2 steps before HEAD (the 3rd most recent commit)
git diff HEAD~4 HEAD~2             # compare file changes between the 5th and 3rd most recent commits

git log --oneline                  # compact commit list
git show e5c3e6572                 # show details of a specific commit
git diff 41526f75e e5c3e6572       # diff between two commits

# show changes staged for next commit
git diff --staged

# change message of last local commit not pushed
git commit --amend

# undo last local commit not pushed and keep changes
git reset HEAD~1                   # mixed reset (keeps changes unstaged)

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
git branch -d env/ask-uat          # delete the local branch only if the current branch already contains all its commits
git branch -D env/ask-uat          # force delete the local branch even if the current branch does not contain its commits
git push origin -d env/ask-uat     # delete the remote branch with the same name

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
# create a new feature branch from develop and push changes to remote
git checkout develop
git pull origin develop

git checkout -b feat/security-rasp-agent

git add .
git commit -m "feat(security): update rasp agent with official latest url"

git push origin feat/security-rasp-agent
```
