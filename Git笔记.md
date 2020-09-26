# Git 笔记

### 版本控制

#### 1、版本控制介绍

版本控制工具应该具备的功能：

> - 协同修改
>   - 多人并行不悖的修改服务器端的同一文件
> - 数据备份
>   - 不仅保存目录和文件的当前状态，还能够保存每一个提交过的历史状态
> - 版本管理
>   - 在保存每一个版本的文件信息的时候要做到不保存重复数据，以节约存储空间，提高运行效率。这方面 svn 采用的是增量式管理的方式，而 git 采取的是文件系统快照的方式。
> - 权限控制
>   - 对团队中参与开发的人员进行权限控制
>   - 对团队外开发者贡献的代码进行审核，这个是 git 独有的
> - 历史记录
>   - 查看修改人、修改时间、修改内容、日志信息
>   - 将本地文件恢复到某一个历史状态
> - 分支管理
>   - 允许开发团队在工作过程中多条生产线同时推进任务，进一步提高效率。

### Git

#### 1、git 的优势

> - 大部分操作在本地完成，不需要联网
> - 完整性保证
> - 尽可能添加数据而不是删除或修改数据
> - 分支操作非常快捷流畅
> - 与 Linux 命令全面兼容

#### 2、git 结构

> git 中会有 工作区、暂存区、本地库
>
> - 工作区：就是你写代码的地方
> - 暂存区：临时存储的区域
> - 本地库：存放历史版本

```
工作区---(git add)-----> 暂存区 ----(git commit)---> 本地库
```

#### 3、git 和代码托管中心

代码托管中心的任务：维护远程库

对于代码托管中心：

> 局域网环境下：
>
> - GitLab 服务器
>
> 外网环境下：
>
> - GitHub 
> - 码云

### Git 命令行操作

#### 1、本地库初始化

> - 命令：`git init`
> - 注意：执行初始化命令之后，会在当前目录生成一个 `.git` 隐藏目录，这个目录中存放的是本地库相关的子目录和文件，不能删除也不要去修改。

#### 2、设置签名

> - 形式
>
>   - 用户名：hpshen1996
>   - Email 地址： hpshen1996@163.com（用户名和 email 地址的用户名可以不用相同）
>
> - 作用：
>
>   - 设置签名主要是为了区分不同开发人员的身份
>
> - 签名级别：
>
>   - 项目级别/仓库级别：仅在当前本地库范围内有效
>   - 系统用户级别：登录当前操作系统的用户范围
>
> - 级别的优先级
>
>   - 项目级别优先于系统用户级别，二者都有时采用项目级别的签名
>   - 如果只有系统用户级别的签名，就以系统用户级别的签名为准
>   - 二者都没有是不允许的，提交的时候会报错
>
> - 命令
>
>   - 项目级别/仓库级别：
>     - `git config user.name hpshen1996`
>     - `git config user.email hpshen1996@163.com`
>   - 系统用户级别：
>     - `git config --global user.name hpshen1996`
>     - `git config --global user.email hpshen1996@163.com`
>
> - 签名信息保存位置
>
>   - 如果是项目级别，签名信息会被保存在 `.git/config` 文件中
>
>   - ```
>     cat .git/config
>     [user]
>     	name = hpshen
>     	email = hpshen1996@163.com
>     ```
>
>   - 如果是系统用户级别，签名信息会被保存在 `~/.gitconfig` 文件中

#### 3、添加提交及查看状态

```shell
git status
On branch master    # 这个就表示在 master 分支上，也就是主干上
No commits yet      # 之前没有提交过什么，这个就表示本地库中没有任何提交
nothing to commit (create/copy files and use "git add" to track)   # 这个就表示暂存区中没有任何提交

vim hello.txt
git status
On branch master
No commits yet
Untracked files:    # 没有追踪到的文件，说明这个文件没有加到暂存区被 git 管理
  (use "git add <file>..." to include in what will be committed)

	hello.txt

nothing added to commit but untracked files present (use "git add" to track)

git add hello.txt
git status
On branch master
No commits yet
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   hello.txt

git rm --cached hello.txt       # 可以删除暂存区中的文件
```

**相关命令：**

① 状态查看操作

> `git status`
>
> 查看工作区、暂存区的状态

② 添加操作

> `git add [file name]`
>
> 将工作区的 “新建/修改” 添加到暂存区

③ 提交操作

> `git commit [file name] -m [commit message]`
>
> 将暂存区的内容提交到本地库

#### 4、查看历史记录

① `git log`

显示完整的历史记录

```
commit ff784c95290c5c81abf99b548f68c516edf2674a (HEAD -> master)
Author: hpshen <hpshen1996@163.com>
Date:   Sat May 4 14:00:10 2019 +0800

    test 666
```

② `git log --pretty=oneline`

一行方式显示历史记录

```
ff784c95290c5c81abf99b548f68c516edf2674a (HEAD -> master) test 666
ab9a83a0bab1631afbb6b40859a61bd095723b85 my second commit modifiy
f0213b36a70d4a77f4758d27c28fe9b7df12573c new file hello.txt
```

③ `git log --oneline`

一行的方式显示历史记录

```
ff784c9 (HEAD -> master) test 666
ab9a83a my second commit modifiy
f0213b3 new file hello.txt
```

④ `git reflog`

可以显示出回退到某个版本需要移动几步

```
ff784c9 (HEAD -> master) HEAD@{0}: commit: test 666
ab9a83a HEAD@{1}: commit: my second commit modifiy
f0213b3 HEAD@{2}: commit (initial): new file hello.txt
```

HEAD@{移动到当前版本需要多少步}

#### 5、版本前进后退操作的本质

git 在帮我们管理版本历史记录的时候，其实它有一个指针，这个指针的名字就叫 HEAD，我们可以基于 HEAD 这个指针把我们当前所在的版本，想前或想后移动，也就是历史记录的前进和回退。

#### 6、基于索引值前进后退版本

> 命令：
>
> `git reset --hard [局部索引值]`

```shell
git reflog
ff784c9 (HEAD -> master) HEAD@{3}: commit: test 666
ab9a83a HEAD@{4}: commit: my second commit modifiy
f0213b3 HEAD@{5}: commit (initial): new file hello.txt

git reset --hard ab9a83a     # 回退到索引值对应的版本
# 这种方式可以前进也可以后退版本，只要后面跟上索引值就可以到指定版本
```

#### 7、后退版本的其他方式

① 使用 ^ 符号（只能后退）

> 命令：
>
> `git reset --hard HEAD^`
>
> 一个 ^ 表示后退一步，n 个 ^ 符号表示后退 n 步

② 使用 ~ 符号（只能后退）

> 命令：
>
> `git reset --hard HEAD~2`
>
> 波浪号后的数字表示后退 n 步

#### 8、永久删除文件后找回

大致流程是这样的，你在工作区新建一个文件，然后提交到了本地库。这时候你在工作区将这个文件删除了，这时候该怎么找回这个删除的文件呢？

答：其实很简单，因为你已经提交到本地库了，查看版本历史，找到对应版本，然后恢复即可。

```shell
vim cc.txt
git add cc.txt
git commit cc.txt -m "新添加 cc.txt"
rm -rf cc.txt

git reflog
ed3080a (HEAD -> master) HEAD@{0}: commit: 新添加 cc.txt

git reset --hard ed3080a       # 这样就恢复这个删除的文件了
```

#### 9、添加到暂存区的删除文件找回

大致的流程是这样的，你在工作区新建一个文件，然后提交到了本地库，这时候你在工作区删除了这个文件，并且添加到了暂存区，这时候应该怎么恢复呢？

答：其实也很简单，直接看版本历史然后恢复即可。

```sh
vim dd.txt
git add dd.txt
git commit dd.txt -m "新添加 dd.txt"

rm -rf dd.txt
git add dd.txt

# 其实 --hard 的意思就是把 HEAD 指针移动到本地库对应的版本，然后同时刷新暂存区和工作区，让暂存区和工作区的版本和本地库保持一致
git reset --hard HEAD     # 或者你用 git reset --hard 3f2b7c1(因为 3f2b7c1 这个版本正好是 HEAD 指针指向的位置，不用前进也不用后退多少步)
```

#### 10、比较文件

大致流程是这样的，你在工作区新建了一个文件，然后提交到了本地库。这时候你在工作区修改了这个文件，这时候你就可以使用 `git diff 文件名` 这个命令来比较工作区和暂存区文件的不同。比如这时候，你修改了某一行的内容，但是你用 `git diff 文件名` 命令去查看的时候，虽然你只修改了某一行，但是==对于 git 来说，它是以行为单位进行文件的管理的==。所以你看到的结果就是删除原来的数据行，然后新增你修改的行。

假如这时候我用 `git add` 命令把修改的文件添加到暂存区，然后你再用 `git diff 文件名` 这个命令，发现就没有不同了。那么这时候你可以使用 `git diff HEAD 文件名`，这个就是与本地库中的某个版本进行比较了，你会发现又有不同了。甚至于你可以使用 `git diff HEAD^` 与某个历史版本进行比较。

**注：**

`git diff 文件名`  如果你不写文件名，是把工作区中的所有文件与暂存区或者本地库进行比较

### Git 分支

#### 1、分支概述

分支的好处：

> - 同时并行推进多个功能开发，提高开发效率
> - 各个分支在开发过程中，如果某一个分支开发失败，不会对其他分支有任何影响。失败的分支删除重新开始即可。

#### 2、分支操作

① 创建分支

`git branch 分支名`

② 查看分支

`git branch -v`

③ 切换分支

`git checkout 分支名`

④ 合并分支

假如我现在在 master 分支，然后发现了 bug ，那么我可以新建一个分支，专门去修复 bug，假如使用 `git branch hot_fix`，创建了一个 hot_fix 分支，然在在这个分支上修复完 bug 后，我想把这个分支合并到 master 上。

> 合并分支：
>
> - 需要切换到接受修改的分支上
>   - 比如说我现在在 hot_fix 分支上，我想把这个分支上修改的内容合并到 master 上，那么首先我应该使用 `git checkout master` 切换到 master 分支上
>   - 执行合并命令。`git merge hot_fix`    我现在在 master 分支上，执行这条命令后，就会把 hot_fix 上修改的内容合并到 master 分支上。

#### 3、分支合并冲突解决

大致的流程是这样的，比如说我现在有两个分支，一个 hot_fix 分支，一个 master 分支，我现在在两个分支上都修改了同一个文件的同一处内容，那么在合并的时候就会出现冲突。

```shell
Switched to branch 'host_fix'
aaaaaa
bbbbbb
cccccc
shp
<<<<<<< HEAD                           # 从这个开始
666 edit by master								# 这中间表示当前分支的内容
=======									# 到这结束      从这里开始
666 edit by hot_fix								# 这中间表示另一分支的内容
>>>>>>> host_fix						# 到这里结束
```

**解决冲突的步骤：**

> 1、编辑有冲突的文件，删除特殊符号
>
> 2、编辑有冲突的文件，把文件修改到满意的程度，保存退出
>
> 3、`git add 文件名`
>
> 4、`git commit -m "提交信息"`      注意这里不要写文件名，不然会报错

### Git 基本原理

#### 1、哈希算法简介

哈希是一个系列的加密算法，各个不同的哈希算法虽然加密强度不同，但是有以下几个共同点：

> 1、不管输入数据的数据量有多大，输入同一个哈希算法，得到的加密结果长度固定
>
> 2、哈希算法确定，输入数据确定，输出数据能够保持不变
>
> 3、哈希算法确定，输入数据有变化，输出数据一定有变化，而且通常变化很大
>
> 4、哈希算法不可逆

Git 底层采用的是 SHA-1 算法。

哈希算法可以用来验证文件

> 比如说我现在有一个原始文件  `------>(SHA1加密)`      `aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d`
>
> 然后这个原始文件经过了网络传输，得到目标文件，我们不知道这个目标文件有没有损坏，或者说与原来的原始文件相比，有没有差异，这时候我们就可以对目标文件进行 SHA1 加密    `----->(SHA1加密)`  `aaf4c61ddcc5e8a2dabede0f3b482cd9aea9434d`  ，加密结果和原始文件的加密结果进行比对，不过不一样，证明文件有损坏。

### Git 本地库和远程库

#### 1、本地库和远程库交互方式

说一下本地库和远程库是怎么交互的，比如说现在有 A 和 B 两个用户，A 用户在本地创建一个本地库，在远程创建一个远程库，然后可以把本地库的内容 push 到远程库，然后 B 用户可以去 clone 这个远程库到本地库，这样 B 用户就可以在本地库中对其做修改，修改完成后，在 B 用户加入到 A 用户的团队的基础上，B 用户就可以把修改的内容 push 到远程库中，然后 A 用户再  pull 远程库中的修改到本地库。

#### 2、在本地创建远程库别名

假如你现在在本地创建了一个本地库，在远程也有一个远程库，然后你现在想把本地库的内容推送到远程库，应该怎么做呢？

答：首先我们需要知道的就是我要把本地库推送到远程的哪个仓库，那么第一步我们就要在本地创建一个远程库的别名。

```shell
git remote -v     # 命令行执行这条命令，你会发现返回是空，因为我们没有创建远程库的别名

git remote add origin git@github.com:hepingfly/testgithub.git
# 注：
# origin 是给远程库起的别名
# git@github.com:hepingfly/testgithub.git 是远程库的地址
# 以后就用 origin 代表上面一长串的远程库地址

git remote -v
origin	git@github.com:hepingfly/testgithub.git (fetch)
origin	git@github.com:hepingfly/testgithub.git (push)
```

#### 3、推送操作

上一步我们已经给远程仓库创建了别名，现在我们就可以进行推送操作了。

推送命令：

> `git push origin master`
>
> 解释下上面这个命令：
>
> git push + 你要推送的远程库地址的别名 + 你要推送到哪个分支上

### Git 工作流

git 工作流说白了就是我们在项目开发过程中如何去使用 git 。

#### 1、集中式工作流

这种方式像 SVN 一样，集中式工作流以中央仓库作为项目所有修改的单点实体。所有修改都提交到 Master 这个分支上。这种方式与 SVN 的主要区别就是开发人员有本地库。Git 的很多特性并没有用到。

![集中式工作流](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-git/%E9%9B%86%E4%B8%AD%E5%BC%8F%E5%B7%A5%E4%BD%9C%E6%B5%81.png)

#### 2、GitFlow 工作流

- 主干分支  master
  - 主要负责管理正在运行的生产环境代码。永远保持与正在运行的生产环境完全一致。
- 开发分支  develop
  - ​	主要负责管理正在开发过程中的代码。一般情况下应该是最新的代码。
- bug 修理分支   hotfix 
  - 主要负责管理生产环境下出现的紧急修复的代码。从主干分支分出，修理完毕并测试上线后，并回主干分支。并回后，视情况可以删除该分支。
- 准生产分支（预发布分支） release 
  - 较大的版本上线前，会从开发分支中分出准生产分支，进行最后阶段的集成测试。该版本上线后，会合并到主干分支。生产环境运行一阶段较稳定后可以视情况删除。
- 功能分支  feature
  - 为了不影响较短期的开发工作，一般把中长期开发模块，会从开发分支中独立出来。开发完成后会合并到开发分支。

![GitFlow流](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-git/git%E5%B7%A5%E4%BD%9C%E6%B5%81.png)

下面具体描述一下这个 git 工作流：

```
首先 master 分支一般都是生产环境正在运行的代码，现在是 v1.0 版本。我们现在想开发一个新的功能，这个新的功能我们不能直接在 master 上进行开发，我们一般会有一个 develop 分支，这个分支作为我们开发过程中的基准分支，你开发不同功能的分支，最终都要把它合并到 develop 分支上去。
比如我们现在想要开发二个新的功能，一个叫 talk 功能。一个叫 read 功能。这两个功能为了可以同时进行开发，我们创建两个分支来进行开发，正在开发过程中，我们线上运行的代码出现 bug 了，有 bug 就需要修复。
修复 bug 我们就会创建一个 hotfix 分支，在这个分支上去修复 bug。修完之后直接把这个分支合并到 master 分支上，这样 master 分支上就没有 bug 了。这个时候 master 变成 v1.1 版本。
修复的这个 bug 同样要给它合并到 develop 分支上。
然后继续进行 talk 分支的开发，等到 talk 分支开发完成了，就把这个分支合并到 develop 分支上。这个时候要准备上线了，但是在上线之前需要做测试，我们基于 develop 分支去创建一个 release 分支，作为预发布分支，在这个分支上做上线前测试，如果发现 bug ，那么直接在 release 分支上进行修复，修复完测试没有问题了，就把 release 分支合并到 master 分支上，这样我们新开发的功能就可以发布到线上了，master 变成 v2.0 版本。
同样刚才在 release 分支上修复的 bug ，同样也需要合并到 develop 分支上。 

类似于 talk 分支，当 read 分支功能也开发完成了，那么同样把 read 分支合并到 develop 分支上，然后准备上线。
上线前同样要基于 develop 分支创建一个 release 分支作为预发布分支，在这个分支上进行测试，有问题直接在 release 分支进行修复，修复完验证没有问题，再把 release 分支合并到 master 分支上，功能上线成功。
```

#### 3、Forking 工作流

Forking 工作流是在 GitFlow 基础上，充分利用了 Git 的 Fork 和 pull request 的功能以达到代码审核的目的。更适合安全可靠的管理大团队的开发者，而且能接受不信任贡献值的提交。















































































































































