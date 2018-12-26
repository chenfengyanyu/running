---
title: Git 版本控制之 Git-Flow
date: 2018-12-05 22:49:54
thumbnailImage: https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitflow/logo.png
thumbnailImagePosition: left
tags: 
- git
- git-flow
comments: false
metaAlignment: center
categories: 技术博文
---
最近在着手制定开发规范，想要把项目正规高效的跑起来。计划引入 Git 版本控制，Git-Flow 便成为了首选。因为之前并没有过多接触，所以先花些时间摸索一下。
<!-- more -->
#### 一、为什么使用 git-flow
当在团队开发中使用版本控制系统时，商定一个统一的工作流程是至关重要的。`Git` 的确可以在各个方面做很多事情，然而，如果在你的团队中还没有能形成一个特定有效的工作流程，那么混乱就将是不可避免的。

{% alert success %}
基本套路：你可以定义一个完全适合你自己项目的工作流程，或者使用一个别人定义好的。
{% endalert %}


#### 二、安装 git-flow
我们使用 `Homebrew` 来安装 `git-flow`：
```
brew install git-flow
```
之后，通过 `git-flow` 来初始化项目：
```
git flow init
```
这时候就会有一些配置的操作，先默认操作：
```
Initialized empty Git repository in /Users/jartto/Documents/git-flow-demo/.git/
No branches exist yet. Base branches must be created now.
Branch name for production releases: [master]
Branch name for "next release" development: [develop]

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'jartto@bogon.(none)')
fatal: Not a valid object name: 'master'.
error: pathspec 'develop' did not match any file(s) known to git.

How to name your supporting branch prefixes?
Feature branches? [feature/]
Release branches? [release/]
Hotfix branches? [hotfix/]
Support branches? [support/]
Version tag prefix? []
```
{% alert info %}
需要强调一点：git-flow 只是封装了 git 的命令。
{% endalert %}


所以在我们初始化的时候，对仓库并没有其他改动，只是创建了几个分支。当然，如果你不想继续使用 `git-flow` ,那么只需要简单的停用 `git-flow` 的命令即可，不需要修改或者删除任何文件。

为了验证一下，我们看下目前的分支，执行：
```
git branch
```
输出：
```
* develop
  master
```
很简单，`develop` 分支是我们日常开发的分支，会有很多改动。而 `master` 主要针对线上分支，[下面](http://jartto.wang/2018/12/05/git-flow/)会细说。

{% alert info %}
这里补充一点，我们可以通过 help 命令来查看用例。
{% endalert %}
```
git flow help
```
输出如下：
```
usage: git flow <subcommand>

Available subcommands are:
   init      Initialize a new git repo with support for the branching model.
   feature   Manage your feature branches.
   release   Manage your release branches.
   hotfix    Manage your hotfix branches.
   support   Manage your support branches.
   version   Shows version information.

Try 'git flow <subcommand> help' for details.
```
如果我要看 `feature` 相关命令呢？
```
git flow feature help
```
使用规范就会列出来：
```
usage: git flow feature [list] [-v]
       git flow feature start [-F] <name> [<base>]
       git flow feature finish [-rFk] <name|nameprefix>
       git flow feature publish <name>
       git flow feature track <name>
       git flow feature diff [<name|nameprefix>]
       git flow feature rebase [-i] [<name|nameprefix>]
       git flow feature checkout [<name|nameprefix>]
       git flow feature pull <remote> [<name>]
```

#### 三、分支模式
`git-flow` 模式会预设两个主分支在仓库中：
1.`master` 只能用来包含产品代码
我们不能直接工作在这个 `master` 分支上，而是在其他指定的，独立的特性分支中。
{% alert warning %}
不直接提交改动到 master 分支上也是很多工作流程的一个共同的规则。
{% endalert %}

2.`develop` 是你进行任何新的开发的基础分支
当你开始一个新的功能分支时，它将是开发的基础。另外，该分支也汇集所有已经完成的功能，并等待被整合到 `master` 分支中。

![gitflow1](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitflow/gitflow1.png)

{% alert info %}
上面说到的这两个分支被称作为长期分支，它们会存活在项目的整个生命周期中。
{% endalert %}


而其他的分支，例如针对功能的分支，针对发行的分支，仅仅只是临时存在的。它们是根据需要来创建的，当它们完成了自己的任务之后就会被删除掉。

![gitflow2](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitflow/gitflow2.png)


#### 四、明确分支功能
1.`master` 分支
最为稳定功能比较完整的随时可发布的代码，即代码开发完成，经过测试，没有明显的 `bug`，才能合并到 `master` 中。请注意永远不要在 `master` 分支上直接开发和提交代码，以确保 `master` 上的代码一直可用；

2.`develop` 分支
用作平时开发的主分支，并一直存在，永远是功能最新最全的分支，包含所有要发布 到下一个 `release` 的代码，主要用于合并其他分支，比如 `feature` 分支； 如果修改代码，新建 `feature` 分支修改完再合并到 `develop` 分支。所有的 `feature`、`release` 分支都是从 `develop` 分支上拉的。

3.`feature` 分支
这个分支主要是用来开发新的功能，一旦开发完成，通过测试没问题，我们合并回 `develop` 分支进入下一个 `release` 。

4.`release` 分支
用于发布准备的专门分支。当开发进行到一定程度，或者说快到了既定的发布日，可以发布时，建立一个 `release` 分支并指定版本号(可以在 `finish` 的时候添加)。开发人员可以对 `release` 分支上的代码进行集中测试和修改 `bug`。（这个测试，测试新功能与已有的功能是否有冲突，兼容性）全部完成经过测试没有问题后，将 `release` 分支上的代码合并到 `master` 分支和 `develop` 分支。

5.`hotfix` 分支
用于修复线上代码的 `bug` 。从 `master` 分支上拉。完成 `hotfix` 后，打上 `tag` 我们合并回 `master` 和 `develop` 分支。

需要注意：
- 所有开发分支从 `develop` 分支拉。
- 所有 `hotfix` 分支从 `master` 拉。
- 所有在 `master` 上的提交都必要要有 `tag`，方便回滚。
- 只要有合并到 `master` 分支的操作，都需要和 `develop` 分支合并下，保证同步。
- `master` 和 `develop` 分支是主要分支，主要分支每种类型只能有一个，派生分支每个类型可以同时存在多个。

#### 五、关于 Feature 分支
在 `Git-flow` 中，通过使用 `Feature` 分支，使得我们在同一时间开发多个分支更加简单。
我们接到了一个 `Test1` 需求，使用 `feature start` 来启动：
{% alert info %}
git flow feature start test1
{% endalert %}

当我们开始一个新的 `feature` 开发后：
```
Switched to a new branch 'feature/test1'

Summary of actions:
- A new branch 'feature/test1' was created, based on 'develop'
- You are now on branch 'feature/test1'

Now, start committing on your feature. When done, use:

     git flow feature finish test1
```
我们自动切到了 `feature/test1` 分支下，正好开始我们的开发，建一个文件先：
```
vi main.js
```
写入 `console.log('hello jartto');` 接着提交我们的变更：
```
git add .
git commit -m 'feat: add console.log'
```
好了，现在我们开发完了，结束 feature 开发：
```
git flow feature finish test1
```
控制台输出了：
```
Switched to branch 'develop'
Updating d975789..27e920c
Fast-forward
main.js | 1 +
1 file changed, 1 insertion(+)
create mode 100644 main.js
Deleted branch feature/test1 (was 27e920c).

Summary of actions:
- The feature branch 'feature/test1' was merged into 'develop'
- Feature branch 'feature/test1' has been removed
- You are now on branch 'develop'
```
这里做了几件事情：
1.将 `feature/test1` 分支合并到了 `develop` 分支；
2.删除了 `feature/test1`；
3.切换到 `develop` 分支；

需要注意：`git-flow` 使用的命令是：
```
git merge —no-ff feature/test1
```
{% alert info %}
这样，在我们移除 `feature` 分支之前，是不会丢失任何历史记录的。
{% endalert %}

如果你还不了解 `--no-ff` 相关知识，可以先看看：[Git merge 的 --ff 和 --no-ff](https://blog.csdn.net/chaiyu2002/article/details/81020370)。

接着，我们看一下变更记录：
```
git log --oneline
```
输出：
```
27e920c (HEAD -> develop) feat: add console.log
d975789 (master) Initial commit
```

#### 六、release 分支－版本发布
当我们开发完毕，需要去发布新版本的时候，我们可以使用：
```
git flow release start 0.1.0
```
控制台会有一些提示：
```
Switched to a new branch 'release/0.1.0'

Summary of actions:
- A new branch 'release/0.1.0' was created, based on 'develop'
- You are now on branch 'release/0.1.0'

Follow-up actions:
- Bump the version number now!
- Start committing last-minute fixes in preparing your release
- When done, run:

     git flow release finish '0.1.0'
```

很清晰，我们简单说一下：
1.基于 `develop` 分支新建了 `release/0.1.0` 分支；
2.切换至 `release/0.1.0` 分支；

又出现了新问题：
1.这是什么意思：`Bump the version number now!`
2.`last-minute fixes` 又是什么意思？

{% alert info %}
那接下来我们要做什么呢？不着急，按照提示一步步来。
{% endalert %}

我们修改了代码，进行add，和 commit 之后，执行：
```
git flow release finish 0.1.0
```
这个过程中间可能出现异常：
```
fatal: no tag message?
Tagging failed. Please run finish again to retry.
```
输出：
```
Switched to branch 'develop'
Merge made by the 'recursive' strategy.
test.js | 0
1 file changed, 0 insertions(+), 0 deletions(-)
create mode 100644 test.js
Deleted branch release/0.1.0 (was 0426707).

Summary of actions:
- Latest objects have been fetched from 'origin'
- Release branch has been merged into 'master'
- The release was tagged '0.1.0'
- Release branch has been back-merged into 'develop'
- Release branch 'release/0.1.0' has been deleted
```
这里主要有几个操作：
1.首先，`git-flow` 会拉取远程仓库，以确保目前是最新的版本。
2.然后，`release` 的内容会被合并到 `master` 和 `develop` 两个分支中去，这样不仅产品代码为最新的版本，而且新的功能分支也将基于最新代码。
3.为便于识别和做历史参考，`release` 提交会被标记上这个 `release` 的 `Tag`。
4.清理操作，版本分支会被删除，并且回到 `develop`。

#### 七、Hotfix 线上代码
{% alert warning %}
如果线上代码有问题，这时候你需要紧急修复呢？
{% endalert %}

我们可以使用 `git flow hotfix` ：
```
git flow hotfix start jartto
```
看一下执行了什么：
```
Switched to a new branch 'hotfix/jartto'

Summary of actions:
- A new branch 'hotfix/jartto' was created, based on 'master'
- You are now on branch 'hotfix/jartto'

Follow-up actions:
- Bump the version number now!
- Start committing your hot fixes
- When done, run:

     git flow hotfix finish 'jartto'
```
接着我们新建了目录：
```
mkdir assets
```
放入一张图片，修改完毕，提交并结束 `hotfix`：
```
git add .
git commit -m 'fix: assets img'
git flow hotfix finish 'jartto'
```
看一下 `git-flow` 有哪些操作：
```
Switched to branch 'master'
Merge made by the 'recursive' strategy.
assets/git-flow.png | Bin 0 -> 25839 bytes
1 files changed, 0 insertions(+), 0 deletions(-)
create mode 100644 assets/git-flow.png
Switched to branch 'develop'
Merge made by the 'recursive' strategy.
assets/git-flow.png | Bin 0 -> 25839 bytes
1 files changed, 0 insertions(+), 0 deletions(-)
create mode 100644 assets/git-flow.png
Deleted branch hotfix/jartto (was a734849).

Summary of actions:
- Latest objects have been fetched from 'origin'
- Hotfix branch has been merged into 'master'
- The hotfix was tagged 'jartto'
- Hotfix branch has been back-merged into 'develop'
- Hotfix branch 'hotfix/jartto' has been deleted
```
查看一下目前的 `Tag` 情况：
```
git tag
```
正是我们上面添加的两个标签：
```
0.1.0
jartto
```
总结一下：
1.完成的改动会被合并到 `master` 中，同样也会合并到 `develop` 分支中，这样就可以确保这个错误不会再次出现在下一个 `release` 中。
2.这个 `hotfix` 程序将被标记起来以便于参考。
3.这个 `hotfix` 分支将被删除，然后切换到 `develop` 分支上去。


#### 八、git-flow 流程图示
恭喜你，到这里你已经完成了 `git-flow` 的基本流程。为了更加整体的理解工作流，我们来看看下面这张流程图：
![gitflow3](https://raw.githubusercontent.com/chenfengyanyu/my-web-accumulation/master/images/gitflow/gitflow3.png)

{% alert success %}
清清楚楚，是不是和我们上面的过程一模一样。
{% endalert %}

#### 九、参考
[Learn Version Control with Git](https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/git-flow)
[Using git-flow to automate your git branching workflow](https://jeffkreeftmeijer.com/git-flow/)
[为什么使用 Git-flow 工作流](https://blog.csdn.net/aaaaaaliang/article/details/79451598)
[Git 分支模型](https://www.oschina.net/translate/a-successful-git-branching-model?print)