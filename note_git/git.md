# GIT：

## Git是什么：

+ 最先进的分布式版本控制系统

+ 集中式版本控制系统：版本库是集中存放在中央服务器的，而干活的时候，用的都是自己的电脑，所以要先从中央服务器取得最新的版本，然后开始干活，干完活了，再把自己的活推送给中央服务器。
+ 分布式版本控制系统根本没有“中央服务器”，每个人的电脑上都是一个完整的版本库，这样，你工作的时候，就不需要联网了，因为版本库就在你自己的电脑上。如果你和你的同事都在各自电脑上更改了相同的文件，只需要把各自的修改推送给对方，就可以互相看到对方的修改（分布式版本控制系统通常也有一台充当“中央服务器”的电脑，但仅作为方便“交换”，没有它一样能使用分布式，只不过交换不方便）

## 版本库：

### 什么是版本库？

+ 版本库又名仓库，英文名**repository**，你可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪，以便任何时刻都可以追踪历史，或者在将来某个时刻可以“还原”

### 创建版本库：

1. `cd 路径`进入到一个目录内，路径中最好不要含有中文
2. `git init`把这个目录变成Git可以管理的仓库
3. 当前目录下多了一个`.git`的目录，这个目录是Git来跟踪管理版本库的

### 添加文件到Git仓库：

1. 使用命令`git add <file>`，告诉Git，把文件添加到仓库，可以反复多次使用以添加多个文件

2. 使用命令`git commit -m <message>`，告诉Git，把文件提交到仓库，message是为本此提交的说明，这样你就能从历史记录里方便的找到改动记录

   #### 注意：

   + Git命令必须在Git仓库目录内执行，在仓库目录外执行没有意义
   + 添加的文件必须在当前目录下存在，可用`ls`来查看文件，当然可以是直接目录也可以是子目录，因为这是一个仓

### 查看工作区状态：

+ `git status`命令查看状态：
  + `changes not staged for commit`：意思是文件已经修改过，但还没有准备提交
  + `changes to be committed`：意思是将要被提交的修改包括哪些请问您文件
  + `nothing to commit`：意思是当前没有需要提交的修改
+ 如果`git status`告诉你有文件被修改过，可以用`git diff`来查看修改的内容

### 版本更替：

+ HEAD指向的是当前版本
+ `git log`命令显示从近到远的提交日志，里面可以看到每个版本的版本号，加上`--pretty=oneline`参数可以使显示更加简单
+ `git reset --hard HEAD^`命令回到HEAD上一个版本，^的个数为回到上几个版本数，如果会到的版本数过多，比如要回到上100个版本，这是可以写成`HEAD~100`
+ 如果回到以前的版本后再想回过来，可以通过`git reset --hard <版本号>`来回倒版本号对应的版本
+ 命令`git reflog`可以看到Git记录下来的你的每一次命令

### 工作区和暂存区

+ 工作区就是你电脑的目录
+ 但.git文件不是工作区，而是版本库，里面包括暂存区，指针HEAD和分支
+ `git add`命令就是讲工作区的修改提交到暂存区
+ `git commit`命令是将暂存区修改提交到分支
+ `git diff`比较的是工作区和版本库的区别ls

### 管理更改：

+ GIt跟踪的是文件的修改，而不是文件本身，如果某次更改文件后不用`git add`放到暂存区，那么本次修改就不会到`commit`中

### 撤销修改：

+ 当你在工作区修改了文件但是还没有add时，可以通过命令`git restore -- <file>`（或者`git checkout -- <file>`）撤销工作区的修改，这个命令可能不同的系统不一样，可以通过git status便可以看到取消工作区修改的命令提示

  使用这个命令后，会回到上一次添加到暂存区后的状态

+ 当你修改了文件并且已经添加到暂存区后，可以通过`git restore --staged <file>`（或者`git reset HEAD <file>`）来撤销暂存区修改到工作区，这是如果还想撤销工作区修改，参照上一条

### 删除文件：

+ 如果是已经删除了工作区的文件后想删除版本库的相应文件，使用`git rm <file>`和`git commit`命令提交即可
+ 如果是想恢复工作区文件，可以使用`git check -- <file>`，`git checkout`其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”

## 远程仓库：

### 配置公钥：

+ 通过命令`ssh-keygen -t rsa -C "<email>"`创建一个本机的SSH公钥和密钥，并把公钥配置到github上，以后本机就可以上传代码到gitbub
+ 使用SSH Key的原因是因为GitHub需要识别你推送的东西是你推送的，而不是别人冒充，而Git支持SSH协议，所以GitHub需要知道你的公钥。GitHub允许添加多个key，这样你就可以在多台电脑上传GitHub

### 本地版本库关联远程库与提交：

* 要关联一个远程库，使用命令`git remote add origin git@github.com:git_username/repository_name.git`
* 取消关联远程库`git remote remove origin`

* 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；

* 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改

### 从远程仓库克隆到本地：

+ `git clone git@github:git_username/repository_name.git`
+ github给出的地址不止一个
+ Git不仅支持SSH协议，还支持https等多种协议，只不过SSH协议速度最快

## 分支管理：

### 基本管理命令

+ 每次提交，Git都会把它们串成一条时间线，而这条时间线就是一个分支
+ 在版本库里，有个HEAD指针，此指针指向的是分支，而分支才指向提交
+ `git branch <分支名>`创建分支
+ `git branch -d <分支名>`删除分支（如果要丢弃一个没有被合并过得分支，d要变成D）
+ `git checkout <分支名>`切换分支（也可以通过git checkout -b <分支名>来创建并切换分支）
+ `git branch`查看总分支和当前分支
+ `git merge <分支名>`将当前分支合并到指定分支
+ `git switch -c <分支名>`创建并切换分支
+ `git switch <分支名>`创建分支（switch要比checkout来创建分支更合理）Git

### 冲突解决：

+ 当两个分支指向不同的提交时，此时合并两个分支，便会产生冲突

+ 当Git无法自动合并分支时，就需要解决冲突，即把合并失败的文件手动编辑成我们希望的内容，然后添加、提交
+ 通过`git log --graph --pretty=oneline --abbrey -commit`可以看到分支合并图

### 普通模式合并：

+ 当使用`git merge`时默认Fast forward模式，这种模式下，删除分支后，会丢掉分支信息，使合并后看不出曾经做过分支
+ 使用`git merge --no-ff -m "<备注>" <分支名>` 合并后的历史有分支，能看出来曾经做过合并

### Bug修复：

+ 修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除
+ 如果手头的工作没有完成，可通过命令`git stash`保存一下工作现场，然后去修复bug，修复后可用`git stash pop` 回到工作现场，同时删除stash（`git stash apply`能够恢复且stash内容不删除）
+ `git stash list`查看stash内容
+ `git stash apply stash@｛id｝`来恢复指定的stash
+ 在一个分支上修改的bug想要合并到另一个分支上，如果修改相同的话，可以用`git cherry-pick <commit id>`把提交复制到当前分支

### 多人协作：

+ `git remote -v `查看远程库信息

+ 如果你的同事从远程库克隆后，其实只能看到master分支，如果想要在远程库的其他分支上开发，就要通过命令`git checkout -b dev origin/<分支名>`来创建远程origin的<分支名>分支到本地
+ 通过`git push origin <分支名>`来推送修改提交后的分支
+ 如果推送失败，可能是因为远程的分支已经被其他人修改，需要先用git pull来抓取远程的新提交然后在本地合并（如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to=origin/<branch-name> branch-name>`）
+ 如果合并有冲突，则要解决冲突，在本地提交
+ 提交后再推送

### Rebase变基操作：

+ rebase操作可以把本地未push的分叉提交历史整理成直线；
+ rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比

## 标签：

+ 标签是指向某个commit的指针，但是不能够像分支那样可以移动，作用是方便记录某次提交，commit id太复杂了
+ `git tag <标签名>`添加标签，默认为当前提交
+ `git tag`查看全部标签
+ `git tag <标签名> <commit id>`给指定提交打标签
+ `git tag -a <标签名> -m “标签说明”  <commit id>`给标签加上说明
+ `git show <标签名>`查看标签信息
+ `git push origin <tagname>`可以推送一个本地标签；
+ `git push origin --tags`可以推送全部未推送过的本地标签；
+ `git tag -d <tagname>`可以删除一个本地标签；
+ `git push origin :refs/tags/<tagname>`可以删除一个远程标签

## 牛掰的Github：

- 在GitHub上，可以任意Fork开源仓库；
- 自己拥有Fork后的仓库的读写权限；
- 可以推送pull request给官方仓库来贡献代码

## Git设置：

+ pass

# 常见问题和常用操作汇总：

## 文件上传到github通常的步骤：

+ `git add <filename>`将工作区的修改提交到暂存区
  + `git add .`添加当前目录内所有文件
+ `git commit -m <message>` 将暂存区修改提交到分支
+ ![image-20210812190550185](D:\note_file\note_git\image-20210812190550185.png)
+ `git push origin <分支名>` 将分支推送到远程仓库(github)

## 文件从github上删除通常的步骤：

+ 本地工作区删除：手动删除即可
+ 本地版本库删除（直接删除版本库中的文件，也会删除工作区的文件）：
  + `git rm <filename>`：先删除
  + `git commit -m <message>`：后提交
+ 推送到远程仓库(github)：`git push origin <分支名>`

## 密钥问题：

+ 确保用户主目录下的.ssh目内没有文件
+ `ssh-keygen -t rsa -C "youremail@example.com"`创建SSH Key密钥，之后在用户主目录下会有id_rsa和id_ras.pub两个文件，前者为私钥，后者为公钥
+ 在github上添加公钥内容

## 配置邮箱和姓名：

+ `git config --global user.email "you@exampli.com"`——配置上github上绑定的email
+ `git config --global user.name "Your Name"`——github上的名字

## 关联远程库：

+ `git remote -v`：查看关联状态
+ `git remote rm <远程库名字>` ：删除远程库的关联（不是删除远程库）
+ `git remote add origin git@github.com:git_username/repository_name.git`：关联远程库

## 远程推送失败问题：

+ 可能是已经被别人修改，所以要先`git pull origin master`再进行`git push -u origin master`

