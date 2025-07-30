---
title: GIT basic
date: 2023-11-26 21:01:57
tags: [笔记, git]
---

# Git 以及github，gitee的使用

## git简介：



> Git (/ɡɪt/) is a distributed version control system that tracks changes in any set of computer files, usually used for coordinating work among programmers who are collaboratively developing source code during software development. Its goals include speed, data integrity, and support for distributed, non-linear workflows (thousands of parallel branches running on different computers).

## **What is Git?**

Git is a popular version control system. It was created by Linus Torvalds in 2005, and has been maintained by Junio Hamano since then.

It is used for:

Tracking code changes
Tracking who made changes
Coding collaboration

## **What does Git do?**

Manage projects with **Repositories**
**Clone** a project to work on a local copy
**Control and track** changes with **Staging and Committing**
**Branch and Merge** to allow for work on different parts and versions of a project
**Pull** the latest version of the project to a local copy
**Push** local updates to the main project

## **Working with Git**

Initialize Git on a folder, making it a Repository
Git now creates a hidden folder to keep track of changes in that folder
When a file is changed, added or deleted, it is considered modified
You select the modified files you want to Stage
The Staged files are Committed, which prompts Git to store a permanent snapshot of the files
Git allows you to see the full history of every commit.
You can revert back to any previous commit.
Git does not store a separate copy of every file in every commit, but keeps track of changes made in each commit!
Change Platform:

Shift focus to GitHubGitHub
Shift focus to BitbucketBitbucket
Shift focus to GitLabGitLab

## **Why Git?**

Over 70% of developers use Git!
Developers can work together from anywhere in the world.
Developers can see the full history of the project.
Developers can revert to earlier versions of a project.

## **What is GitHub?**

Git is not the same as GitHub.
GitHub makes tools that use Git.
GitHub is the largest host of source code in the world, and has been owned by Microsoft since 2018.
In this tutorial, we will focus on using Git with GitHub.

**git 有图形化界面（gui）和命令行（bash），这里仅使用git命令行，即git（bash）。**

### **初始化**

#### check git version and info

```bash
git —version

```

#### configure git

```bash
git config --global user.name "xxxxx"		# global means all the repos are in charge
git config --global user.email "xxxxxxxxx@gmail.com"

# if you just want to use “regen” for just once , you can remove “—global”

git config  user.name "regen"		
git config  user.email "regenissb@gmail.com"

```

#### initialize you repo 

```bash
#use mkdir and cd to create you working file . Then

git init  

# This file is initialized as a git repository from now on . You can make files.

touch hello.c



```

###  file operation

1. check file status
   Files in the repo has 2 status:
   * Tracked - files that Git knows about and are added to the repository
   * Untracked - files that are in your working directory, but not added to the repository

```bash
git status
```

​		or you can use 

```bash
git status --short
#	Note: Short status flags are:
#		?? - Untracked files
#		A - Files added to stage
#		M - Modified files
#		D - Deleted files
```







2. add to stage environment 

```bash
# once you finish a part or a bug ,you need to add it to  stage environment. So that you files in the stage environment are ready to commit .

git add hello.c

# or you can use 
git add —all
git add -A
# this two commends stages all the changes .	
```

3. move from  stage to **commit** for our repo.

* commit all the changes 

```bash
git commit -m "First release of Hello World!"
# The commit command performs a commit, and the -m "message" adds a message.
# When we commit, we should always include a message.

```

* commit all the changes without add them to stage

```bash
git commit -a -m "Updated index.html with a new line"
```

* view the history of commit log

```bash
git log
```

## **GIT branch**

​	Introduction :

> In Git, a `branch` is a new/separate version of the main repository.
>
> Branches allow you to work on different parts of a project without impacting the main branch.
>
> When the work is complete, a branch can be merged with the main project.
>
> You can even switch between branches and work on different projects without them interfering with each other.
>
> Branching in Git is very lightweight and fast!

#### create new git branch

```bash
git branch newBranch
	# add a new branch
```

#### check out the branches

```bash
git branch

	  newBtanch
	* master
# The ' * 'means that you are working on master branch.
```

#### move branch

```bash
git checkout newbranch 		# switch to new branch 'newBranch'

```

#### check status of this branch , add to stage and commit . (same code)

```bash
git status
git add --all
git commit -m "new changes in branch newBranch"
```

#### merge branch 

1. In order to merge two branches, we need to change to the master branch:

```bash
git checkout master
```

2. merge

```bash
git merge newBranch
```

3. delete branches

```bash
git branch -d newBranch
```

* merge conflict

## **其他操作**

* GIT Associating a local repository to a remote repository

```bash
git remote add origin git@github.com:regen/test.git
```

```bash

git push -u origin master
```
## 使用案例

### 	多个设备同一账号，在不同的场景使用github同步代码。

#### 终端:

##### 初始化设置

```bash
# 设置用户
git config --global user.name "xxxxx"		# global means all the repos are in charge
git config --global user.email "xxxxxxxxx@gmail.com"
```

##### github添加ssh公钥匙

```bash
ssh-keygen -t rsa -C "xxxxxxxxx@gmail.com"
```

将生成的rsq_pub中的内容复制到github里的ssh栏

##### 开始

* 复制仓库

```bash
git clone git@github.com:regenm/Notes.git
```

* 修改前更新

```bash
git pull origin master
```

* 修改后上传

```bash
git add -A
git commit -m "add an application of git"
git status
git push
```

