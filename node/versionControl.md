
[mac下使用nvm进行node版本控制](https://segmentfault.com/a/1190000004404505)

## 前言
目前主流的node版本管理工具有两种，nvm和n。
两者差异挺大的，具体分析可以参考一下淘宝FED团队的一篇文章：

管理 node 版本，选择 nvm 还是 n？

总的来说，nvm有点类似于 Python 的 virtualenv 或者 Ruby 的 rvm，每个node版本的模块都会被安装在各自版本的沙箱里面（因此切换版本后模块需重新安装），因此考虑到需要时常对node版本进行切换测试兼容性和一些模块对node版本的限制，我选择了使用nvm作为管理工具，下面就来说说nvm的安装和使用过程。

## 安装
一开始，我像往常一样在 oh-my-zsh 中选择了使用 Homebrew 来安装nvm，完成后却发现它被全局安装了，这就造成了后面使用上的一点问题，具体问题可参考Github上的一个issue。

然后我就又用Homebrew把它给卸掉了，还是决定找找看它的文档，Github上找到这里。

清楚看到文档中Installation 那一小节倒数第二行有一句Homebrew installation is not supported.，由此可见，没事儿还是要多看看文档。

正式进入安装过程：

### 我选择了使用curl方式来安装：

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.30.2/install.sh | bash
完成后nvm就被安装在了~/.nvm下啦，接下来就需要配一下环境变量了，如果你也使用了zsh的话，就需要在~/.zshrc这个配置文件中配置，否则就找找看~/.bash_profile或者~/.profile吧。

### 打开~/.zshrc，在最后一行加上：

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh" # This loads nvm
```

这一步的作用是每次新打开一个bash，nvm都会被自动添加到环境变量中了。
完成后输入source ~/.zshrc重新启动一下配置。
输入nvm可以看到如下信息：

```shell
➜  ~  nvm

Node Version Manager

Note: <version> refers to any version-like string nvm understands. This includes:
  - full or partial version numbers, starting with an optional "v" (0.10, v0.1.2, v1)
  - default (built-in) aliases: node, stable, unstable, iojs, system
  - custom aliases you define with `nvm alias foo`

Usage:
  nvm help                                  Show this message
  nvm --version                             Print out the latest released version of nvm
  nvm install [-s] <version>                Download and install a <version>, [-s] from source. Uses .nvmrc if available
    --reinstall-packages-from=<version>     When installing, reinstall packages installed in <node|iojs|node version number>
  nvm uninstall <version>                   Uninstall a version
  nvm use [--silent] <version>              Modify PATH to use <version>. Uses .nvmrc if available
  nvm exec [--silent] <version> [<command>] Run <command> on <version>. Uses .nvmrc if available
  nvm run [--silent] <version> [<args>]     Run `node` on <version> with <args> as arguments. Uses .nvmrc if available
  nvm current                               Display currently activated version
  nvm ls                                    List installed versions
  nvm ls <version>                          List versions matching a given description
  nvm ls-remote                             List remote versions available for install
  nvm version <version>                     Resolve the given description to a single local version
  nvm version-remote <version>              Resolve the given description to a single remote version
  nvm deactivate                            Undo effects of `nvm` on current shell
  nvm alias [<pattern>]                     Show all aliases beginning with <pattern>
  nvm alias <name> <version>                Set an alias named <name> pointing to <version>
  nvm unalias <name>                        Deletes the alias named <name>
  nvm reinstall-packages <version>          Reinstall global `npm` packages contained in <version> to current version
  nvm unload                                Unload `nvm` from shell
  nvm which [<version>]                     Display path to installed node version. Uses .nvmrc if available

Example:
  nvm install v0.10.32                  Install a specific version number
  nvm use 0.10                          Use the latest available 0.10.x release
  nvm run 0.10.32 app.js                Run app.js using node v0.10.32
  nvm exec 0.10.32 node app.js          Run `node app.js` with the PATH pointing to node v0.10.32
  nvm alias default 0.10.32             Set default node version on a shell

Note:
  to remove, delete, or uninstall nvm - just remove the `$NVM_DIR` folder (usually `~/.nvm`)
```

至此，nvm的安装就算是告一段落了，下面我们就来看一下如何使用它来对node版本进行切换使用。

## 使用
其实，上述最后一步打印出的信息已经可以清楚看到nvm的全部命令。
各项命令的详细用法及含义可参考上文提到的nvm文档。

接下来就轮到安装我们的主角node大哥了。

首先我们可以使用nvm ls-remote看一下node有哪些版本可以安装：

```
➜  ~  nvm ls-remote
            v0.1.14
            v0.1.15
            v0.1.16
            ...
```

Note：由于在写文章的时候网络有问题，无法展示所有结果出来，上面代码示意一下，正确情况下会显示非常多版本出来，大家都知道node更新速度非常非常快。

我常用的node版本是4.1.0，因此首先安装这个版本：

$ nvm install v4.1.0
######################################################################## 100.0%

Now using node v4.1.0
同时安装一下最新版来测试nvm的版本管理功能：

$ nvm install v5.5.0
######################################################################## 100.0%

Now using node v5.5.0
使用nvm轻松切换node版本
在介绍使用方法前，简单说明一下nvm的工作原理：

按照我上述安装方法的话，nvm会将各个版本的node安装在~/.nvm/versions/node目录下，我们可以打开这个目录看看有些什么东西：

➜  ~  ls -a ~/.nvm/versions/node
.      ..     v4.1.0 v5.5.0
事实上v4.1.0和v5.5.0这两个目录分别存放node的binary档，nvm会在$PATH前面安插指定版本的目录，透过这种方式在使用node命令时就会用指定版本的node来运行了。

可以确认实际的$PATH看看：

➜  ~  echo $PATH
/Users/***/.nvm/versions/node/v5.5.0/bin:...
由于刚刚我们通过nvm安装node，会自动把最后安装的版本设为当前使用的版本，因此上述路径结尾会是.../v5.5.0/bin（还可通过nvm ls命令查看当前已安装的所有node版本）。

## 接下来我们可以使用nvm use <version>切换版本：

➜  ~  nvm use v4.1.0
Now using node v4.1.0 (npm v2.14.3)
➜  ~  node -v
v4.1.0
➜  ~  nvm use v5.5.0
Now using node v5.5.0 (npm v3.3.12)
➜  ~  node -v
v5.5.0
轻轻松松完成切换！

## 指定默认版本

不过问题来了，这时如果你新打开一个bash，输入nvm current会发现显示为null，这是因为使用nvm use命令只会在当前bash里生效，重新打开一个bash你会发现$PATH的值已经不包含刚才的node目录了，要解决这个问题也很简单，使用nvm alias default <version>命令来指定一个默认的node版本就ok了，这里我使用了v4.1.0作为默认版本：

➜  ~  nvm alias default v4.1.0
default -> v4.1.0
此时再打开一个bash输入nvm current就会显示为v4.1.0了。