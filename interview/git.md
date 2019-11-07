## git

### gitflow 有哪些分支

```
Git Flow常用的分支
Production 分支
也就是我们经常使用的Master分支，这个分支最近发布到生产环境的代码，最近发布的Release， 这个分支只能从其他分支合并，不能在这个分支直接修改

Develop 分支
这个分支是我们是我们的主开发分支，包含所有要发布到下一个Release的代码，这个主要合并与其他分支，比如Feature分支

Feature 分支
这个分支主要是用来开发一个新的功能，一旦开发完成，我们合并回Develop分支进入下一个Release

Release分支
当你需要一个发布一个新Release的时候，我们基于Develop分支创建一个Release分支，完成Release后，我们合并到Master和Develop分支

Hotfix分支
当我们在Production发现新的Bug时候，我们需要创建一个Hotfix, 完成Hotfix后，我们合并回Master和Develop分支，所以Hotfix的改动会进入下一个Release

https://www.cnblogs.com/wish123/p/9785101.html

```

### git回滚

1.按次数回滚，回滚到前100次

    git reset --hard HEAD^  表示往前回滚一次

    git reset --hard HEAD~100  回滚一百次

2.使用特定版本号回滚

    git log -3
    git reset --hard 33075bc2da10cd

### 撤销某个文件的修改

    git revert fileA
    只会撤销文件A的修改 其他的commit不受影响

### 删除本地分支和远程分支

删除远程分支

    git push origin --delete dev

删除本地分支
    
    git branch -d dev

### git pull 拉错远程分支

    1. 使用 git reflog 查看历史操作
    2. git reset --hard 40a9a45  回退

### .gitignore不起作用

    git rm -r --cached .
    git add .
    git commit -m 'reset gitignore'

### 合并某个提交commit到指定的分支上

    git log
    git checkout dev
    git cherry-pick 某个commit id 

    合并多个连续分支
    git checkout -b newbranch 62e45cb3  
    git rebase --onto master 76ca45da^ 
    从76ca45da到62e45cb3的提交都被合并到master