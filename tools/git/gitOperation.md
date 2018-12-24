## git 拉取其他分支的某些文件到本分支

<p class="tip">
  <strong>某些情况下，我们可能希望拉取远程分支或本地分支的某个文件到本分支，当然拉取文件夹也是可以的。</strong><br><br>
</p>


[origin reference--overflow](https://stackoverflow.com/questions/3334475/git-how-to-update-checkout-a-single-file-from-remote-origin-master)

    It is possible to do (in the deployed repository)

    git fetch
    git checkout origin/master -- path/to/file

    The fetch will download all the recent changes, but it will not put it in your current checked out code (working area).

    The checkout will update the working tree with the particular file from the downloaded changes (origin/master).

    At least this works for me for those little small typo fixes, where it feels weird to create a branch etc just to change one word in a file.


## pip install 指定源

    可以使用阿里pip源

```shell
$ pip install transitions -i https://mirrors.aliyun.com/pypi/simple
# 生成图片依赖以下包
$ pip install graphviz -i https://mirrors.aliyun.com/pypi/simple
# $ pip install pygraphviz -i https://mirrors.aliyun.com/pypi/simple
$ pip install transitions[diagrams] -i https://mirrors.aliyun.com/pypi/simple
```


## A、B两个分支合并，但是B分支删除了某些commit。导致ApullB产生很多B分支改动冲突。

    829  git branch -D ttt
    830  git checkout -b ttt
    831  git reset b0d94639
    832  git add .
    833  git st
    834  git status
    835  git log
    836  git stash 
    837  git reset --hard e7be76e2
    838  git status
    839  git reset 
    840  git reset 254f2589
    841  git add .
    842  git stash 
    843  git log 
    844  git pull origin 2.0test
    845  git stash list
    846  git stash pop
    847  git stash list
    848  git stash pop stash@\{1\}
    849  git add .
    850  git stash pop stash@\{1\}
    851  git status
    852  gut add .
    853  git status
