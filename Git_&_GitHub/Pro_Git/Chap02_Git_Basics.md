
# 2. Git 基础
## 2.1 获取Git仓库
通常有两种获取 Git 项目仓库的方式：
1. 将尚未进行版本控制的本地目录转换为 Git 仓库；
2. 从其它服务器 克隆 一个已存在的 Git 仓库。
两种方式都会在你的本地机器上得到一个工作就绪的 Git 仓库。


### 2.1.1. 在已存在目录中初始化仓库
如果你有一个尚未进行版本控制的项目目录，想要用 Git 来控制它，那么首先需要进入该项目目录，然后执行。

```bash
$ git init
```

该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪。

然后需要通过以下两条命令追踪文件和进行提交
```bash
$ git add <Files>
$ git commit -m <description>
```


### 2.2.2. 克隆现有的仓库
如果你想获得一份已经存在了的 Git 仓库的拷贝，比如说，你想为某个开源项目贡献自己的一份力，这时就要用到 `git clone` 命令。

克隆仓库的命令是

```bash
$ git clone <url>
```

Git支持多种数据传输协议：

* `https://`协议
* `git://`协议
* SSH传输协议


## 2.2 记录每次更新到仓库
如果我们的机器本地有个项目的仓库，通常，我们会对这些文件做些修改，每当完成一个阶段的目标，想要记录下变化时，就将它提交到仓库。

工作目录下的每一个文件都不外乎这两种状态：**已跟踪**或**未跟踪**。

**已跟踪的文件**是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后， 它们的状态可能是未修改，已修改或已放入暂存区。简而言之，已跟踪的文件就是 Git 已经知道的文件。

工作目录中除已跟踪文件外的其它所有文件都属于**未跟踪文件**，它们既不存在于上次快照的记录中，也没有被放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态，因为 Git 刚刚检出了它们， 而你尚未编辑过它们。

编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。 在工作时，你可以选择性地将这些修改过的文件放入暂存区，然后提交所有已暂存的修改，如此反复。

![Git Life Cycle](./../images/GitLifeCycle.png)


### 2.2.1. 检查当前文件状态
可以用`git status`命令查看都有哪些文件，又都处于什么状态（比如track，untrack，modified等等）。


### 2.2.2. 跟踪新文件
使用命令 `git add <Files/Dirs>` 开始跟踪一个文件。如果参数是目录的路径，该命令叫递归地跟踪该目录下的所有文件。


### 2.2.3. 暂存以修改的文件
当处于暂存区的文件发生修改之后，新的修改还未放到暂存区。如果要暂存新的修改，需要再次执行 `git add <file>` 命令。这个命令是多功能的：可以开始跟踪新文件，或把已跟踪的文件放到暂存区，或用于合并时把有冲突的文件标记为已解决状态等。可以将该命令理解为 `精确地修改内容添加到下一次提交(commit)中。`


### 2.2.4 状态简览
`git status` 命令的输出十分详细，但其用语有些繁琐。 Git 有一个选项可以帮你缩短状态命令的输出，这样可以以简洁的方式查看更改。 如果你使用 `git status -s` 命令或 `git status --short` 命令，你将得到一种格式更为紧凑的输出。
```bash
$ git status
$ git status -s
$ git status --short
```


### 2.2.5. 忽略文件
一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。

文件 `.gitignore` 的格式规范如下：
* 所有空行或者以 # 开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
* 匹配模式可以以（/）开头防止递归。
* 匹配模式可以以（/）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号 `*` 匹配零个或多个任意字符； `[abc]` 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 问号 `?` 只匹配一个任意字符； 如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号 `**` 表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等。

**Note:** GitHub 有一个十分详细的针对数十种项目及语言的 `.gitignore` 文件列表， 你可以在 https://github.com/github/gitignore 找到它。


### 2.2.6. 查看已暂存和未暂存的修改
如果 `git status` 命令的输出对于你来说过于简略，而你想知道具体修改了什么地方，可以用 `git diff` 命令。 通常可能会用该命令来回答这两个问题： 当前做的哪些更新尚未暂存？ 有哪些更新已暂存并准备好下次提交？ 虽然 `git status` 已经通过在相应栏下列出文件名的方式回答了这个问题，但 `git diff` 能通过文件补丁的格式更加具体地显示哪些行发生了改变。

若要查看已暂存的将要添加到下次提交里的内容，可以用 `git diff --staged` 命令。这条命令将比对已暂存文件与最后一次提交的文件差异。

请注意，`git diff` 本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件，运行 `git diff` 后却什么也没有，就是这个原因。

用 `git diff --cached` 查看已经暂存起来的变化（ `--staged` 和 `--cached` 是同义词）。


### 2.2.7. 提交更新
现在的暂存区已经准备就绪，可以提交了。 在此之前，请务必确认还有什么已修改或新建的文件还没有 git add 过， 否则提交的时候不会记录这些尚未暂存的变化。 这些已修改但未暂存的文件只会保留在本地磁盘。 所以，每次准备提交前，先用 git status 看下，你所需要的文件是不是都已暂存起来了， 然后再运行提交命令 `git commit`：
```bash
$ git commit -m <discription>
```
请记住，提交时记录的是放在暂存区域的快照。 任何还未暂存文件的仍然保持已修改状态，可以在下次提交时纳入版本管理。


### 2.2.8. 跳过使用暂存区
尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。 Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` 加上 `-a` 选项，Git 就会**自动把所有已经跟踪过的文件暂存起来一并提交**，从而跳过 `git add` 步骤。


### 2.2.9. 移除文件
要从 Git 中移除某个文件，就必须要**从已跟踪文件清单中移除（确切地说，是从暂存区域移除）**，然后提交。 可以用 `git rm` 命令完成此项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了。

如果只是简单地从工作目录中手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分（也就是 未暂存清单）看到变化，然后再运行 `git rm` 记录此次移除文件的操作。

另外一种情况是，我们想把文件**从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中**。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 当你忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时，这一做法尤其有用。 为达到这一目的，使用 `--cached` 选项。
```bash
git rm --cached <Files>
```


### 2.2.9 移动文件
要在 Git 中对文件改名，可以这么做：
```bash
$ git mv file_from file_to
```
其实，运行 `git mv` 就相当于运行了下面三条命令：
```bash
$ mv README.md README
$ git rm README.md
$ git add README
```