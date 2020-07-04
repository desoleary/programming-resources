# Git Process Document 

#### Resources 
- [Git power tools for daily use](https://nvie.com/posts/git-power-tools/)
- [Gitflow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)

#### Typical Feature Git Workflow

###### Step 1. Fetch and check out the branch for this merge request

```
git fetch origin
git checkout -b TLD-20-cleanup-rubocop origin/TLD-20-cleanup-rubocop
```


###### Step 2. Review the changes locally

```
git fetch origin
git checkout origin/development
git merge --no-ff TLD-20-cleanup-rubocop
git push origin development
```

###### Step 3. Merge the branch and fix any conflicts that come up

```
git fetch origin
git checkout origin/development
git merge --no-ff TLD-20-cleanup-rubocop
```


#### [Creating a feature branch](https://nvie.com/posts/a-successful-git-branching-model/#creating-a-feature-branch) 

```shell script
$ git checkout -b myfeature remote-branch
# Switched to a new branch "myfeature"
```

#### [Incorporating a finished feature on development](https://nvie.com/posts/a-successful-git-branching-model/#incorporating-a-finished-feature-on-develop)

> this is added for demonstrated purposes only as this will be handled by **GitHub** when merging your feature branch

```shell script
$ git checkout development
# Switched to branch 'development'
$ git merge --no-ff myfeature
# Updating ea1b82a..05e9557
# (Summary of changes)
$ git branch -d myfeature
# Deleted branch myfeature (was 05e9557).
$ git push origin development
```

#### [Release branches](https://nvie.com/posts/a-successful-git-branching-model/#release-branches)

-   May branch off from:
    - development
-   Must merge back into:
    - development and master
-   Branch naming convention:
    - release-*

```shell script
$ git checkout -b release-1.2 development
# Switched to a new branch "release-1.2"
$ ./bump-version.sh 1.2
# Files modified successfully, version bumped to 1.2.
$ git commit -a -m "Bumped version number to 1.2"
# [release-1.2 74d9424] Bumped version number to 1.2
# 1 files changed, 1 insertions(+), 1 deletions(-)
```

###### [Finishing a release branch](https://nvie.com/posts/a-successful-git-branching-model/#finishing-a-release-branch)

```shell script
$ git checkout master
# Switched to branch 'master'
$ git merge --no-ff release-1.2
# Merge made by recursive.
# (Summary of changes)
$ git tag -a 1.2
```

```shell script
$ git checkout development
# Switched to branch 'development'
$ git merge --no-ff release-1.2
# Merge made by recursive.
# (Summary of changes)
```

#### [Hotfix branches](https://nvie.com/posts/a-successful-git-branching-model/#hotfix-branches)

-   May branch off from:
    - master
-   Must merge back into:
    - develop and master
-   Branch naming convention:
    - hotfix-*
    
###### Creating the hotfix branch

```shell script
$ git checkout -b hotfix-1.2.1 master
# Switched to a new branch "hotfix-1.2.1"
$ ./bump-version.sh 1.2.1
# Files modified successfully, version bumped to 1.2.1.
$ git commit -a -m "Bumped version number to 1.2.1"
# [hotfix-1.2.1 41e61bb] Bumped version number to 1.2.1
# 1 files changed, 1 insertions(+), 1 deletions(-)
```

###### [Finishing a hotfix branch](https://nvie.com/posts/a-successful-git-branching-model/#finishing-a-hotfix-branch)

```shell script
$ git checkout master
# Switched to branch 'master'
$ git merge --no-ff hotfix-1.2.1
# Merge made by recursive.
# (Summary of changes)
$ git tag -a 1.2.1
```

```shell script
$ git checkout development
# Switched to branch 'development'
$ git merge --no-ff hotfix-1.2.1
# Merge made by recursive.
# (Summary of changes)
```

```shell script
git branch -d hotfix-1.2.1
```

