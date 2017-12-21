---
title: Git 常规操作
date: 2017-12-01 20:32:22
thumbnailImage: http://7xvi3w.com1.z0.glb.clouddn.com/git0.png
thumbnailImagePosition: left
tags: 
- git
comments: false
metaAlignment: center
categories: 技术博文 
---
不得不说 Git 改变了我们的编程习惯，随时版本备份百利而无一害。我们不能只停留在熟练使用 `add`,`pull`,`push` 等基本命令的层次，是时候扩展技能了。
<!-- more -->
#### 一、Config
先 `git config --help` 打印出使用说明：
```bash
NAME
  git-config - Get and set repository or global options

SYNOPSIS
  git config [<file-option>] [type] [--show-origin] [-z|--null] name [value [value_regex]]
  git config [<file-option>] [type] --add name value
  git config [<file-option>] [type] --replace-all name value [value_regex]
  git config [<file-option>] [type] [--show-origin] [-z|--null] --get name [value_regex]
  git config [<file-option>] [type] [--show-origin] [-z|--null] --get-all name [value_regex]
  git config [<file-option>] [type] [--show-origin] [-z|--null] [--name-only] --get-regexp name_regex [value_regex]
  git config [<file-option>] [type] [-z|--null] --get-urlmatch name URL
  git config [<file-option>] --unset name [value_regex]
  git config [<file-option>] --unset-all name [value_regex]
  git config [<file-option>] --rename-section old_name new_name
  git config [<file-option>] --remove-section name
  git config [<file-option>] [--show-origin] [-z|--null] [--name-only] -l | --list
  git config [<file-option>] --get-color name [default]
  git config [<file-option>] --get-colorbool name [stdout-is-tty]
  git config [<file-option>] -e | --edit
```
可以看到，`config` 也有很多用法，我们只选一些常见的操作来说明：
```
# 取消全局 用户名/邮箱 配置
$ git config --global --unset user.name
$ git config --global --unset user.email

# 进入项目文件夹，单独设置每个仓库用户名/邮箱
$ git config user.email "xxxx@xx.com"
$ git config user.name "xxxx"

# 查看某个仓库下的配置文件
git config -l

# 查看全局配置
git config --global -l
```

#### 二、Stash
我们先来看看 `git stash --help` 的使用帮助：
```bash
NAME
  git-stash - Stash the changes in a dirty working directory away

SYNOPSIS
  git stash list [<options>]
  git stash show [<stash>]
  git stash drop [-q|--quiet] [<stash>]
  git stash ( pop | apply ) [--index] [-q|--quiet] [<stash>]
  git stash branch <branchname> [<stash>]
  git stash [save [-p|--patch] [-k|--[no-]keep-index] [-q|--quiet]
              [-u|--include-untracked] [-a|--all] [<message>]]
  git stash clear
  git stash create [<message>]
  git stash store [-m|--message <message>] [-q|--quiet] <commit>


DESCRIPTION
  Use git stash when you want to record the current state of the working directory and the index, but want to go back to a clean working directory. The
  command saves your local modifications away and reverts the working directory to match the HEAD commit.
```
上述解释很清楚了，我们来看看一些应用场景：
`git stash` 可用来暂存当前正在进行的工作， 比如想 `pull` 最新代码， 又不想加新 `commit`， 或者另外一种情况，为了 `fix` 一个紧急的 `bug`,  先 `stash`, 使返回到自己上一个 `commit`, 改完 `bug` 之后再 `stash pop`, 继续原来的工作。

基本用法如下：
```bash
git stash

...
do some work
...

git stash pop
```

当然，我们可以为每次 `stash` 加上别名：
```bash
git stash save "fix jartto's blog bugs"
```

多次使用 `git stash` 命令后，栈里将充满了未提交的代码，这时候我们可以使用 `list` 命令来选择应用回来的版本：
```bash
git stash list
```

确定好要应用回来的版本，找到对应版本号，使用：
```bash
git stash apply stash@{1}
```
这样就可以将你指定版本号为 `stash@{1}` 的工作取出来。

既然是 `list` ，那可能会用到删除某次的暂存，请慎用：
```bash
git stash drop stash@{1}
```

嗯，很好，当所有暂存版本都应用后，我们可以选择是否清除 `stash`，命令如下：
```bash
git stash clear
```

了解更多，请参考：
[git stash 和 git stash pop](http://blog.csdn.net/wh_19910525/article/details/7784901)
[git stash](https://git-scm.com/docs/git-stash)

#### 三、Remote
我们先来看看 `git remote --help` 的使用帮助：
```bash
NAME
  git-remote - Manage set of tracked repositories

SYNOPSIS
  git remote [-v | --verbose]
  git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=<fetch|push>] <name> <url>
  git remote rename <old> <new>
  git remote remove <name>
  git remote set-head <name> (-a | --auto | -d | --delete | <branch>)
  git remote set-branches [--add] <name> <branch>...
  git remote get-url [--push] [--all] <name>
  git remote set-url [--push] <name> <newurl> [<oldurl>]
  git remote set-url --add [--push] <name> <newurl>
  git remote set-url --delete [--push] <name> <url>
  git remote [-v | --verbose] show [-n] <name>...
  git remote prune [-n | --dry-run] <name>...
  git remote [-v | --verbose] update [-p | --prune] [(<group> | <remote>)...]
```
同样，我们挑几个常用命令来说明：
1.`git` 显示远程分支：
```bash
git remote show origin
```

2.`git` 删除远程分支：
```bash
git push origin --delete jartto-hotfix
```

3.添加源：
```bash
git remote add  origin git@192.168.1.1:test/jartto-blog.git
```

4.修改源地址：
```bash
git remote set-url origin git@jartto.wang:test/jartto-blog.git
```

5.删除源
```bash
# 查看源列表
git remote
git remote rm origin
```

#### 四、Cherry-pick
先看看 `Git` 怎么定义 `cherry-pick` 命令的：
{% alert info %}
Given one or more existing commits, apply the change each one introduces, recording a new commit for each. This requires your working tree to be clean (no modifications from the HEAD commit).
{% endalert %}

When it is not obvious how to apply a change, the following happens:
1. The current branch and HEAD pointer stay at the last commit successfully made.
2. The CHERRY_PICK_HEAD ref is set to point at the commit that introduced the change that is difficult to apply.
3. Paths in which the change applied cleanly are updated both in the index file and in your working tree.
4. For conflicting paths, the index file records up to three versions, as described in the "TRUE MERGE" section of git-merge(1). The working tree files will include a description of the conflict bracketed by the usual conflict markers <<<<<<< and >>>>>>>.
5. No other modifications are made.

```bash
NAME
  git-cherry-pick - Apply the changes introduced by some existing commits

SYNOPSIS
  git cherry-pick [--edit] [-n] [-m parent-number] [-s] [-x] [--ff]
                    [-S[<keyid>]] <commit>...
  git cherry-pick --continue
  git cherry-pick --quit
  git cherry-pick --abort

OPTIONS
# Commits to cherry-pick. For a more complete list of ways to spell commits, see gitrevisions(7). Sets of commits can be passed but no traversal is done by default, as if the --no-walk option was specified, see git-rev-list(1). Note that specifying a range will feed all <commit>... arguments to a single revision walk (see a later example that uses maint master..next).
<commit>...
      
# With this option, git cherry-pick will let you edit the commit message prior to committing.
-e, --edit

# When recording the commit, append a line that says "(cherry picked from commit ...)" to the original commit message in order to indicate which commit this change was cherry-picked from. This is done only for cherry picks without conflicts. Do not use this option if you are cherry-picking from your private branch because the information is useless to the recipient. If on the other hand you are cherry-picking between two publicly visible branches (e.g. backporting a fix to a maintenance branch for an older release from a development branch), adding this information can be useful.    
-x

# It used to be that the command defaulted to do -x described above, and -r was to disable it. Now the default is not to do -x so this option is a no-op.
-r

# Usually you cannot cherry-pick a merge because you do not know which side of the merge should be considered the mainline. This option specifies the parent number (starting from 1) of the mainline and allows cherry-pick to replay the change relative to the specified parent.
-m parent-number, --mainline parent-number
      
# Usually the command automatically creates a sequence of commits. This flag applies the changes necessary to cherry-pick each named commit to your working tree and the index, without making any commit. In addition, when this option is used, your index does not have to match the HEAD commit. The cherry-pick is done against the beginning state of your index. This is useful when cherry-picking more than one commits effect to your index in a row.
-n, --no-commit
      
# Add Signed-off-by line at the end of the commit message. See the signoff option in git-commit(1) for more information.
-s, --signoff
      
# GPG-sign commits. The keyid argument is optional and defaults to the committer identity; if specified, it must be stuck to the option without a space.
-S[<keyid>], --gpg-sign[=<keyid>]
      
# If the current HEAD is the same as the parent of the cherry-picked commit, then a fast forward to this commit will be performed.
--ff
      
# By default, cherry-picking an empty commit will fail, indicating that an explicit invocation of git commit --allow-empty is required. This option overrides that behavior, allowing empty commits to be preserved automatically in a cherry-pick. Note that when "--ff" is in effect, empty commits that meet the "fast-forward" requirement will be kept even without this option. Note also, that use of this option only keeps commits that were initially empty (i.e. the commit recorded the same tree as its parent). Commits which are made empty due to a previous commit are dropped. To force the inclusion of those commits use --keep-redundant-commits.
--allow-empty
      
# By default, cherry-picking a commit with an empty message will fail. This option overrides that behavior, allowing commits with empty messages to be cherry picked.
--allow-empty-message
      
# If a commit being cherry picked duplicates a commit already in the current history, it will become empty. By default these redundant commits cause cherry-pick to stop so the user can examine the commit. This option overrides that behavior and creates an empty commit object. Implies --allow-empty.
--keep-redundant-commits
      
# Use the given merge strategy. Should only be used once. See the MERGE STRATEGIES section in git-merge(1) for details.
--strategy=<strategy>

# Pass the merge strategy-specific option through to the merge strategy. See git-merge(1) for details.
-X<option>, --strategy-option=<option>
      

SEQUENCER SUBCOMMANDS
# Continue the operation in progress using the information in .git/sequencer. Can be used to continue after resolving conflicts in a failed cherry-pick or revert.
--continue

# Forget about the current operation in progress. Can be used to clear the sequencer state after a failed cherry-pick or revert.
--quit
    
# Cancel the operation and return to the pre-sequence state.
--abort
```

从打印出来的帮助信息中也可以看出来 `cherry-pick` 的用法和场景比较复杂，我们先从 `git` 帮助文档中的示例下手：
```bash
# Apply the change introduced by the commit at the tip of the master branch and create a new commit with this change.
git cherry-pick master

# Apply the changes introduced by all commits that are ancestors of master but not of HEAD to produce new commits.
git cherry-pick ..master, git cherry-pick ^HEAD master
  
# Apply the changes introduced by all commits that are ancestors of maint or next, but not master or any of its ancestors. Note that the latter does not mean maint and everything between master and next; specifically, maint will not be used if it is included in master.
git cherry-pick maint next ^master, git cherry-pick maint master..next
  
# Apply the changes introduced by the fifth and third last commits pointed to by master and create 2 new commits with these changes.
git cherry-pick master~4 master~2
  
# Apply to the working tree and the index the changes introduced by the second last commit pointed to by master and by the last commit pointed to by next, but do not create any commit with these changes.
git cherry-pick -n master~1 next
  
# If history is linear and HEAD is an ancestor of next, update the working tree and advance the HEAD pointer to match next. Otherwise, apply the changes introduced by those commits that are in next but not HEAD to the current branch, creating a new commit for each new change.
git cherry-pick --ff ..next
  
# Apply the changes introduced by all commits on the master branch that touched README to the working tree and index, so the result can be inspected and made into a single new commit if suitable.
git rev-list --reverse master -- README | git cherry-pick -n --stdin
```
{% alert success %}
当执行完 `cherry-pick` 以后，将会生成一个新的提交；这个新的提交的哈希值和原来的不同，但标识名一样；
{% endalert %}
这么多说明文档，可能也很难理解，我们来列举一些实际场景：

1.项目刚上线完毕，你需要紧急更改线上 `bug`，你基于线上版本切出了 `hotfix` 版本并提交，修复了问题：
```bash
git checkout -b hotfix release
```
此时，你切回自己的开发分之，发现版本落后了，可以有两个操作：
```bash
# 方法一：拉取分支变更
git pull origin hotfix

# 方法二：在开发分支重新提交 hotfix 变更
git cherry-pick 268c2e21c4746492ead78e5a162bc444f4b4a7fc
```

2.如果你的应用已经发布了一个版本2.0, 代码分支叫 `release-2.0`, 现在正在开发 3.0, 代码的分支叫 `dev-3.0`. 那么有一天产品说, 要把正在开发的某个特性提前上线, 也就是说要把 `dev-3.0` 分支上的某些更改移到 2.x 的版本上, 那么怎么办呢?

基于 `release-2.0` 分支新建分支` release-2.1` , 并且到新创建的分支上:
```bash
git checkout -b release-2.1 release-2.0
```
将 `dev-3.0` 分支上的某些 `commit` 在 `release-2.1` 分支上重演，例如：
```bash
git cherry-pick 
20c2f506d789bb9f041050dc2c1e954fa3fb6910 
2633961a16b0dda7b767b9264662223a2874dfa9 
5d5929eafd1b03fd4e7b6aa15a6c571fbcb3ceb4
```
{% alert info %}
`cherry-pick` 会重演某些 `commit` , 即把某些 `commit` 的更改重新执行一遍。
{% endalert %}

3.`cherry-pick` 不仅可以用在不同分支之间, 还可以用在同一个分支上。比如说你在某一个向某个分支中添加了一个功能， 后来处于某种原因把它给删除了，然而后来某一天你又要添加上这个功能了，这时候就可以使用 `cherry-pick` 把添加那个功能的 `commit`，再重演一遍。请参考：[git cherry-pick 用法](http://www.jianshu.com/p/d577dcc36a08)。

4.[知乎](https://www.zhihu.com/question/27804571)上看到了另一种场景，有一条主分支 `master`，进行过两次提交（ `m0` 和 `m1` ）。此时，新开了一个分支 `develop` 做开发，进行了三次提交（ `d0` 、`d1` 和 `d2`）。如果只想将 `d2` 这次提交合并到主分支 `master` 就会产生一个冲突，需要用户手动去编辑，问题是，如何避免冲突？

[答一](https://www.zhihu.com/question/27804571/answer/38192329)：你无法 `cherry-pick` 的原因是，你 `d2` 修改的文件，已经在 `d1`（or `d0`） 被修改过了，所以 `cherry-pick` 并不知道如何删除和增加对应的 `lines`，所以就冲突了。你唯一的办法就是解决冲突。

[答二](https://www.zhihu.com/question/27804571/answer/38167731)：`cherry-pick` 只会将一个 `commit` 引入的更改应用到当前的 `commit` 上。而你的命令：
```bash
git cherry-pick d2的哈希码
```
只会将从 `d1->d2` 的修改引入到 `m1` 中，这样当然会造成冲突。正确的做法是：
```bash
git checkout master
git cherry-pick --no-commit d0的哈希 d1的哈希 d2的哈希
git commit -m "merged commit"
```
{% alert info %}
`--no-commit` 参数是防止 `cherry-pick` 每一次应用更改都 `commit` 一次。
{% endalert %}
除此之外，还有一个命令是等效的：
```bash
git merge d2 --squash
```

#### 五、Reset
先来理解两个概念：
1.四个区
- 工作区(Working Area)
- 暂存区(Stage)
- 本地仓库(Local Repository)
- 远程仓库(Remote Repository)

2.五种状态
代码进入每一个区成功之后会产生一个状态，再加上最初始的一个状态，一共是5种状态：
- 未修改(Origin)
- 已修改(Modified)
- 已暂存(Staged)
- 已提交(Committed)
- 已推送(Pushed)

下面来说具体说明几种状态中的撤销操作：
1.已修改，未暂存
如果我们只是修改了文件，但还没有执行 `git add .`，这时候我们的文件还在工作区，并没有进入暂存区，可以用如下命令进行撤销操作：
```bash
git checkout .
```
或者执行：
```bash
git reset --hard
```
{% alert info %}
简单来说，`git add .` 的反向操作就是 `git checkout .`
{% endalert %}

2.已暂存，未提交
假如你已经执行了 `git add .`，但还没有执行 `git commit -m "comment"`。这个阶段，想要撤销，可以执行如下命令：
```bash
git reset
git checkout .
```
当然，也可以执行：
```bash
git reset --hard
```
{% alert info %}
`git reset` 只是把修改退回到了 `git add .` 之前的状态，也就是说文件本身还处于已修改未暂存状态，你如果想退回未修改状态，还需要执行 `git checkout .`。
{% endalert %}

3.已提交，未推送
你的手太快，你既执行了 `git add .`，又执行了 `git commit`，这时候你的代码已经进入了你的本地仓库，然而你后悔了，怎么办？不要着急，还有办法。
```bash
git reset --hard origin/master
```
{% alert info %}
还是这个 `git reset --hard`命令，只不过这次多了一个参数origin/master，正如我们上面讲过的，origin/master代表远程仓库，既然你已经污染了你的本地仓库，那么就从远程仓库把代码取回来吧。
{% endalert %}

4.已推送
更糟糕的是，你既 `git add` 了，又 `git commit` 了，并且还 `git push` 了，这时你的代码已经进入远程仓库。如果想要撤销，执行如下命令：
```bash
git reset --hard HEAD^
git push -f
```
{% alert info %}
简单来说：由于你的本地仓库和远程仓库是等价的，你只需要先恢复本地仓库，再强制 `push` 到远程仓库就好了：
{% endalert %}

此处参考，[Git 的 4 个阶段的撤销更改](http://mp.weixin.qq.com/s/akvB2DO_1dpUrf-ol77MwQ)
















