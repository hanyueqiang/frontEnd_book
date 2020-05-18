## git 使用篇

#### 在 A 分支写代码，B 分支出了 Bug 怎么切换到 B 分支，在不 commit 和注释的情况下 (某站)

git stash

```
  // 在当前dev分支上执行  $ git stash 命令。将当前分支存起来
  $ git stash

  // 再执行git status已经没有要修改的东西了
  $ git status

  // 切换到测试分支改bug
  $ git checkout test

  // 改完后切回dev
  $ git checkout dev

  // 这个时候执行 git status 命令仍旧显示没有东西需要提交
  // 毕竟我们前边已经成功将dev上未提交的改动给“隐藏“了，
  // 用git stash list命令去查看我们“存储”的列表。
  $ git stash list

  // stash apply命令恢复所有stash内容
  // 但是恢复后，stash内容并不删除
  $ git stash apply

  // 删除所有stash内容
  $ git stash drop
```

#### 第一次 commit 第二次 commit 发现实际上只需要压缩合并两次提交的就好了，用哪个命令?(某站)

```
// commitId是你要合并的commit后所形成的一个commitId前一个commitId
// -i 的参数是不需要合并的 commit 的 hash 值
git rebase -i commitId

// vi模式下，在合并到前面改为squash
squash 的意思是这个 commit 会被合并到前一个commit
// 修改commit信息后 wq退出
```
