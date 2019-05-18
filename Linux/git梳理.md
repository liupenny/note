# git基础概念

## 三个工作区

- git项目的三个工作区域：工作目录 -- 暂存区域 -- Git 仓库

![](https://ws4.sinaimg.cn/large/006tNc79ly1fzhq0sfy81j30cq06iaa8.jpg)

![](https://git-scm.com/book/en/v2/images/areas.png)

**工作目录：**是对项目的某个版本独立提取出来的内容。 这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。
**暂存区域：**是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。 有时候也被称作`‘索引’'，不过一般说法还是叫暂存区域。

**Git 仓库目录：**是 Git 用来保存项目的元数据和对象数据库的地方。 这是 Git 中最重要的部分，从其它计算机克隆仓库时，拷贝的就是这里的数据。

## 工作流程

- 基本的 Git 工作流程如下：

1. 在工作目录中修改文件。 Vim / emacs
2. 暂存文件，将文件的快照放入暂存区域。 git add
3. review改变：git diff / status
4. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。

git commit -a : 一个文件一开始不在暂存区时，第一次需要add，之后再修改只需-a即可。

## 文件状态

- Git 有三种状态，你的文件可能处于其中之一：已提交（committed）、已修改（modified）和已暂存（staged）

1. 已提交表示数据已经安全的保存在本地数据库中。

2. 已修改表示修改了文件，但还没保存到数据库中。 

3. 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。

工作目录下的每一个文件都不外乎这两种状态**：已跟踪或未跟踪**。

- 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后，**它们的状态可能处于未修改，已修改或已放入暂存区。** 

- 工作目录中除已跟踪文件以外的所有其它文件都属于**未跟踪文件，它们既不存在于上次快照的记录中，**也没有放入暂存区。

1. 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态。
2. 编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。 
3. 逐步将这些修改过的文件放入暂存区，然后提交所有暂存了的修改，如此反复。

![](https://ws3.sinaimg.cn/large/006tNc79ly1fzhqgwcfspj316c0i8wg5.jpg)

## 运行前的配置

Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：
1. `/etc/gitconfig 文件:` 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 --system 选项的git config 时，它会从此文件读写配置变量。
2. `~/.gitconfig 或 ~/.config/git/config 文件：`只针对当前用户。 可以传递 --global 选项让 Git读写此文件。
3. ` .git/config:`当前使用仓库的 Git 目录中的 config 文件：针对该仓库。
  每一个级别覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 /etc/gitconfig 中的配置变量。

**用户信息**

安装完 Git 应该做的第一件事就是设置你的用户名称与邮件地址。 这样做很重要，因为每一个 Git 的提交都会使用这些信息，并且它会写入到你的每一次提交中，不可更改：

```
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
```

再次强调，如果使用了 --global 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。 当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运
行没有 --global 选项的命令来配置。

` git config --list:` 命令来列出所有 Git 当时能找到的配置可以检查你的配置

 `git config key ：` 检查 Git 的某一项配置

## 相关名词

- Origin：使用 clone 命令克隆了一个仓库，命令会自动将其添加为远程仓库并默认以 “origin” 为简写。
-  Master：使用 clone 命令克隆了一个仓库，命令会自动设置本地 master分支跟踪克隆的远程仓库的master分支（或不管是什么名字的默认分支）。
- HEAD：一个指针，指向当前所在的本地分支（译注：将 HEAD 想象为当前分支的别名）。

## 打标签

Git 可以给历史中的某一个提交打上标签.

Git 使用两种主要类型的标签：轻量标签（lightweight）与附注标签（annotated）。

- `轻量标签 git tag`：很像一个不会改变的分支 - 它只是一个特定提交的引用。

- `附注标签 git tag -a`：是存储在 Git 数据库中的一个完整对象。 它们是可以被校验的；其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU Privacy Guard （GPG）签名与验证。 

- git push 命s令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到
  共享服务器上。 这个过程就像共享远程分支一样 - 你可以运行 git push origin [tagname]。

## 本质

1. Git 保存的不是文件的变化或者差异，而是一系列不同时刻的文件快照（blob类型）

2. 在进行提交操作时，Git 会保存一个提交对象（commit object）。**该提交对象会包含一个指向暂存内容快照的指针。** 

3. 该提交对象还包含了作者的姓名和邮箱、提交时输入的信息以及指向它的父对象的指针。首次提交产生的提交对象没有父对象，普通提交操作产生的提交对象有一个父对象，而由多个分支合并产生的提交对象有多个父对象

4. commit本身是一个对象，指向一个tree（当前分支下的目录结构）；tree本身也是一个对象，指向很多blob，blob也是一个对象

   ![](https://ws3.sinaimg.cn/large/006tNc79ly1fzhtcbxcofj306r06jq4h.jpg)

5. master是一个文件，内部是一个指针，指向第一个commit。

6. commit命令会创建一个commit（即一个文件），并将当前指针指向这个最新的，此时修改后的文件会（带新的hash值）+ 新的tree + 新的commit 都会保存在rep中。

   ![](https://ws4.sinaimg.cn/large/006tNc79ly1fzhtnt3cnnj318m0u0tdq.jpg)

7. branch就是一个指针，指向当前的commit文件
8. git checkout -b:创建+切换到新分支
9. merge时就是branch的tree的merge。
10. 一个仓库其实就是很多commit形成的有向无环图DAG

## 分支和merge的用处

Git branch -d / D :

-d的话，只有当前分支已经merge在master内时，才可以删除。

-D：不管，以后也找不回了。

# .git目录结构

1. .git里面包含HEAD，config，description和branches、hooks、info、objects、refs五个文件夹。

2. - description 文件仅供 GitWeb （git的web页面）程序使用

   - config 文件包含项目特有的配置选项。 

   - info 目录包含一个全局性排除（global exclude）文件 ，用以放置那些不希望被记录在 .gitignore 文件中的忽略模式
     405（ignored patterns）。 

   - hooks 目录包含客户端或服务端的钩子脚本（hook scripts），

     **重要：**

   - objects 目录存储所有数据内容；

   - refs 目录存储指向数据（分支）的提交对象的指针；

   - HEAD 文件指示目前被检出的分支；

   - index 文件保存暂存区信息。 

# 命令速查

![](https://ws2.sinaimg.cn/large/006tNc79ly1fzhjqluce5j30m80fjjt3.jpg)

跳过暂存区的方式: 只要在提交的时候，给 git commit 加上 -a 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add.

## git add

- git add -u ：添加编辑或者删除的文件，不包括新添加的文件。
- git add -a :   添加所有内容
- git add .：添加新文件和编辑过的文件不包括删除的文件

# 查看项目改动

## git diff

`Git diff：`比较的是工作目录中当前文件和**暂存区域**快照之间的差异， 也就是修改之后还没有暂存起来的变化内容。

`git diff --cached:`暂存区与git仓库的区别

## git log

查看提交历史

git log —graph：用图的形式展示出commit之间的关系

--all :

--decorate:加上分支名

branchA ^branchB：在A分支，不在B分支的

## git commit

git commit -a : 一个文件一开始不在暂存区时，第一次需要add，之后再修改只需-a即可。

## 移除文件

- **要从 Git 中移除某个文件**，就必须要从已跟踪文件清单中移除（即从暂存区域移除），然后提交。
  **git rm：**从暂存区、工作目录中删除，然后commit提交，这样该文件以后就不会出现在未跟踪文件清单中了

- 如果删除之前修改过并且已经放到暂存区域的话，则必须要用
  强制删除选项 -f。 这是一种安全特性，用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。

  **git rm -f：** 删除修改过并已经放到暂存区的文件

- 想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中。 

  **git rm --cached：** 把文件从git仓库删除，但还在工作区

## 撤销操作

- `git commit --amend`: **重新提交，两次提交并在一起**

  提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 --amend 选项的提交命令尝试重新提交

- `git reset HEAD filename ` ：**取消暂存的文件**

  失误将所有文件add进去了，该命令可以取消add

- `git checkout -- [file]`：**撤消对文件的修改**

# 分支

- `git branch`：列出本地已经存在的分支，并且在当前分支的前面加“*”号标记，例如：

```
\#git branch
* liuruiqing.penny
  master
```

- `git branch -r`： 列出远程分支，例如：

```
  \#git branch -r
  origin/HEAD -> origin/master
  origin/adload
  origin/develop
  origin/liuruiqing.penny
  origin/master
```

- `git branch -a`：列出本地分支和远程分支，例如：

```
\#git branch -a
* liuruiqing.penny
  master
  remotes/origin/HEAD -> origin/master
  remotes/origin/adload
  remotes/origin/develop
  remotes/origin/liuruiqing.penny
  remotes/origin/master
```

- `git branch [分支名]`：创建一个新的本地分支，需要注意，此处只是创建分支，不进行分支切换
- `git branch -m | -M [oldbranch][newbranch] `重命名分支，如果newbranch名字分支已经存在，则需要使用-M强制重命名，否则，使用-m进行重命名。
- `git branch -d | -D [branchname]` 删除branchname分支
- `git branch -d -r [branchname] `删除远程branchname分支

## 修改分支名

- Git修改本地或远程分支名称

注：修改远程分支先拉下来再进行以下步骤 

1、旧分支：oldBranch

2、新分支：newBranch

步骤：

a、先将本地分支重命名

git branch -m oldBranch newBranch

b、删除远程分支（远端无此分支则跳过该步骤）

git push --delete origin oldBranch

c、将重命名后的分支推到远端

git push origin newBranch

d、把修改后的本地分支与远程分支关联

git branch --set-upstream-to origin/newBranch

# 远程仓库

- git remote ： 展示所有远程仓库
- git remote show：展示一个远程仓库的更多信息
- git fetch origin：origin为clone时的远程，该命令会抓取克隆（或上一次抓取）后新推送的所有工作。 **必须注意** git fetch 命令会将
  数据拉取到你的本地仓库 - 它并不会自动合并或修改你当前的工作。 
- git pull：有一个分支设置为跟踪一个远程分支（阅读下一节与 Git 分支 了解更多信息），可以使用 git pull 命令来自动的抓取然后合并远程分支到当前分支。

- git push remote branch：使用clone克隆时，git会默认帮你写好git push origin master。