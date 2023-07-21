# git入门


## git入门

### 介绍

Git 是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

### windows中安装

[各操作系统版本下载](https://git-scm.com/downloads)

安装后在开始菜单里找到"Git"->"Git Bash"，会弹出 Git 命令窗口，你可以在该窗口进行 Git 操作。

创建工作目录，比如在D盘根目录下创建blog

```bash
mkdir -p blog
```

### git初始化

默认会生成.git文件夹

```shell
git init
```

#### 用户信息

配置个人的用户名称和电子邮件地址：

```bash
git config --global user.name "smith"
git config --global user.email smith@gmail.com
```

#### 查看配置信息

```bash
git config --list
```

也可以在配置文件中看到

```bash
vim ~/.gitconfig 
[http]
    postBuffer = 2M
[user]
    name = smith
    email = smith@gmail.com
```

### git 工作流程

git-process.png

#### 创建`readme.txt`文件

```bash
Git is a version control system.
Git is free software.
```

#### 把文件提交到暂存区

```bash
git add readme.txt
git commit -m "first push"
```

`-m`:后面输入本次提交的说明，可以输入任何内容。

### 查看提交日志

```bash
$ git log 
commit 934e87838ae19b396a9db3779f5722be53701f22 (HEAD -> master)
Author: smith <smith@gmail.com>
Date:   Mon May 9 09:17:05 2022 +0800

    thired version

commit 7580fbdc4ddfd4cf740cba51e1661b020806638c
Author: smith <smith@gmail.com>
Date:   Mon May 9 09:16:18 2022 +0800

    second version

commit 48eca9171d496942470fbcb5b93500bf593c3fdf
Author: smith <smith@gmail.com>
Date:   Mon May 9 09:12:21 2022 +0800

    first push
```

精简输出加上`--pretty=oneline`

```bash
$ git log --pretty=oneline
934e87838ae19b396a9db3779f5722be53701f22 (HEAD -> master) thired version
7580fbdc4ddfd4cf740cba51e1661b020806638c second version
48eca9171d496942470fbcb5b93500bf593c3fdf first push
```

`commit`后面的id是根据`sha1`算法得来的。

各个版本中readme.txt内容

```bash
版本一
Git is a version control system.
Git is free software.
版本二
Git is a version control system.
Git is free software.
second version
版本三
Git is a version control system.
Git is free software.
thired version
```

### 版本回退

通过`git log`查看最新一次提交id为`934e87838ae19b396a9db3779f5722be53701f22`。git中用HEAD表示当前版本。上一个版本用`HEAD^'`表示，上上版本用`HEAD^^`表示。往上100个版本用`HEAD~100`表示。现在回到上一个版本。

```bash
$ git reset --hard HEAD^
HEAD is now at 7580fbd second version
```

查看readme.txt文件内容。果然回到上一个版本了。

#### 查看当前版本库状态

```bash
$ git log
commit 7580fbdc4ddfd4cf740cba51e1661b020806638c (HEAD -> master)
Author: smith <smith@gmail.com>
Date:   Mon May 9 09:16:18 2022 +0800

    second version

commit 48eca9171d496942470fbcb5b93500bf593c3fdf
Author: smith <smith@gmail.com>
Date:   Mon May 9 09:12:21 2022 +0800

    first push
```

发现之前的版本不见了，还可以通过commit id 回到之前的版本。id不用写全，至少写6位。

```bash
$ git reset --hard 934e87
HEAD is now at 934e878 thired version
```

再次查看readme.txt文件果然又回来了。

Git的版本回退速度非常快，因为Git在内部有个指向当前版本的`HEAD`指针，当你回退版本的时候，Git仅仅是把HEAD从指向`thired version`.

如果还想回退到最新版本而又忘记了commit id ，Git提供了一个命令`git reflog`用来记录你的每一次命令。

```bash
$ git reflog
934e878 (HEAD -> master) HEAD@{0}: reset: moving to 934e87
7580fbd HEAD@{1}: reset: moving to HEAD^
934e878 (HEAD -> master) HEAD@{2}: commit: thired version
7580fbd HEAD@{3}: commit: second version
48eca91 HEAD@{4}: commit (initial): first push
```

然后通过commit  id回到最新版本。

### 总结

- `HEAD`指向的版本就是当前版本，因此，Git允许我们在版本的历史之间穿梭，使用命令`git reset --hard commit_id`。
- 穿梭前，用`git log`可以查看提交历史，以便确定要回退到哪个版本。
- 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。

### 工作区和暂存区

#### 工作区

在笔记本中创建的blog文件夹就是工作区。

#### 暂存区

#### 版本库（Repository）

工作区有一个隐藏目录`.git`，这个不算工作区，而是Git的版本库。Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

把文件向git版本库里添加时，分两步执行：

- 用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区
- 用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支。

因为我们创建Git版本库时，Git自动为我们创建了唯一一个`master`分支，所以，现在，`git commit`就是往`master`分支上提交更改。

需要提交的文件修改通通放到暂存区，然后，一次性提交暂存区的所有修改。

测试：

修改readme.txt文件。提交到暂存区，查看当前状态，最后提交到当前分支。

```bash
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   readme.txt
$ git commit -m "change sth"
[master 410b2e7] change sth
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git status
On branch master
nothing to commit, working tree clean
```

一旦提交后，如果你又没有对工作区做任何修改，那么工作区就是“干净”的：

```bash
$ git status
On branch master
nothing to commit, working tree clean
```

### 管理修改

测试过程：第一次修改 -> `git add` -> 第二次修改 -> `git commit`

修改readme.txt文件

第一次修改并提交到缓存区

```bash
$ vi readme.txt
Git is a version control system.
Git is free software.
with add
$ git add readme.txt
```

第二次修改直接使用commit 提交

```bash
$ with readme.txt
Git is a version control system.
Git is free software.
no add
$ git commit -m "change sth"
[master 729bf9e] change sth
 1 file changed, 1 insertion(+)
```

最后查看状态，发现第二次修改没被提交。

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

#### 总结

Git管理的是修改，当你用`git add`命令后，在工作区的第一次修改被放入暂存区，准备提交，但是，在工作区的第二次修改并没有放入暂存区，所以，`git commit`只负责把暂存区的修改提交了，也就是第一次的修改被提交了，第二次的修改不会被提交。

提交后，用`git diff HEAD -- readme.txt`命令可以查看工作区和版本库里面最新版本的区别：

```bash
$ git diff HEAD -- readme.txt
diff --git a/readme.txt b/readme.txt
index 23faa4f..199308a 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,3 +1,3 @@
 Git is a version control system.
 Git is free software.
-with add
+no add
```

那怎么提交第二次修改呢？你可以继续`git add`再`git commit`，也可以别着急提交第一次修改，先`git add`第二次修改，再`git commit`，就相当于把两次修改合并后一块提交了：

第一次修改 -> `git add` -> 第二次修改 -> `git add` -> `git commit`

都操作完后查看状态。

```bash
$ git status
On branch master
nothing to commit, working tree clean
```

### 撤销更改

修改`readme.txt`文件-->git add(提交到暂存区，没有commit) -->发现文件修改有错误-->git reset HEAD file(把暂存区的修改回退到工作区) -->git checkout -- file(丢弃工作区的修改)-->git status (查看状态)

`vi readme.txt`

```bash
Git is a version control system.
Git is free software.
no add
add sthssssss
```

提交到暂存区，没有commit

```bash
$ git add readme.txt
```

查看状态。有修改。

```bash
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   readme.txt
```

把暂存区的修改回退到工作区

```bash
$ git reset HEAD readme.txt
Unstaged changes after reset:
M       readme.txt
```

查看状态。已撤回。

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

丢弃工作区的修改

```bash
$ git checkout -- readme.txt
```

查看状态。

```bash
$ git status
On branch master
nothing to commit, working tree clean
```

文件已还原

```bash
$ cat readme.txt
Git is a version control system.
Git is free software.
no add
```

#### 总结

- 提交到暂存区的文件可以用`git reset HEAD file`命令把文件退回到工作区。
- 退回到工作区后用`git checkout -- file`命令丢弃工作区修改。
- 可以用`git status`查看状态。

### 删除文件

文件提交后直接把文件删除可以在版本库中找到并恢复，如果从来没提交过，那文件是无法恢复的。

实验：创建`test.txt`文件并提交到版本库，工作区删除文件。并恢复。

```bash
git add .
git commit -m 'test'
rm test.txt
```

查看状态

```bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

恢复文件

```bash
$ git checkout -- test.txt
```

`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

如果确实不想用了版本区可以删除文件，这样就永久删除了。

```bash
git rm test.txt
```

### 远程仓库

需求：

将本地仓和远程仓数据同步，还可以多人协同。

本地仓：笔记本

远程仓：github 

1.github上创建新仓库

2.将远程仓库克隆到本地

```yml
$ git remote add origin git@github.com:github用户名/仓库名.git
```

3.把本地库所有内容推送到远程库

```bash
$ git push -u origin master
```

把本地库的内容推送到远程，用`git push`命令，实际上是把当前分支`master`推送到远程。由于远程库是空的，我们第一次推送`master`分支时，加上了`-u`参数，Git不但会把本地的`master`分支内容推送的远程新的`master`分支，还会把本地的`master`分支和远程的`master`分支关联起来，在以后的推送或者拉取时就可以简化命令。

推送成功后，可以立刻在GitHub页面中看到远程库的内容已经和本地一模一样。

后面再次推送可以使用简化命令

```bash
$ git push origin master
```

#### 删除远程库

如果添加的时候地址写错了，或者就是想删除远程库，可以用`git remote rm <name>`命令。使用前，建议先用`git remote -v`查看远程库信息。

```yml
$ git remote -v
origin  git@github.com:michaelliao/learn-git.git (fetch)
origin  git@github.com:michaelliao/learn-git.git (push)
```

然后，根据名字删除，比如删除`origin`：

```
$ git remote rm origin
```

此处的“删除”其实是解除了本地和远程的绑定关系，并不是物理上删除了远程库。远程库本身并没有任何改动。要真正删除远程库，需要登录到GitHub，在后台页面找到删除按钮再删除。

总结

要关联一个远程库，使用命令`git remote add origin git@server-name:path/repo-name.git`；

关联一个远程库时必须给远程库指定一个名字，`origin`是默认习惯命名；

关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；

此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改。

#### 从远程库克隆

克隆一个本地库

```yaml
$ git clone git@github.com:github用户名/仓库名.git
```

本地查看有了。

有几种方式克隆仓库到本地

- https :https://github.com/github用户名/仓库名.git
- ssh : git@github.com:github用户名/仓库名.git
- github cli : gh repo clone github用户名/仓库名

推荐使用ssh方式，速度更快。

### 分支管理

创建分支dev

```yml
$ git checkout -b dev
Switched to a new branch 'dev'
```

查看当前分支，星号代表当前分支

```yml
$ git branch -a
* dev
  master
```

写入内容

```bash
$ vi readme.txt
Git is a version control system.
Git is free software.
dev branch add sth
```

上传到暂存区，并推送到dev分支。

```yml
$ git add .
$ git commit -m "push dev branch"
[dev d51e5f2] push dev branch
 1 file changed, 1 insertion(+)
```

切换到master主分支，查看readme.txt文件发现最后一行不见了。git中各分支之间互不影响。

```bash
git checkout master
```

将dev分支和master主分支合并。`merge`后面是要合并的分支名。

```yml
$ git merge dev
Updating 8e3076e..d51e5f2
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

切换到master分支

```bash
git checkout master
```

再次查看文件内容，显示了。

```bash
$ cat readme.txt
Git is a version control system.
Git is free software.
dev branch add sth
```

#### 删除dev分支

```yml
git branch -d dev
```

#### 总结

Git鼓励大量使用分支：

查看分支：`git branch`

创建分支：`git branch <name>`

切换分支：`git checkout <name>`或者`git switch <name>`

创建+切换分支：`git checkout -b <name>`或者`git switch -c <name>`

合并某分支到当前分支：`git merge <name>`

删除分支：`git branch -d <name>`

新版本git 支持switch来切换分支。更加容易区分。

### 分支管理策略

通常，合并分支时，如果可能，Git会用`Fast forward`模式，但这种模式下，删除分支后，会丢掉分支信息。

如果要强制禁用`Fast forward`模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。

下面我们实战一下`--no-ff`方式的`git merge`：

首先，仍然创建并切换`dev`分支：

```bash
$ git switch -c dev
Switched to a new branch 'dev'
```

修改readme.txt文件，并提交一个新的commit：

```bash
$ git add readme.txt 
$ git commit -m "add merge"
[dev f52c633] add merge
 1 file changed, 1 insertion(+)
```

现在，我们切换回`master`：

```
$ git switch master
Switched to branch 'master'
```

准备合并`dev`分支，请注意`--no-ff`参数，表示禁用`Fast forward`：

```
$ git merge --no-ff -m "merge with no-ff" dev
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

因为本次合并要创建一个新的commit，所以加上`-m`参数，把commit描述写进去。

合并后，我们用`git log`看看分支历史：

```bash
$ git log --graph --pretty=oneline --abbrev-commit
*   f116cb6 (HEAD -> master) merge with no-ff
|\
| * 98d73ce (dev) add merge
|/
* d51e5f2 push dev branch
* 8e3076e init
* 3f097bb dev branch commit
* 601bf08 del sth
```

#### 分支策略

- `master`分支应该是非常稳定的，也就是仅用来发布新版本
- `dev`分支是不稳定的，到某个时候，比如1.0版本发布时，再把`dev`分支合并到`master`上，在`master`分支发布1.0版本。
- 你和你的小伙伴们每个人都在`dev`分支上干活，每个人都有自己的分支，时不时地往`dev`分支上合并就可以了。

合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并。

#### Bug分支

发现master分支有Bug需要修复，此时dev上的工作还没提交到暂存区。可以用`git stash`命令将当前工作现场保存起来，后面修复完Bug后在还原回来。

```bash
$ git status
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

```yml
$ git stash
Saved working directory and index state WIP on dev: 7f2ad74 init status
$ git status
On branch dev
nothing to commit, working tree clean
```

首先确定要在哪个分支上修复bug，假定需要在`master`分支上修复，就从`master`创建临时分支：

```yaml
$ git checkout master
Switched to branch 'master'
```

创建新分支`issue-102`

```yml
$ git checkout -b issue-102
Switched to a new branch 'issue-102'
```

修复Bug（修改readme.txt文件内容），然后提交。

```bash
$ git add readme.txt
$ git commit -m "fix bug 102"
[issue-102 858cab6] fix bug 102
 1 file changed, 1 insertion(+)
```

修复完成后，切换到`master`分支，并完成合并，最后删除`issue-102`分支：

```bash
$ git switch master
Switched to branch 'master'
$ git merge --no-ff -m "merged bug fix 102" issue-102
Merge made by the 'ort' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

现在回到dev分支还原之前的工作状态

```bash
$ git switch dev
Switched to branch 'dev'
```

查看之前存储的数据

```bash
$ git stash list
stash@{0}: WIP on dev: 7f2ad74 init status
```

两种方法恢复之前的工作内容

- 用`git stash apply`恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除
- 用`git stash pop`，恢复的同时把stash内容也删了

```bash
$ git stash pop
On branch dev
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (6228502b0116dba3633cdb19b738aba9bd6d4664)
```

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：

```yml
$ git stash apply stash@{0}
```

在master分支上修复了bug后，我们要想一想，dev分支是早期从master分支分出来的，所以，这个bug其实在当前dev分支上也存在。

先切换到master分支上查看之前“ fix bug 102” 那条`commit id`

```bash
$ git switch master #也可以用git switch -来切换。
$ git log --graph
commit 858cab6c5238646df8d353bfd83382a92a432fcd (issue-102)
Author: smith <smith@gmail.com>
Date:   Tue May 10 13:18:18 2022 +0800

    fix bug 102
```

Git专门提供了一个`cherry-pick`命令，让我们能复制一个特定的提交到当前分支。再次切换到dev分支，复制master分支上“fix bug 102”这条改动提交。

```yml

$ git branch
* dev
  master
$ git cherry-pick 858cab
[dev 0996ec3] fix bug 102
 Date: Tue May 10 13:18:18 2022 +0800
 1 file changed, 1 insertion(+)
```

Git自动给dev分支做了一次提交，注意这次提交的commit是`0996ec3`，它并不同于master的`858cab`，因为这两个commit只是改动相同，但确实是两个不同的commit。用`git cherry-pick`，我们就不需要在dev分支上手动再把修bug的过程重复一遍。

##### 总结

修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；

当手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug，修复后，再`git stash pop`，回到工作现场；

在master分支上修复的bug，想要合并到当前dev分支，可以用`git cherry-pick <commit>`命令，把bug提交的修改“复制”到当前分支，避免重复劳动。







### 参考

[1](https://www.liaoxuefeng.com/wiki/896043488029600/900375748016320)




















