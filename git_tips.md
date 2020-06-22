## Git 使用前的最小配置 

git的配置分3个级别，各项级别的配置分别保存在不同文件中。可以在git config 命令里加上如下选项来区分：  
   * --system（所有用户）  /etc/gitconfig 文件
   * --global(当前用户， 适用于该用户的所有仓库)
   * --local（仅适用于当前所在仓库） 

注意如果某个设置出现在不同级别中，则具体的级别里的值会覆盖更一般的级别里的值，即local会覆盖global，global会覆盖system。 

* 需要设置当前用户的用户名和邮箱以方便显示作者和沟通联系。此设置对该用户所有仓库有效。
```
git config --global user.name '您的名称' 
git config --global user.email '您的Email'
```

* 加--local选项后设置只对当前 Git 仓库有效，即可以为某个仓库单独设置这两项。 
```
git config --local user.name '您的名称'
git config --local user.email '您的Email'
```

### 查看 Git 的配置
* 查看所有配置项 (包括--system, --global, --local各级内容)
```
git config --list 
```
* 查看 system 类型的配置项 
```
git config --system --list  对应/etc/gitconfig 文件，需要管理员权限操作 (后两个--参数位置可以互换)  
```
* 查看 global 类型的配置项 
```
git config --global --list 对应 ~/.gitconfig 或 ~/.config/git/config 文件
```

* 查看只作用于当前仓库的配置项 
```
git config --local --list  对应该仓库下的 .git/config文件
```

* 查看某一项配置,例如用户名
```
git config user.name 
```
* 还可以加一个级别选项来过滤配置，如下面例子视图查看本仓库内该配置，如果不存在则返回空
```
git config --local user.name  对应该仓库下的 .git/config文件
```
* 如果某个配置出现在多个级别中，则会显示按照优先级而被采纳的配置值。此时可以加--show-origin选项来查看该项配置来自哪个配置文件/级别。例如下面例子查看core.filemode这个配置：
```
git config --show-origin core.filemode
显示：file:.git/config        false

```

### 清除 Git 的配置
* 清除 global 类型的配置项   
```
git config --unset  --global 某个配置项 
```

* 清除某个仓库的配置项 
```
git config --unset --local 某个配置项 
```
## 查看Git 使用帮助 
有三种等价的方法可以找到 Git 命令的综合手册（manpage），会调用本地浏览器打开该帮助网页
* git help \<verb\>
* git \<verb\> --help 我个人喜欢这种，符合Linux帮助规范
* man git-\<verb\>

如果你不需要全面的手册，只需要可用选项的快速参考，那么可以用 -h 选项获得更简明的 “help” 输出：
```
git add -h
usage: git add [<options>] [--] <pathspec>...

    -n, --dry-run         dry run
    -v, --verbose         be verbose
```

## 本地基本操作
### 获取 Git 仓库
通常有两种获取 Git 项目仓库的方式：

1. 将尚未进行版本控制的本地目录转换为 Git 仓库；  
使用git init, 将在本地文件夹内生成.git文件夹

2. 从其它服务器 克隆 一个已存在的 Git 仓库。  
使用git clone url \[本地仓库名\] 命令，(本地仓库名可以省略)。  
**注意**： 此时**不需要**先运行git init，因为clone完毕后在该仓库的根文件夹下就有一个.git文件夹。 git clone相当于连续执行了git init, git add remote URL, git fetch, git checkout这四个命令，可以看到不再需要git init。此处可以参考这个链接：[Do I need to do 'git init' before doing 'git clone' on a project
](https://stackoverflow.com/questions/22724921/do-i-need-to-do-git-init-before-doing-git-clone-on-a-project "Do I need to do 'git init' before doing 'git clone' on a project")

### 查看变更情况（显示变更状态）
* 首先了解基本的3个区域： 
   1. 工作区： 当前用户操作的文件目录。工作目录下的每一个文件都不外乎这两种状态：*已跟踪* 或 *未跟踪*。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后， 它们的状态可能是*未修改*，*已修改*或*已放入暂存区*。简而言之，已跟踪的文件就是 Git 已经知道的文件。 工作目录中除已跟踪文件外的其它所有文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有被放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态，因为 Git 刚刚检出了它们， 而你尚未编辑过它们。
   2. 暂存区： 保存暂时更改，稍后可以将此更改保存到仓库以永久保存该修改，也可以放弃该暂时更改。
   3. 仓库区： 保存确认的版本更改

* 查看变更情况
```
git status  

$ git status
On branch master  显示当前分支
Your branch is up to date with 'origin/master'.

Changes to be committed: 这里显示暂存区里为保存到仓库的变更，　可以用ｇit commit 来保存这些变更或者用git reset来撤销这些暂存的变更。
  (use "git reset HEAD <file>..." to unstage)

        modified:   git_tips.md
        new file:   newfile

Changes not staged for commit: 这里是从工作区到暂存区之间的变更，需要执行git add来保存本地变更到暂存区或者用git checkout -- <file>来撤销本地的更改。
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   git_tips.md

```
* 查看变更情况的 简略形式， 适用于文件较多时
```
git status -s 或者--short 
```
  它直接显示两列，第一列是状态，第二列是文件名
 ```
 $ git status -s
 M README   文件已修改且已暂存
MM Rakefile 文件已修，暂存后又作了修改
A  lib/git.rb 新添加到暂存区中的文件
?? LICENSE.txt 新添加的文件，未跟踪
``` 
### 使用.gitignore文件忽略对某些文件、文件夹的版本控制
* 文件名必须是.gitignore, 可以放在不同文件夹下，一般建议放在repo根目录下。以下参照 [简书-聊聊 gitignore](https://www.jianshu.com/p/f43769879bb3 "聊聊 gitignore")
* 一行对应一条规则

* 所有空行或者以 # 开头的行都会被 Git 忽略。可以随便插入若干行空行, 以 # 开头的规则被认为是注释

* 匹配是递归地应用在整个工作区中，即会试图搜索并匹配全部子文件夹里的内容。如abc会匹配abc, folder1/abc.txt, 以及名为abc的文件夹，包括abc/, t1/abc ...


* 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（*）匹配零个或多个任意字符；[abc] 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 问号（?）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到 9 的数字）。 使用两个星号（\*\*）表示匹配任意中间目录，比如 a/**/z 可以匹配 a/z 、 a/b/z 或 a/b/c/z 等。

* 匹配模式可以以（/）开头防止递归, 即匹配必须从当前文件夹开始，不迭代匹配子文件夹。 /abc 只匹配当前路径下的文件或文件夹，不匹配any/abc

* 匹配模式可以以（/）结尾指定目录。

* 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（!）取反。
* 如果你想过滤一个 以 # 开头的文件, 可以在 # 之前加一个 反斜杠\\进行转义

* 会自动去除规则前后的空格, 如果你想要过滤文件名中头尾含有空格的文件, 用 反斜杠 \\ 将规则包裹起来。
* 具体例子
```
# 这是注释, 下面一条规则会过滤 #.txt 这个文件
\#.txt

# 忽略所有的 .a 类型文件, 匹配 x.a, subfolder/y.a 等
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf

# 过滤" 123.txt" 这个文件名含空格的文件，加斜杠对空格转义
\ 123.txt


# 过滤 build 文件夹
build/
# 下面这句是无意义的, 因为父文件夹build已经被过滤了
!build/a.class
# 过滤 "!.txt" 文件, 需要\转义叹号
\!.txt
```

* 如何添加强行将某一个被忽略的文件加入版本控制中而不用修改任何现存的 gitignore  
如果只有一两个被过滤的文件想要加入版本控制中, 而你又嫌弃修改 gitignore 复杂而麻烦, 你可以使用 `git add -f mainlib.a` 加入版本控制

* 检查那条gitignor规则导致某个文件被忽略：  
`git check-ignore -v filename`


### 跟踪版本变更

* 查看当前工作在哪个分支上 
```
git branch -v  
```

* 切换到指定分支 
```
git checkout 指定分支  
```

* 把当前目录及其子目录下所有变更都加入到暂存区
```
git add .  注意这里不需要加-R等类似Linux命令参数来迭代获取所有文件夹内容
```

* 把仓库内所有变更都加入到暂存区， **这个很实用，避免手动添加多个目录** 
```
git add -A  
```

* 把指定的某些文件添加到暂存区 用空格隔开
```
git add 文件1 文件2 文件3  
```

* 创建正式的 commit，要加注释消息 
```
git commit  -m 'commit message'
```

* 比较某文件工作区和暂存区的差异 
```
git diff 某文件
```

* 比较某文件暂存区和 HEAD（当前库存储） 的差异
```
git diff --cached 某文件
```

* 比较某文件工作区和 HEAD 的差异 注意这里HEAD没有--
```
git diff HEAD 某文件
```

* 比较工作区和暂存区的所有差异
```
git diff  
```

* 比较暂存区和 HEAD 的所有差异
```
git diff --cached  
```

* 把工作区指定文件恢复成和暂存区一样
```
git checkout 文件1 文件2 文件3  
```

* 把暂存区指定文件恢复成和 HEAD 一样
```
git reset 文件1 文件2 文件3  
```

* 把暂存区和工作区所有文件恢复成和 HEAD 一样
```
git reset --hard  
```

* 用 difftool 比较任意两个 commit 的差异
```
git difftool 提交A 提交B  
```

* 查看哪些文件没被 Git 管控
```
git ls-files --others
```

#### 加塞临时任务的处理
* 把未处理完的变更先保存到 stash 中
```
git stash
```

* 临时任务处理完后继续之前未完的工作
```
git stash pop
```
  > 或者  
```
git stash apply
```
  > pop 不保留 stash，apply 保留 stash

* 查看所有 stash
```
git stash list
```

* 取回某次 stash 的变更
```
git stash pop stash@{数字n}
```

#### 修改个人分支的历史
* 修改最后一次 commit
```
1）在工作区修改文件
2）git add .
3）git commit --amend
```

* 修改中间的 commit (代号X)
```
1）git rebase -i X前面一个commit的id
2）在工作区修改文件
3）git add .
4）git rebase --continue
后续可能需要处理冲突，直到 rebase 结束
```

#### 查看变更的历史
* 当前分支各个 commit 用一行显示
```
git log --oneline
```

* 显示就近的 n 个 commit
```
git log -n
```

* 用图示显示所有分支的历史
```
git log --oneline --graph --all
```

* 查看涉及到某文件变更的所有 commit
```
git log 某文件
```

* 某文件各行最后修改对应的 commit 以及作者
```
git blame 某文件
```
#### 分支与标签
* 基于当前分支创建新分支
```
git branch 新分支
```

* 基于指定分支创建新分支
```
git branch 新分支 已有分支 
```

* 基于某个 commit 创建分支
```
git branch 新分支 某个commit的id
```

* 创建分支并切换到该分支
```
git checkout -b 新分支
```

* 列出本地分支
```
git branch -v
```

* 列出本地和远端分支
```
git branch -av
```

* 列出远端所有分支
```
git branch -rv
```

* 列出名称符合某样式的远端分支
```
git branch -rv -l '某样式'
```

* 安全删除本地某分支
```
git branch -d 拟删除分支
```

* 强行删除本地某分支
```
git branch -D 拟删除分支
```

* 删除已合并到 master 分支的所有本地分支
```
git branch --merged master | grep -v '^\*\|  master' | xargs -n 1 git branch -d
```

* 删除远端 origin 已不存在的所有本地分支
```
git remote prune origin
```

* 给 commit 打上标签
```
git tag 标签名 commid的id
```

#### 两分支之间的集成
* 把A分支合入到当前分支，且为 merge 创建 commit
```
git merge A分支
```

* 把A分支合入到B分支，且为 merge 创建 commit
```
git merge A分支 B分支
```

* 把当前分支基于B分支做 rebase，以便把B分支合入到当前分支
```
git rebase B分支
```

* 把A分支基于B分支做 rebase，以便把B分支合入到A分支
```
git rebase B分支 A分支
```

* 用 mergetool 解决冲突
```
git mergetool
```

#### 和远端的交互
* 列出所有 remote
```
git remote -v
```

* 增加 remote
```
git remote add url地址
```

* 删除 remote
```
git remote remove remote的名称
```

* 改变 remote 的 name
```
git remote rename 旧名称 新名称
```

* 把远端所有分支和标签的变更都拉到本地
```
git fetch remote
```

* 把远端分支的变更拉到本地,且 merge 到本地分支
```
git pull remote名称 分支名
```

* 把本地分支 push 到远端
```
git push remote名称 分支名
```

* 删除远端分支
```
git push remote --delete 远端分支名 
或者
git push remote :远端分支名
```

* 向远端提交指定标签
```
git push remote 标签名
```

* 向远端提交所有标签
```
git push remote --tags
```


