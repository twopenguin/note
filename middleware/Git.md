## Git 初始化一个仓库

### git init

对现有的某个项目开始用 Git 管理，只需到此项目所在的目录，执行： 

```shell
git init
```

初始化后，在当前目录下会出现一个名为 .git 的目录，所有 Git 需要的数据和资源都
存放在这个目录中。不过目前，仅仅是按照既有的结构框架初始化好了里边所有的文件和目
录，但我们**还没有**开始**跟踪**管理项目中的任何一个文件。 



如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对
这些文件进行跟踪，然后提交： 

```shell
$ git add *.c
$ git add README
$ git commit -m 'initial project version'
```

### 从现有仓库克隆 

如果想对某个开源项目出一份力，可以先把该项目的 Git 仓库复制一份出来，这就需要
用到 git clone 命令。如果你熟悉其他的 VCS 比如 Subversion，你可能已经注意到这里
使用的是 clone 而不是 checkout。 

克隆仓库的命令格式为 git clone [url]。比如，要克隆 Ruby 语言的 Git 代码仓库
Grit，可以用下面的命令： 

```shell
$ git clone git://github.com/schacon/grit.git
```

如果希望在克隆的时候，自己定义要新建的项目目录名称，可以在上面的命令最后指定 :

```shell
$ git clone git://github.com/schacon/grit.git mygrit
```

## 常用命令

### git add 文件名(跟踪新文件 )

**只要在 “Changes to be committed” 这行下面的，就说明是已暂存状态** 

**文件 benchmarks.rb 出现在 “Changed but not updated” 这行下面，说明已跟踪文件**
**的内容发生了变化，但还没有放到暂存区** 

### git branch -a（查看所有分支，包括远程）

### git checkout [分支名] 切换分支

### git checkout -b <分支名> 创建并切换分支

### git commit(提交暂存区到仓库)

git commit -m “这是提交的什么内容！”

### git commit -a(跳过暂存，直接提交)

尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。Git 提
供了一个跳过使用暂存区域的方式，只要在提交的时候，给 git commit 加上 -a 选项，Git
就会**自动把所有已经跟踪过的文件暂存起来一并提交**，从而跳过 git add 步骤： 

### 忽略某些文件 

我们可以创建一个名为**.gitignore** 的文件，列出要忽略的文件模式，来看一个简单的例子： 

```shell
$ cat .gitignore
*.[oa]
*~
```

第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件。第二行告诉 Git 忽略所有以波浪符（~）结尾的文件，**要养成一开始就设置好.gitignore 文件的习惯**，以免将来误提交这类无用的文件。

文件 .gitignore 的格式规范如下：

• 所有空行或者以注释符号 ＃ 开头的行都会被 Git 忽略。

• 可以使用标准的 glob 模式匹配。

• 匹配模式最后跟反斜杠（/）说明要忽略的是目录。

• 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（!）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。星号（*）匹配零个或多个任  意字符； [abc] 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一  个 b，要么匹配一个 c）；问号（?）只匹配一个任意字符；如果在方括号中使用短划线分  隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 [0-9] 表示匹配所有 0 到  9 的数字）

我们再看一个 .gitignore 文件的例子： 

```shell
# 此为注释 – 将被 Git 忽略
*.a # 忽略所有 .a 结尾的文件
!lib.a # 但 lib.a 除外
/TODO # 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
build/ # 忽略 build/ 目录下的所有文件
doc/*.txt # 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```

### git diff 

此命令比较的是工作目录中当前文件和暂存区域快照之间的差异，也就是修改之后还没有
暂存起来的变化内容 

### git diff --cached

看已经暂存起来的文件和上次提交时的快照之间的差异 



### git rm(把文件从版本控制中移除)

####参数解释：

-r  递归移除目录，(Allow recursive removal when a leading directory name is given.)
-n：加上这个参数，执行命令时，是不会删除任何文件，而是展示此命令要删除的文件列表预览，所以一般用这个参数先看看要删除哪些文件，防止误删，确认之后，就去掉此参数，真正的删除文件。  

--cache：如果加了这个参数就是从版本控制移除，但是本地保留

-f：强制删除

#### 常见用法

git rm --cached a.a 移除文件(只从暂存区中删除)
git commit -m "remove" 移除文件(从Git中删除)
git rm -f a.a 强行移除修改后文件(从暂存区和工作区中删除)

### git status(查看状态)

**就是在“Untracked files”这行下面。Git 不会自动将之纳入跟踪范围** 

### 克隆远程某个特定分支

```shell
git clone -b <branch name> [remote repository address]
```

举例：

```shell
git clone -b reconsitution/dev http://zhangyida@www.vc.git.51yiy.top/r/vc-business.git
```

####  

##常见错误

###SSL_connect

错误描述：

git push github失败，提示：SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443

今天用IntelliJ IDEA提交完代码后，偶然之间发现没有push到Github上，再仔细一看，之前好几次的push都是失败的（呜呜，我的Contribution） 。报错都是一样的：

Push failed: Failed with error: unable to access 'https://github.com/weidongcao/bigdata/': 
OpenSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443
1
2
然后再换sourcetree和git命令行提交都提交不了， 
然后去网上根据这个错误提示搜了一下，发现是因为Git的Http代理的问题，Git支持三种协议：git://、ssh://和http://，本来push的时候应该走ssh隧道的，但是因为设置了http代理，所以就走了http的代理，于是就提交不了了。 

OK，找到原因了，那就取消http代理吧：

解决办法
在github项目在本地的根目录下打开git命令行， 
执行下面的命令：

```shell
git config --global --unset http.proxy
```

然后再次使用IntelliJ IDEA push，OK，问题解决，本来还想着需要再设置一下ssh呢，没有想到不用 





