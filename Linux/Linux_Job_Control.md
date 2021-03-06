# [Linux Job Control](https://www.cnblogs.com/sparkdev/p/11980732.html)

执行一个命令会创建一个或多个进程，这些进程被称为一个进程组(process group)。进程组中包含一个或多个进程，每个进程都会属于一个进程组，进程组也叫 job。


### Ctrl + z
严格来说 `ctrl + z` 并不是一个 job 管理命令，它只是向当前进程发送一个 `SIGSTOP` 信号，该信号使进程进入暂停(stopped)状态，也就是挂起进程，此状态下，进程状态会被系统保存，此进程会被放置到作业队列中去，从而让出终端。
使用 `ctrl + z` 我们可以暂停正在占用终端的进程而不结束它，然后我们可以使用终端命令来操作此进程。

## 杀死进程
有时候使用 ctrl + c 无法杀死一个正在运行的前台进程，这是因为 ctrl + c 的本质是向进程发送 SIGINT 信号。SIGINT 是用来终止进程的，但是这是一个可以被忽略的信号，如果程序忽略了它，我们就无法通过 ctrl + c 来终止该进程。

此时我们可以先使用 ctrl + z 把进程切换到后台，然后使用 kill %n(n 为进程的 jobID)来终止进程。kill 命令默认向进程发送 SIGTERM 信号，程序一般会在 SIGTERM 信号的处理函数中正常地终止程序并执行资源清理工作。既然 SIGTERM 信号能够被程序处理，那么它也能够被忽略，所以也无法通过这种方式结束那些顽固的进程。

杀死进程的终极手段是 kill -SIGKILL PID(kill -9 PID)。SIGKILL 信号是不能被忽略的，所以这一招肯定管用。但是由于它过于强硬，使用这种方式杀死进程后往往会有后遗症，比如进程使用的资源没有在退出前清理干净，常见的例子是用这种方法杀死 vim 进程后会遗留下 .swp 文件。


