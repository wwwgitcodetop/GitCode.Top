# Git 分支操作指南与讲解

> 请注意，因为作者的记性不是很好，因此特别写篇教程给自己用来快速查看，至少这篇文章说的是什么作者能明白。
但请注意，这篇文章的目的是作者写给自己的。

Git 是一个版本控制系统，而分支 `branch` 是其中的概念之一。
通过分支，可以实现在同一个仓库中独立地开发不同的功能，就比如本博客的资源域名：

```
file.gitcode.top
file2.gitcode.top
```

如您所见，但其实 `file` 和 `file2` 的文件均在同一个存储库里，只是分支不同和部署的区域不同而已，我们之前还分别存好几个存储库里 233。

## 查询分支
查询当前仓库中所有的分支，可以在终端执行：

```bash
git branch
```

该命令将列出本地存储库的所有分支，并在当前分支前加上星号 (*) 标记。
如果想查看远程分支，可以加上 `-r` 参数，例如：

```bash
git branch -r
```

如果要查看本地和远程所有的分支，可以使用 `-a` 参数：

```bash
git branch -a
```

## 创建分支
在终端使用一下命令创建新的分支 `main2`：

```bash
git branch main2
```

此时，创建了一个名为 `main2` 的分支，可以重复再执行 `git branch` 命令查询本地分支列表，应该能看到新创建的分支。

## 切换分支
接下来如果要切换到刚刚创建分支，可以使用以下命令：

```bash
git checkout main2
```

从 Git 2.23 版本开始，推荐使用 `git switch` 命令来切换分支：

```bash
git switch main2
```

因为 `git checkout` 除了切换分支外，还可以用于其他操作，如查看提交、创建分支、还原文件等。
它的用法更广泛，而 `git switch` 专门用于切换分支。

## 删除分支
如果你后悔创建 `main2` 了或是其他什么原因需要删除一个分支，命令如下：

```bash
git branch -D main2
```

这样就删除了 `main2` 分支

> [!NOTE]
> 需要注意的是，`-D` 参数用于强制删除尚未被合并的分支，如果要删除已经被合并到其他分支的分支，可以使用 `-d` 参数

```bash
git branch -d main2
```

（作者没搞懂这里是什么意思，但还是CV文档过来了）

## 变基
变基 `rebase` 是 Git 中的另一个操作，用于将一个分支的更改应用到另一个分支之上。
与 `merge` 操作不同，`rebase` 会重新应用提交记录，生成更线性的历史记录。

（作者小学水平，这里也没搞懂，不好意思）

例如，有两个分支 `main` 和 `main2`，我们希望将 `main2` 分支上的更改应用到 `main` 分支之上，可以使用以下命令：

```bash
git checkout main2
git rebase main
```

这将会把 `main2` 分支上的提交重新应用到 `main` 分支的最新提交之后。变基的过程可能会遇到冲突，此时需要手动解决冲突并继续变基：

```bash
git add <解决冲突的文件>
git rebase --continue
```

（好像 `git add` 后面加点可以应用到全部？作者没试过，误操作）

变基完成后，`main2` 分支的提交历史会被重新排列到 `main` 分支的后面。

变基会重写提交历史，因此不建议在公开的分支上使用变基操作，以免影响他人协作。
