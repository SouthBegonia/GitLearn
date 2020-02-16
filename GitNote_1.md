# git 操作指令

## 信息查看

```
git status		//查看当前暂存区（stage/index）和当前HEAD commit 的区别（注：大部分时候HEAD指向最新分支

git log		    //查看commit历史记录，即便commit_id
git log --graph //查看commit的历史合并线路
git log --oneline --graph //简洁模式查看commit历史记录
git reflog		//查看详细commit历史操作记录（比如版本回退后想要悔棋）

git branch		//查看本地分支
git remote		//查看远程分支

git --version	//查看当前git版本
```
---------------------

## 删除、撤销及回退

### 删除文件:
```
rm README.txt		//删除当前目录下的README.txt文件，不生成commit记录
git rm README.txt	//删除原本存在于暂存区或者版本库中的README.txt文件，并生成commit记录
```

### 撤销修改及回退版本：

1.对未提交到暂存区的、修改了的文件（即 **尚未 git add** 的）进行撤销：

```
git checkout -- README.txt
git checkout -- *.txt
git checkout -- *
```

2.对已经提交暂存区的文件（即 **已经 git add 但尚未 git commit** 的）进行撤销：

```
//文件从暂存区回到工作区，实现撤销git add功能
git reset HEAD README.txt
git reset HEAD *.txt
git reset HEAD
```

3.对 **已经 git commit** 的文件进行撤销，仅能**版本回退**：

```
//回退到上1个版本\上N个版本\某个版本，不保留任何工作区或者暂存区文件（慎用
git reset --hard HEAD^
git reset --hard HEAD~N
git reset --hard  commit_id

//回退到上1个版本\上N个版本\某个版本，且保留当前工作区和暂存区文件（推荐
git reset --soft HEAD^
git reset --soft HEAD~N
git reset --soft commit_id

//补充：
--mixed：不删除工作空间改动代码，撤销commit，并且撤销 git add ，git reset --mixed HEAD^ 和 git reset HEAD^ 等价
--soft：不删除工作空间改动代码，撤销 commit，不撤销 git add
--hard：删除工作空间改动代码，撤销 commit，撤销 git add 
```

---------------------

## 分支管理
我们在使用git指令时会遇到 **master、HEAD、origin、branch** 等词汇，其含义如下，同时推荐佳作：[HEAD、master 与 branch - 小强唐](https://www.jianshu.com/p/4219b6f62ce3)
- `branch`：分支，指向commit的指针，包含从初始commit到它的所有路径
- `master`：在创建本地仓库时，就自动创建的**默认主分支**
- `HEAD`：**指向当前commit的指针**，通常指向最新的commit。除了指向commit还可以指向branch，例如master
- `origin`：远程仓库（github）所提供的**默认远程分支**

### 分支操作：
```
git branch			  //查看分支，当前分支带*号

git branch dev		  //创建分支dev
git checkout dev	  //切换到dev分支（注：工作区和暂存区文件可能在分支切换后被消除
git checkout -b dev   //创建并切换到dev分支

git merge dev		  //将dev分支合并到当前分支
git merge --no-ff -m "merge with no-ff" dev  //将dev分支合并到当前分支，并在log中保留dev分支的记录

git branch -d dev 	  //删除dev分支
```

### 分支冲突：
假如dev分支和master分支都有新的add和commit，合并时则会造成冲突，冲突消息如下图：

解决措施：根据 git state 定位两个 commit 冲突的文件，如本例的dev.txt。后打开该文件可看到 `<<<<<<<，=======，>>>>>>>` 标记出不同分支的内容，修改后再 git add、git commit 即可。当然该过程的3次commit都会被记录


---------------------

## 关联、推送到远程仓库
首选创建并初始化本地仓库，与远程仓库同名，执行 git init 完成初始化，后找到远程仓库Clone ordownload，以SSH或者HTTPS进行克隆：`git remote add origin git@github.com:SouthBegonia/GitLearn.git`

```
git push -u origin master	//首次推送本地master分支到远程仓库、并实现本地远程两master分支关联
git push origin master	    //推送本地master分支到远程仓库
git push origin dev		    //推送本地dev分支到远程仓库
```

# git 常见错误合集

**1. git push 超时**：
- 问题描述：

```
Connection reset by 13.250.177.223 port 22
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

- 问题原因：网络状况不佳
- 解决方法：待网好重新push

**2. git push 时报错**：
- 问题描述：

![](https://img2018.cnblogs.com/blog/1688704/202002/1688704-20200216125935238-995599178.jpg)

- 问题原因：远程仓库已存在README.md或.ignore等文件或是在github网页内对文件进行了修改及其commit，但尚未pull到本地就执行push，进而造成冲突
- 解决方法：先`git pull --rebase origin master` 把远程仓库有的文件pull到本地，再执行`git push -u origin master`

**3. github网页图片加载慢**:
- 问题描述：github个人网页无法显示头像、仓库内README中也无法显示图片
- 解决方法：修改host中的ip，请移步： [GitHub页面加载缓慢以及图片加载失败的解决方案](https://blog.csdn.net/qq_42780289/article/details/102518929)



# github 检索项目方法
在github中检索优质开源项目是大家家常便饭的事，但单凭官网推送、名字搜索所检索到的项目寥寥无几或是不符合我们的需要，而github的又缺少可视化的高级检索栏，因此我们迫切需要准确的检索方法： **在检索栏中键入各类指令**，例如项目名带有某词汇，或是限制项目star数、最新push日期等。更全面的指令参考官方：[Searching for repositories - GitHub Help](https://help.github.com/en/github/searching-for-information-on-github/searching-for-repositories)

```
in:name Unity		 //搜索项目名带有Unity一词的项目
in:readme Unity	     //搜索README ...
in:description 爬虫  //搜索描述 ...

language:C#		     //限制项目语言为C#
istars:>100      	 //限制star数大于100 
forks:>10			 //限制fork数 ...
pushed:>2019-9-03    //最近更新日期 ...

Awesome Unity		 //热门Unity项目

//示例：
in:name RPG in:description Unity istars:>50 language:C#
```

# 参考
- [Git教程 - 廖雪峰的官方网站](https://www.liaoxuefeng.com/wiki/896043488029600)
- [git Documentation](https://www.git-scm.com/docs)
- [Git基本知识与常用指令 - 马非白即黑](https://www.cnblogs.com/gavincoder/p/9073368.html)
- [git rm命令的使用 - Samven_7](https://blog.csdn.net/qq_42780289/article/details/98353792)
- [Git 丢弃本地修改 - Rethink](https://www.jianshu.com/p/565306500575)
- [HEAD、master 与 branch - 小强唐](https://www.jianshu.com/p/4219b6f62ce3)
- [Searching for repositories - GitHub Help](https://help.github.com/en/github/searching-for-information-on-github/searching-for-repositories)