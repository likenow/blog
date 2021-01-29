## Git worktree

### 背景

> 我现在开发的项目，工程比较大，30G+
>
> 能不能在同一个 repo 中同时有两份代码，并且可以保持两份相似但不是完全相同的代码并行开发呢？
>
> 答案是：有。
>
> 同事搞了一个技术分享，就是关于这方面的内容。他使用 `git worktree` 这一 `Git 2.15` 版本引入的新概念，基于`脚本语言`、`ruby`和 `Cocoapods` 做了一个脚手架。



### 遇到的问题

我们目前要维护和开发的 SDK 版本大概在3~5个，我们要对接的宿主App 主要的有3个，每个工程都要 30G+。开发中切换 `branch` 的成本太高，尤其是当代码变动很大的时候，有可能改变了项目结构，甚至可能变更了 `build system`。

如果切换 `branch` 会面临：

- Xcode 需要花费约 30min 的时间来重新索引和设置

如果 `clone` 多个仓库会面临：

- 在不同的仓库中拉取相同的变更很多次，占用很大的存储  ps. 256G 的本，根本玩不转

我要介绍的 `worktree`, 可以避免频繁的切换分支，将老的分支 `checkout` 到单独的文件夹中作为 `worktree`，每一个分支都可以有一个独立的 IDE 工程。



### git worktree 使用

```bash
# 这行命令将在 new-dir 目录中将 some-existing-branch 中的内容 check out 出来
# 就像在该目录中 clone 了一份新代码一样。
git worktree add ../new-dir some-existing-branch
```

**注意不要将目录放到主仓库中。**

在此之后新目录中的内容就可以和主仓库中的内容一样，新建分支，`push` 到远端。

当功能开发完成可以直接删除该目录，然后运行 

```bash
# 清理操作
git worktree prune
```



#### 优点

`git worktree` 非常适合大型项目又需要维护多个分支，想要避免来回切换的情况：

- `git worktree` 可以快速进行并行开发，同一个项目多个分支同时并行演进
- `git worktree` 的提交可以在同一个项目中共享
- `git worktree` 和单独 `clone` 项目相比，节省了硬盘空间，又因为 `git worktree` 使用 `hard link` 实现，要远远快于 `clone`



### 更多

```bash
usage: git worktree add [<options>] <path> [<commit-ish>]
   or: git worktree list [<options>]
   or: git worktree lock [<options>] <path>
   or: git worktree move <worktree> <new-path>
   or: git worktree prune [<options>]
   or: git worktree remove [<options>] <worktree>
   or: git worktree unlock <path>
```

