# Git中文教程

## GIT对象模型

所有用来表示项目历史信息的文件，是通过一个40个字符的“对象名”（哈希值）来索引的。

每个对象包括三个部分：类型、大小和内容；有四种类型的对象：blob,tree,commit,tag。

+ “blob”用来存储文件数据，通常是一个文件。
+ “tree”有点象一个目录，它管理一些“tree”或是“blob”（就象文件和子目录）。
+ 一个“commit”只指向一个"tree"，它用来标记项目某一个特定时间点的状态。它包括一些关于时间点的元数据，如时间戳、最近一次提交的作者、指向上次提交（commits）的指针等等。
+ 一个“tag”是来标记某一个提交(commit) 的方法。

与SVN的区别：Git与你熟悉的大部分版本控制系统的差别是很大的。也许你熟悉Subversion、CVS、Perforce、Mercurial 等等，他们使用 “增量文件系统” （Delta Storage systems）, 就是说它们存储每次提交(commit)之间的差异。Git正好与之相反，它会把你的每次提交的文件的全部内容（snapshot）都会记录下来。这会是在使用Git时的一个很重要的理念。

blob对象：一个"blob对象"就是一块二进制数据，它没有指向任何东西或有任何其它属性，甚至连文件名都没有。因为blob对象内容全部都是数据，如两个文件在一个目录树（或是一个版本仓库）中有同样的数据内容，那么它们将会共享同一个blob对象。Blob对象和其所对应的文件所在路径、文件名是否被更改都完全没有关系。

一个tree对象有一串(bunch)指向blob对象或是其它tree对象的指针，它一般用来表示内容之间的目录层次关系。git show命令还可以用来查看tree对象，但是git ls-tree能让你看到更多的细节。

 一个提交(commit)由以下的部分组成:

+  一个 tree　对象: tree对象的SHA1签名, 代表着目录在某一时间点的内容。
+ 父对象 (parent(s))：提交(commit)的SHA1签名代表着当前提交前一步的项目历史。上面的那个例子就只有一个父对象;合并的提交(merge commits)可能会有不只一个父对象。如果一个提交没有父对象, 那么我们就叫它“根提交"(root commit),它就代表着项目最初的一个版本(revision). 每个项目必须有至少有一个“根提交"(root commit). 一个项目可能有多个"根提交“，虽然这并不常见(这不是好的作法)。
+ 作者 : 做了此次修改的人的名字,　还有修改日期。
+ 提交者（committer): 实际创建提交(commit)的人的名字, 同时也带有提交日期。他可能会和作者不是同一个人; 例如作者写一个补丁(patch)并把它用邮件发给提交者,由他来创建提交(commit)。

一般用 git commit 来创建一个提交(commit),这个提交(commit)的父对象一般是当前分支(current HEAD),　同时把存储在当前索引(index)的内容全部提交。

标签对象（tag）：一个标签对象包括一个对象名(译者注:就是SHA1签名),对象类型,标签名, 标签创建人的名字("tagger"),还有一条可能包含有签名(signature)的消息。你可以用 git cat-file 命令来查看这些信息。

GIT目录与工作目录

GIT索引：Git索引是一个在你的工作目录和项目仓库间的暂存区(staging area)。有了它,你可以把许多内容的修改一起提交(commit)。 如果你创建了一个提交(commit),那么提交的是当前索引(index)里的内容, 而不是工作目录中的内容。

使用 git status 命令是查看索引内容的最简单办法。 你运行 git status命令, 就可以看到: 哪些文件被暂存了(就是在你的Git索引中), 哪些文件被修改了但是没有暂存, 还有哪些文件没有被跟踪(untracked)。

## 安装初始化

Windows 下安装 msysgit 就可以了

Git配置

    $ git config --global user.name "Scott Chacon"
    $ git config --global user.email "schacon@gmail.com"
    
执行了上面的命令后,会在你的主目录(home directory)建立一个叫 ~/.gitconfig 的文件。 内容一般像下面这样:

    [user]
    name = Scott Chacon
    email = schacon@gmail.com

如果你想使项目里的某个值与前面的全局设置有区别(例如把私人邮箱地址改为工作邮箱);你可以在项目中使用git config 命令不带 --global 选项来设置. 这会在你项目目录下的 .git/config 文件增加一节[user]内容(如上所示).

## 基本用法

### 获得一个GIT仓库，有两种方法

1.   Clone一个仓库

        git clone git://git.kernel.org/pub/scm/git/git.git
        git clone http://www.kernel.org/pub/scm/git/git.git

    在默认情况下，Git会把"Git URL"里目录名的'.git'的后辍去掉,做为新克隆(clone)项目的目录名

2.   初始化一个新的仓库

        $ cd project
        $ git init

    Git会输出:Initialized empty Git repository in .git/。如果你仔细观查会发现project目录下会有一个名叫”.git” 的目录被创建，这意味着一个仓库被初始化了

### 正常的工作流程

+   修改文件，将它们更新的内容添加到索引中.

        $ git add file1 file2 file3

+   查看文件被修改的状态

        $ git diff --cached

    如果没有--cached参数，git diff 会显示当前你所有已做的但没有加入到索引里的修改。你也可以用git status命令来获得当前项目的一个状况
    
        $ git status
        # On branch master
        # Changes to be committed:
        #   (use "git reset HEAD <file>..." to unstage)
        #
        #   modified:   file1
        #   modified:   file2
        #   modified:   file3
        #
        
+  修改做完后就把新修改的文件加入到索引中，最后把他们提交：

        $ git commit

    这会提示你输入本次修改的注释，完成后就会记录一个新的项目版本。  
    也可以使用如下命令提交：

        $ git commit -a

    这会自动把所有内容被修改的文件(不包括新创建的文件)都添加到索引中，并且同时把它们提交。

一个技巧：commit注释最好以一行短句子作为开头，来简要描述一下这次commit所作的修改(最好不要超过50个字符)；然后空一行再把详细的注释写清楚。这样就可以很方便的用工具把commit注释变成email通知，第一行作为标题，剩下的部分就作email的正文.

### Git跟踪的是内容不是文件

git add 不但是用来添加不在版本控制中的新文件，也用于添加已在版本控制中但是刚修改过的文件; 在这两种情况下,Git都会获得当前文件的快照并且把内容暂存(stage)到索引中，为下一次commit做好准备。

### 分支与合并@基础

+   创建和得到仓库中存在的所有分支列表

        $ git branch experimental   # 创建一个新的叫“experimental”的分支
        $ git branch                # 得到仓库中的所有分支，星号标识代表你当前工作的分支

+   切换分支

        $ git checkout experimental # 切换到“experimental”分支

+   合并分支

        $ git merge experimental    # 合并experimental分支到当前分支

    如果这个两个分支间的修改没有冲突(conflict),那么合并就完成了。如有冲突（冲突--同一个文件在远程分支和本地分支里按不同的方式被修改了），输入下面的命令就可以查看当前有哪些文件产生了冲突:

        $ git diff

    当你编辑了有冲突的文件（在有问题的文件上会有冲突标记，你所需要的做是就是编辑解决冲突，接着把冲突标识符删掉），解决了冲突后就可以提交了：

        $ git commit -a

    提交(commit)了合并的内容后就可查看一下:

        $ gitk

    执行了gitk后会有一个很漂亮的图形的显示项目的历史。这时你就可以删除掉你的 “experimental” 分支了(如果愿意)：

        $ git branch -d experimental

    git branch -d只能删除那些已经被当前分支的合并的分支. 如果你要强制删除某个分支的话就用git branch –D；下面假设你要强制删除一个叫”crazy-idea”的分支：

        $ git branch -D crazy-idea

    分支是很轻量级且容易的，这样就很容易来尝试它。
    
+   撤销一个合并

        $ git reset --hard HEAD
        $ git reset --hard ORIG_HEAD    # 这条命令在某些情况会很危险，如果你把一个已经被另一个分支合并的分支给删了，那么 以后在合并相关的分支时会出错。
    
+   快速向前合并

    如果当前的分支和另一个分支没有内容上的差异，就是说当前分支的每一个提交(commit)都已经存在另一个分支里了，git 就会执行一个“快速向前"(fast forward)操作；git 不创建任何新的提交(commit),只是将当前分支指向合并进来的分支。

### 查看历史--GIT日志

git log命令可以显示所有的提交(commit)

    $ git log v2.5.. Makefile fs/   # 找出所有从"v2.5“开始在fs目录下的所有Makefile的修改
    $ git log -p                    # 显示补丁（patchs）

+   日志统计

        $ git log --stat            # 显示在每个提交(commit)中哪些文件被修改了, 这些文件分别添加或删除了多少行内容

+   格式化日志

        $ git log --pretty=oneline  # --pretty参数可以使用的选项有oneline,short,也可以使用medium,full,fuller,email,raw

    如果这些格式不完全符合你的相求， 你也可以用'--pretty=format'参数(参见：git log)来创建你自己的“格式”。

        $ git log --pretty=format:'%h was %an, %ar, message: %s'

    你也可以用'--graph'选项来可视化你的提交图(commit graph),就像下面这样，它会用ASCII字符来画出一个很漂亮的提交历史(commit history)线。

        $ git log --pretty=format:'%h : %s' --graph

+   日志排序

    按默认情况，提交(commits)会按逆时间(reverse chronological)顺序显示。但可以通过指定'--topo-order'参数来改变显示顺序。

        $ git log --pretty=format:'%h : %s' --topo-order --graph

    '--topo-order'：会让提交(commits)按拓朴顺序来显示
    '--date-order'：显示提交日志的顺序主要按提交日期来排序
    '--reverse'：逆向显示所有日志

### 比较提交--GIT DIFF

+   你可以用 git diff 来比较项目中任意两个版本的差异。

        $ git diff master..test         # 比较master与test两个分支的差异
        $ git diff master...test        # 比较master与test共有的父分支与test的差异

+   哪些内容会被提交(commit)

    你通常用git diff来找你当前工作目录和上次提交与本地索引间的差异。

        $ git diff                  # 当前工作目录中，没有添加到索引中，在下次提交时不会被提交的修改。
        $ git diff --cached         # 当前工作目录中，当前索引与上次提交间的差异
        $ git diff HEAD             # 显示当前工作目录与上次提交时之间的所有差别

        $ git diff test             # 显示当前工作目录与test分支的差别，也可以加上路径限定符，来只比较某一个文件或目录
        $ git diff HEAD -- ./lib    # 显示你当前工作目录下的lib目录与上次提交之间的差别(或者更准确的说是在当前分支)
        $ git diff --stat           # 仅统计一下有哪些文件被改动，有多少行被改动

### 分布式工作流程

+   涉及到的一些命令

        $ git clone /home/alice/project myrepo

        $ cd /home/alice/project
        $ git pull /home/bob/myrepo master

        $ git remote add bob /home/bob/myrepo
        $ git fetch bob
        $ git log -p master..bob/master
        $ git merge bob/master
        $ git pull remotes/bob/master

        $ git clone alice.org:/home/alice/project myrepo

+   公共Git仓库

    git命令里的仓库地址也可以是本地 的某个目录名：

        $ git clone /path/to/repository
        $ git pull /path/to/other/repository

    也可以是一个ssh地址：

        $ git clone ssh://yourhost/~you/repository

+   将修改推到一个公共仓库

        $ git push ssh://yourserver.com/~you/proj.git master

+   别名

        $ cat >>.git/config <<EOF
        [remote "public-repo"]
            url = ssh://yourserver.com/~you/proj.git
        EOF

    这样，就可以使用'public-repo'来代替'ssh://yourserver.com/~you/proj.git'
        

### GIT标签

+   轻量级标签

        $ git tag stable-1 1b2e1d63ff

    这样，我们可以用stable-1 作为提交(commit) "1b2e1d63ff" 的代称(refer)。

+   标签对象

        $ git tag -a stable-1 1b2e1d63ff

+   签名的标签：在你的 .git/config 或 ~.gitconfig里配好key

        [user]
            signingkey = <gpg-key-id>
    
    也可以使用命令行配置Key

        $ git config (--global) user.signingkey <gpg-key-id>
    
    现在你可以创建“签名的标签”

        $ git tag -s stable-1 1b2e1d63ff    
        $ git tag -u <gpg-key-id> stable-1 1b2e1d63ff   # 如果没有在配置文件中配GPG key,你可以用"-u“ 参数直接指定

## 中级技能

### 忽略某些文件

项目中经常会生成一些Git不需要追踪的文件。可以在顶层工作目录中添加一个叫'.gitignore'的文件，来告诉Git系统要忽略掉哪些文件。下面是文件内容示例：

    # 以'#'开始的行，被视为注释。
    # 忽略掉所有文件名是foo.txt的文件
    foo.txt
    # 忽略所有生成的html文件
    *.html
    # foo.html是手工维护的，所以例外。
    !foo.html
    # 忽略所有 .o 和 .a 的文件。
    *.[oa]

也可以把'.gitignore'文件放到工作树里的其他目录中，这样就会在它和它的子目录起忽略指定文件作用。'.gitignore'文件同样可以象其他文件一样加载到项目仓库里，这样项目的其它开发者也能共享同一套忽略文件规则。

如果你想忽略规则只对特定的仓库起作用,你可以把这些忽略规则写到你的仓库下 .git/info/exclude文件中，或是写在Git配置变量core.excludesfile中指定的文件里。

### REBASE

与git merge不同处在于，git merge 将保留所有分支的commit，而git rebase将只保留主发展分支，其它被合并的分支应该被删除，就象从来没有发生过一样。

相关命令：

    $ git checkout -b mywork origin         # 基于远程分支'origin'，创建一个叫'mywork'的分支
    ......                                  # 对这个分支做一些修改
    $ git merge origin
    $ git rebase origin                     # 这会把你mywork分支里的每个提交都取消掉，并且把它们临时保存为补丁(patch，这些补丁放在'.git/rebase'目录中)，然后把mywork分支更新到最新的origin分支，最后把保存的这些补丁应用到mywork分支中。

### 交互式REBASE

如果你想在rebase的过程中对一部分提交进行修改，你可以在'git rebase'命令中加入'-i'或'--interactive'参数去调用交互模式。

    $ git rebase -i origin/master
    $ git log github/master

### 交互式添加

交互式添加提供友好的界面去操作Git索引（index），同时亦提供了可视化索引的能力。只需简单键入'git add -i'，即可使用此功能。

    $>git add -i

### 储藏

