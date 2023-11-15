# Git 常用命令总结

## git config
``` shell
git config --list           # 查看配置
git config -e [--global]    # 编辑配置文件

# 设置提交代码时的用户信息
git config [--global] user.name "[name]"
git config [--global] user.email "[email address]"
```

## 版本回退 git reset

版本回退是指将版本库恢复到上个或者某个 `git commit` 之后。Git 中每次进行 `commit` 操作会在版本库中保存一个快照，如果把文件改乱了，或者误删了文件，可以从某个 commit 恢复，用到的操作命令如下：

```shell
git reset [--soft | --mixed | --hard] <commit-id | HEAD>

说明：
--mixed: 默认参数，不删除工作空间改动代码，撤销 commit，并且撤销 git add .
--soft: 不删除工作空间改动代码，撤销 commit，不撤销 git add .
--hard: 删除工作空间改动代码，撤销 commit，撤销 git add . （即完全恢复到某次 git commit 之后）

如果使用 --mixed 或者 --soft 参数回到多个版本之前时，需要注意后面多次修改的内容都会留在工作区或者暂存区
```

其中 `commit-id` 可用命令 `git log --pretty=oneline` 查看。回退的版本较近时可以用 `HEAD` 表示，在 Git 中，用 `HEAD` 表示当前版本，上一个版本就是 `HEAD^`，上上一个版本就是 `HEAD^^`，依次类推。

实例：

```shell
git reset HEAD^                   # 回退所有内容到上一个版本
git reset HEAD^ hello.txt         # 回退 hello.txt 文件的版本到上一个版本
git reset  052e                   # 回退到指定版本
git reset --hard origin/master    # 将本地的状态回退到和远程的一样
```

## 撤销修改 git restore

撤销修改是指将工作区或者暂存区的修改撤销，用到的操作命令如下：

```shell
git restore [--staged] <file>

说明：
--staged: 将暂存区的修改撤销

注意：git restore 对于 untracked files 是不起作用的。
```

## git tag

在进行版本发布时会使用 Git 的标签功能，Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated）。
轻量标签很像一个不会改变的分支——它只是某个特定提交的引用。而附注标签是存储在 Git 数据库中的一个完整对象，它们是可以被校验的，其中包含打标签者的名字、电子邮件地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。 通常会建议创建附注标签，这样你可以拥有以上所有信息。但是如果你只是想用一个临时的标签， 或者因为某些原因不想要保存这些信息，那么也可以用轻量标签。下面是标签功能设计到的常用操作命令：

```shell
git tag [-a] <tag-name> <commit-id>     #- a: 附注标签
```

其他常用命令：

```shell
git tag [-l | --list] <可带通配符的标签名>   # 列出标签
git show <tag-name>                       # 查看标签信息和与之对应的提交信息
git push origin <tag-name>                # 标签信息推送到服务器
git tag -d <tagname>                      # 删除标签
git push origin --delete <tag-name>       # 删除远程标签
git checkout -b <branch-name> <tag-name>  # 从某个标签处创建分支
```

## git branch
```shell
git branch [-a | -r]                                    # 列出分支，-a 列出所有分支，-r 列出远程分支
git branch <new-branch>                                 # 创建新分支
git branch <new-branch> <commit-id>                     # 基于某个 commit 创建新分支
git checkout [-b] <branch-name>                         # 切换分支，-b 创建并切换到新分支
git merge <branch-name>                                 # 合并分支到当前分支
git branch --set-upstream [branch] [remote-branch]      # 设置远程分支
git branch -m <new-name>                                # 分支重命名，切换到需重命名的分支后执行
git branch [-d | -D] <branch-name>                      # -d 删除本地已合并的分支, -D 强制删除
git push origin --delete <branch-name>                  # 删除远程分支
```

## git cherry-pick
多分支代码库，将某个分支的某个（某几个）提交合并到当前分支，并产生一个新的提交。产生冲突的时候，该操作会停下来，需要用户解决冲突。

```shell
git cherry-pick <commit-id-1> ... <commit-id-n> [-e | -n | -s |-x]  [-m parent-number]

# -e 编辑提交信息； -n 不生成新的提交，只更新工作区和暂存区; -s 末尾追加操作者签名；
# -x 在提交信息的末尾追加一行(cherry picked from commit ...)，方便以后查到这个提交是如何产生的

# 如果原始提交是一个合并节点，来自于两个分支的合并，那么 Cherry pick 默认将失败，因为它不知道应该采用哪个分支的代码变动
# -m 配置项告诉 Git，应该采用哪个分支的变动。它的参数parent-number是一个从1开始的整数，代表原始提交的父分支编号
```

## git stash
有时，在项目的一部分上已经工作一段时间后，所有东西都进入了混乱的状态， 而这时想要切换到另一个分支做一点别的事情。 但是，又不想仅仅因为过会儿回到这一点而为做了一半的工作创建一次提交。 这是就可以使用到 `git stash` 命令。

stash 会处理工作目录的脏的状态，并将未完成的修改保存到一个栈上，而你可以在任何时候（甚至在不同的分支上）重新应用这些改动。


```shell
git stash [-u | -a]                         # 保存当前工作区的修改, -u 选项会将未跟踪的文件一并保存, -a 选项会将所有修改（包括未跟踪的文件）保存
git stash save "stash message"              # 效果同上，但是可为每个 stash 添加说明，记录 stash 版本
git stash list                              # 查看保存的工作区
git stash pop [<stash@{1}>]                 # 从 stash 堆栈中恢复，并删除该 stash
git stash drop [<stash@{1}>]                # 删除 stash，可删除指定的 stash
git stash apply <stash@{1}> [--index]       # 恢复指定的工作区，可将堆栈中的 stash 多次应用于工作区，不会删除堆栈。--index 选项会将暂存区的内容也恢复，默认不恢复
```



## git blame

查看某个文件的某一行具体是谁编写的, 类似于 IDEA 的 git 追溯注解。

```shell
git blame <filename>
git blame <filename> | grep "pattern"  # 查找某个文件某一部分是谁编写的
git blame <filename> -L <line1,line2>  # 查看某个文件文件行区间是谁编写的
```



## git fsck

该命令常用于恢复本地错误操作。对 HEAD 进行操作的错误，一般情况下 reflog 都能够恢复，然而有些错误并不会对 HEAD 进行操作，例如将部分代码 stash 之后又不小心 drop 或 clear 掉了，那么此时 HEAD 并没有发生变化，reflog 对此类错误是无能为力的，这个时候 fsck 就可以派上用场了，该命令会更加底层，即直接检查 git 中的 blob，tree 和 commit 对象并找到悬空的对象，找到后即可以通过 `git show <commitId>` 来查看该 commit 是否是被误操作删掉的 commit，如果是的话知道这个 commitId 都可以恢复。

```Shell
git fsck --lost-found
```

```
[ydj-macbook-pro ~ % git fsck --lost-found
Checking object directories: 100% (256/256), done.
Checking objects: 100% (64354/64354), done.
dangling blob eb0a5cabe0d2030d22c011ffd96584a8b9d46ef5
dangling blob f70d5ccb0439260ea17892401cb22e54f456bf04
dangling blob 5e13c8974187cdf3d3bd171c38bcb7ed88ce9672
dangling blob a1140825d9cd0ebd157098b8d4f8685383d66a59
dangling blob 841a404f69eba49fb48b5d5ef1864681b9221ca4
dangling blob 36222c728e3596e57e7837bb46361b436ef7dd90
dangling blob 492320b8281788ea90a4154b92e1adeb8cba9fbd
dangling blob 81279c8f3fd9b95edae9bfb16c2655236880d679
dangling blob 0b2e7c8280893cd0f461d59b4e2787fca00215fa
dangling blob 2c32a4721204375f08699df533e25df087ba5614
dangling blob f934a008b42005ff416099ddf4619b25f3019195
dangling blob c938a8d3a8de7b2b91cebff691a0c7b07c83e1d3
dangling tree b43eb805a4f9e525118c10f3c8dc58ddf35be8db
dangling blob ff3ec88e19a4a202d1f8d4e987e2052375500ad7
dangling blob c03f7c6eea43833c92a27f605ce564f90fe6c491
dangling commit a3aed9355e4efbf5a149a8e53a672c47625d1932
dangling blob 4e410cea8127c6b84b58e4f67bcc3a9b3f410f74
[ydj-macbook-pro ~ %
```

```shell
git show <commitID>     # 查看是否是自己想要的代码
git merge <commitID>    # 恢复代码
```



## 远程仓库

```shell
git remote [-v]                                 # 查看远程仓库
git remote add <remote-name> <remote-url>       # 添加远程仓库
git remote remove <remote-name>                 # 删除远程仓库
gti remote add <remote-name> <remote-url>       # 添加远程仓库并命名为 origin
git remote set-url <remote-name> <remote-url>   # 修改远程仓库地址
```

## 参考
[1] Git - Book. https://git-scm.com/book/zh/v2.

[2] 常用 Git 命令清单 - 阮一峰的网络日志. https://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html.

[3] git cherry-pick 教程 - 阮一峰的网络日志. https://www.ruanyifeng.com/blog/2020/04/git-cherry-pick.html.
