# GitHub学习记录

## GitHub整体了解

- GitHub 与 Git 的区别 

> 在 Git 中，开发者将源代码存入名叫“Git 仓库”的资料库中并加以使用。而GitHub 则是在网络上提供 Git 仓库的一项服务。 也就是说，GitHub 上公开的软件源代码全都由 Git 进行管理。

- 在开发者之间引发化学反应的 Pull Request

> Pull Request 是指开发者在本地对源代码进行更改后，向 GitHub 中托管的 Git 仓库请求合并的功能。开发者可以在 Pull Request 上通过评论交流，例如“修正了 BUG，可以合并一下吗？”以及“我试着做了这样一个新功能，可以合并一下吗？”等。
> GitHub 的 Pull Request 不但能轻松查看源代码的前后差别，还可以对指定的一行代码进行评论

- 对特定用户进行评论

> 任务管理和 BUG 报告可以通过 Issue 进行交互。如果想让特定用户来看，只要用“ @ 用户名”的
> 格式书写，对方便会接到通知（Notifications） ，查看 Issue。

- 看到更多其他团队的软件

> 只要将感兴趣的仓库添加至 Watch 中，就可以在 News Feed 查看该仓库的相关信息 ，便能在第一时间掌握最新版本的新功能或 BUG 修正的信息。

##Git 的导入 
- 集中型与分散型版本管理

  - 以 Subversion 为代表的集中型 

    ​	将仓库集中存放在服务器之中，所以只存在一个仓库。这就是为什么这种版本管理系统会被称作集中型 

  - 以Git 为代表的分散型

    ​	GitHub 将仓库 Fork 给了每一个用户。 Fork 就是将 GitHub 的某个特定仓库复制到自己的账户下。 Fork 出的仓库与原仓库是两个不同的仓库，开发者可以随意编辑

- 安装

  - 最近的 Mac 中都预装了 Git。而各版本的 Linux 中也都以软件包（Package）的形式提供给用户了

  - 在 Windows 环境中，最简单快捷的方法是使用 msysGit

    - 设置环境变量

      ​	msysGit 中附属的 Git Bash 命令提示符，所以请选择最上面的Use Git Bash only

    - 换行符的处理

      ​	GitHub 中公开的代码大部分都是以 Mac 或 Linux 中的 LF（LineFeed）换行。然而，由于 Windows 中是以 CRLF（Carriage Return ＋Line Feed）换行的 
      ​	Git 可以通过设置自动转换这些换行符。使用 Windows 环境的各位，请选择推荐的“ Checkout Windows-style, commit Unix-style line endings”选项。换行符在签出时会自动转换为 CRLF，在提交时则会自动转换为LF。

    - 顺利安装好 msysGit 之后， Git Bash 会作为一个应用程序添加进系统，接下来请启动它。双击之后，会弹出一个名为 Git Bash 的命令提示符（图 2.6），它附属于 msysGit。如果各位是按照本书中介绍的流程进行安装，那么 git 命令就只能在 Git Bash 中使用，在 Windows 附属的命令提示符中则无法运行。 

    - 初始设置

      - 设置姓名和邮箱地址,这里设置的姓名和邮箱地址会用在 Git 的提交日志中,会在“ ~/.gitconfig”中输出设置文件
      ```c
        $ git config --global user.name "Firstname Lastname"
        $ git config --global user.email "your_email@example.com"
      ```
      - 提高命令输出的可读性

      ```c
        $ git config --global color.ui auto
      ```


## 使用GitHub的前期准备

- 分散型版本管理,原先的应用环境可能是在局域网(或者广域网)内每个开发者电脑上都有相同仓库,而GitHub则是将这个环境放在了网上,不在局限为仅仅自己的开发人员,可以选择公开代码,而且还有网友,增加社交功能
- 社区
  - Follow（关注）别人
  - Watch 功能获取最新的开发信息
- New repository创建新的仓库。
  - **Repository name** 栏中输入仓库的名称
  - **Description** 栏中可以设置仓库的说明。这一栏不是必需项，可以留空。
  - **Public、Private**在这一栏可以选择 Public 还是 Private。这里我们选择 Public，创建公开仓库，仓库内的所有内容都会被公开。
  - 在 Initialize this repository with a README 选项上打钩，随后GitHub 会自动初始化仓库并设置 README 文件，让用户可以立刻clone 这个仓库。如果想向 GitHub 添加手中已有的 Git 仓库，建议不要勾选，直接手动 push。
  - **Add.gitignore**下方左侧的下拉菜单非常方便，通过它可以在初始化时自动生成 .gitignore 文件。这个设定会帮我们把不需要在 Git 仓库中进行版本管理的文件记录在 .gitignore 文件中，省去了每次根据框架进行设置的麻烦。下拉菜单中包含了主要的语言及框架，选择今后将要使用的即可。
  - **Add a license**右侧的下拉菜单可以选择要添加的许可协议文件。如果这个仓库中包含的代码已经确定了许可协议，那么请在这里进行选择。随后将自动生成包含许可协议内容的 LICENSE 文件，用来表明该仓库内容的许可
    协议


## 通过实际操作学习 Git

- 基本操作

  - git init——初始化仓库

    ```c
    $ mkdir git-tutorial
    $ cd git-tutorial
    $ git init
    Initialized empty Git repository in /Users/hirocaster/github/github-book
    /git-tutorial/.git/
    ```

  - git status——查看仓库的状态

    尚没有可提交的内容

    ```c
    $ git status
    # On branch master
    #
    # Initial commit
    #
    nothing to commit (create/copy files and use "git add" to track)
    ```

    建立 README.md 文件

    ```c
    $ touch README.md               //该命令是增加一个文件
    $ git status					//这时候再看仓库就可以发现存在未提交的文件
    # On branch master
    #
    # Initial commit
    ## Untracked files:# (use "git add <file>..." to include in what will
    be committed)#
    # README.md
    nothing added to commit but untracked files present (use "git add" to
    track
    ```

  - git add——向暂存区中添加文件

    ```c
    $ git add README.md
    $ git status
    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    # (use "git rm --cached <file>..." to unstage)
    #
    # new file: README.md
    #
    ```

  - git commit——保存仓库的历史记录

    记述一行提交信息

    ```c
    $ git commit -m "First commit"		//-m 参数后的 "First commit"称作提交信息，是对这个提交的概述。
    [master (root-commit) 9f129ba] First commit
    1 file changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 README.md
    ```

    记述详细提交信息,直接执行 git commit命令

    ```c
    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    # (use "git rm --cached <file>..." to unstage)
    #
    # new file: README.md
    #
    ```

    在编辑器中记述提交信息的格式如下。
    ● 第一行：用一行文字简述提交的更改内容
    ● 第二行：空行
    ● 第三行以后：记述更改的原因和详细内容

    ​