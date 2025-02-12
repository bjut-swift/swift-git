# swift-git
根据b站up主[「码农高天」](https://space.bilibili.com/245645656)的Git教学视频进行的总结，帮助同学们速成Git基本操作，一切解释权归视频作者[gaogaotiantian](https://github.com/gaogaotiantian)所有，如有侵权请联系我们以删除。
建议观看原视频，本仓库仅用作速查复习。

分为三个部分：
- [【十分钟学会正确的github工作流，和开源作者们使用同一套流程】](https://www.bilibili.com/video/BV19e4y1q7JJ)
- [【十分钟学会常用git撤销操作，全面掌握git的时光机】](https://www.bilibili.com/video/BV1ne4y1S7S9/?share_source=copy_web&vd_source=26a2b981a461580e599a2b8bf7ecd932)
- [【Angular提交信息规范】](https://zjdoc-gitguide.readthedocs.io/zh-cn/latest/message/angular-commit.html)

# Git & GitHub 工作流

## Clone from remote

![Screenshot 2024-02-19 at 20 33 14](https://github.com/bjut-swift/swift-git/assets/101612750/6cd0141c-81cb-4f92-9961-edcb9cdb46b1)

## Create feature branch

硬盘并不在乎你的文件在哪个branch，在你使用`checkout`切换branch后，git会把这个branch上的所有源文件同步给硬盘。

![Screenshot 2024-02-19 at 20 37 00](https://github.com/bjut-swift/swift-git/assets/101612750/d222b4c7-9324-400c-831a-ec87c6f32152)

## Check diff and Add

![Screenshot 2024-02-19 at 20 56 50](https://github.com/bjut-swift/swift-git/assets/101612750/62d03e72-2ef8-4752-ac7a-686b608a0c9b)

`git add <changed_file>`会把修改的文件放到**暂存区**

`git commit`将修改真正的放到git中，

![Screenshot 2024-02-19 at 20 59 36](https://github.com/bjut-swift/swift-git/assets/101612750/4ece5dae-c7fc-4da9-b47b-05c4e0e7072d)

> 这样local git就增加一个commit，要注意Disk中的内容是没有变化的

## Tell the local git changes to GitHub

![Screenshot 2024-02-19 at 21 01 56](https://github.com/bjut-swift/swift-git/assets/101612750/3d8ea8bd-aff4-4672-81f9-69815fe5805a)

## update

经常会遇到提交后发现main branch上有了新的commit，那我们要测试自己的feature在这个update更新之下是否还能正常工作。所以要把main branch的更新同步到my-feature branch中。

- `git checkout main`，此时Disk上的源代码还是init的状态，而不是我们修改后的状态
- `git pull origin main`，同步remote的ma
in到local的main中，这样remote、local和Disk的main就又都一样了。

![Screenshot 2024-02-19 at 21 06 24](https://github.com/bjut-swift/swift-git/assets/101612750/b76e30f4-2ed7-406c-89ae-9ea674890ef4)

- `git checkout my-feature`
![Screenshot 2024-02-20 at 02 47 45](https://github.com/bjut-swift/swift-git/assets/101612750/9e5d107a-cbe9-4b97-9e1d-aa620e4364b1)

- `git rebase main`:

![Screenshot 2024-02-20 at 02 50 09](https://github.com/bjut-swift/swift-git/assets/101612750/23bbabab-3c5e-4476-b562-42b99305c605)


把我的修改先扔到一边，然后把main最新的修改拿过来，然后在这个基础上在尝试把我的commit弄回去。

这个过程中可能会有`rebase conflict`，需要手动选择你到底要哪段代码。

rebase后，我们相当于是在最新的main branch上面做了修改，这也是使用rebase而不是`merge`的好处。

## Push to GitHub

- `git push -f origin my-feature`

![Screenshot 2024-02-20 at 02 51 33](https://github.com/bjut-swift/swift-git/assets/101612750/32776ef6-663d-4f27-9a3a-425ab272f2f2)


把local git里面的branch push到GitHub上，因为我们做了rebase，所以push必须加上`-f`，which stands for `force`，强制push

## Pull request

![Screenshot 2024-02-20 at 02 54 50](https://github.com/bjut-swift/swift-git/assets/101612750/06fcc95f-7a7a-4998-8523-4185e3caad06)


- Squash and merge（仓库管理者操作）：

![Screenshot 2024-02-20 at 02 55 43](https://github.com/bjut-swift/swift-git/assets/101612750/61279d81-0abe-41b3-80fa-9f2397d61978)

> 所有改变变成了update2这个commit

Squash就是把分支上的所有改变合并为一个commit， 保证main branch的commit history尽可能简洁。（改变了commit的结构、数量和名字）

然后可以在GitHub上delete branch，但这次时候local git还有这个branch。

- `git checkout main`

- `git branch -D my-feature`

- `git pull origin main`，把最后的squash & merge后的更新同步到我的local的main branch和硬盘中。这时候remote、local和硬盘就又都一样了。

![Screenshot 2024-02-20 at 03 01 37](https://github.com/bjut-swift/swift-git/assets/101612750/1f2d6535-d546-40c3-97d9-36f09fa86ce6)


# 撤销文件修改

也就是改了但是没`add`的时候。

```zsh
git restore <changed_file>
```

（在较新版本中）

![Screenshot 2024-02-20 at 03 07 05](https://github.com/bjut-swift/swift-git/assets/101612750/4cca550f-125c-4c38-8195-f4ca169ea374)


## 暂存区撤销

这时候`git status`会看到绿色的文件

![Screenshot 2024-02-20 at 03 09 44](https://github.com/bjut-swift/swift-git/assets/101612750/da1721b0-445d-48df-a66b-ad7b2248c93a)


这样的操作比较安全，撤销的是`git add`的操作，不会改变硬盘上的源代码。

如果你是还想把文件的修改也撤销了，那就用

```zsh
git checkout HEAD <changed_file>
```

`HEAD`表示最近的一次commit，这个操作会丢失硬盘上的修改。

## 撤销commit
### reset
```zsh
git reset --soft HEAD~1
```

波浪线后的数字就是撤回到前几个commit的意思。

这个命令是让local git撤回到前一个commit的状态，硬盘和暂存区中不会发生变化，只是local git中的commit被撤销了。

```zsh
git reset HEAD~1
# equivalent to
git reset --mixed HEAD~1
```

这个操作会同时把commit，和add都撤销，只保留硬盘上的修改。

```zsh
git reset --hard HEAD~1
```

那就是完整的恢复初始状态了，硬盘上的修改也恢复，可撤销但麻烦，慎用。

### revert
```zsh
git revert HEAD
```

添加一个和之前commit相反效果的commit


![Screenshot 2024-02-20 at 03 21 26](https://github.com/bjut-swift/swift-git/assets/101612750/72386a9f-0ea2-4e8c-9f17-c5026bd503b6)


相较于reset，revert的好处是可以相当于删除任意一个commit的change：

![Screenshot 2024-02-20 at 03 22 49](https://github.com/bjut-swift/swift-git/assets/101612750/24baec5c-c936-4bd3-af1c-2c0515c81945)


一个更重要的好处是，当你push到github后，这个修改从本地到公共区域了，我们需要判断使用的分支是公有分支还是个人分支（公有分支：只要有不只你一个人用的就是公有，几乎所有main分支都是共有的）。对于公有分支，只能往前走，不能倒退。因为一旦做删除操作，别人的分支就乱套了。所以当我们的修改目标是公有分支的时候，就只能用`git revert`，**从结果上**撤销更改。

如果是个人分支，那就可以使用reset和`-f push`：

![Screenshot 2024-02-20 at 03 27 07](https://github.com/bjut-swift/swift-git/assets/101612750/78716586-7ca6-425e-aa60-9b34056df945)
> 因为正常push，对于远端会察觉你有缺少的commit

对于公有分支，绝对不要使用`-f`，个人分支的话可以让commit更加简洁来使用。

# Angular提交规范

目前最受开发人员肯定的规范是前端框架Angular提出的[Angular提交信息规范](https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#-commit-message-guidelines)

这里只介绍使用频率最高的提交类型的介绍，其它详情请进入官方文档学习。

## 提交类型

提交类型指定为下面其中一个：

`build`：对构建系统或者外部依赖项进行了修改

`ci`：对CI配置文件或脚本进行了修改

`docs`：对文档进行了修改

`feat`：增加新的特征

`fix`：修复bug

`pref`：提高性能的代码更改

`refactor`：既不是修复bug也不是添加特征的代码重构

`style`：不影响代码含义的修改，比如空格、格式化、缺失的分号等

`test`：增加确实的测试或者矫正已存在的测试


