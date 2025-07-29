
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
git show HEAD

git diff HEAD~2 HEAD~3

# change a commit message
git commit --amend

# magic time machine
git reflog
# you will see a list of every thing you've done in git, across all branches!
# each one has an index HEAD@{index}
# find the one before you broke everything
git reset HEAD@{index}
git push --force

git diff --staged

# create a new branch from develop branch
git branch -a
git checkout develop
git pull origin develop

git checkout -b env/ask-prod
git push -u origin env/ask-prod

# delete a branch
git branch -d env/ask-uat

# change branch name
git branch -m env/ask-prod

# create a new branch from env/ask-uat branch
git checkout env/ask-uat
git pull origin env/ask-uat

git checkout -b env/ask-prod
git push -u origin env/ask-prod

# git graph
git log --graph --all --oneline
```

```bash
git checkout develop
git pull origin develop

git checkout -b feat/security-rasp-agent

git add .
git commit -m "feat(security): update rasp agent with official latest url"

git push origin feat/security-rasp-agent
```
