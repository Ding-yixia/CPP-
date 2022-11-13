git  reset 的三个参数 

--soft 版本号 -- hard 版本号  --mixed 版本号

测试之前 ，先创建文件夹  完成初始化操作 

```
mkdir t1 
git init 
```

先创建 五个文件

```
touch {1..5}.c
```

一次性放到暂存区，然后 分别提交（五次），产生五次日志记录

```
git add . 
```

```shell
git commit -m "1" 1.c
git commit -m "2" 2.c
git commit -m "3" 3.c
git commit -m "4" 4.c
git commit -m "5" 5.c
```

git status		查看暂存区状态

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

修改 1.c  2.c

```
vim 1.c   vim 2.c
```

并创建 6.c 7.c

```
touch {6..7}.c
```

git status 			(6.c 7.c 未被提交，所以未被追踪)  （ 1.c 2.c 被修改 所以在工作区）

```shell
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c
        modified:   2.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c
        7.c

no changes added to commit (use "git add" and/or "git commit -a")
```

为了测试命令，要让 工作区 暂存区 本地库都要有文件

将2.c 和7.c 添加到暂存区   

**最初始状态是 五 个 .c 文件都被提交到本地库** 

##  初始状态   **此时 工作区（1，6.c） 暂存区(2,7.c) 本地库（3，4，5.c） 都有文件**



```shell
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   2.c
        new file:   7.c

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c


```

为了多次利用该测试文件夹的文件   注明： git 的所有信息也会拷贝，提交记录等等也全部一致

```
cp -r t1 t2
cp -r t1 t3
cp -r t1 t4
cp -r t1 t5
```

##   测试 git reset --soft  版本号 ，进入 t 2    

[TOC]

**查看提交日志**

```shell
$ git reflog
b49282b (HEAD -> master) HEAD@{0}: commit: 55555555
5fa1fc5 HEAD@{1}: commit: 4444444
6977b98 HEAD@{2}: commit: 3333333
e98a5e7 HEAD@{3}: commit: 222222222
89870a3 HEAD@{4}: commit (initial): 11111
```

恢复到 5的版本号

git reset --soft  b49282b

查看暂存区    : 结果无变化  原因 ：5.c 文件之后没有任何文件提交到本地库 

```shell
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   2.c
        new file:   7.c

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c

```

恢复到2的版本号    $ git reset --soft e98a5e7

查看暂存区

```shell
$ git reset --soft e98a5e7

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t3 (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   2.c
        new file:   3.c
        new file:   4.c
        new file:   5.c
        new file:   7.c

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c

```

可见 git reset --soft 版本号 执行后 将2.c  之后提交到本地库的 3，4，5 都移动到暂存区了

作用为 ：

回退到指定版本，并将 这个版本提交之后的所有其他版本从本地库移动到暂存区

也就是清空本地库（版本库）撤销  commit 操作

*工作区:修改了已被git跟踪的1.xt,不会重置它的文件内容。6.xt 是新文件，它的状态也不会改变，还是未跟踪状态*
*暂存区:暂存区中的2.bxt修改状态和7.txt的新文件状态保留*
*版本库:因为3.txt、4.txt、 5.txt 是之后当前版本才加入版本库的，git重置commit后自动将它们放到了暂存区*

## 测试 git reset --mixed  版本号 ，进入 t 3

[TOC]

  进入一个新的文件夹

查看 状态 和 日志 

```shell
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   2.c
        new file:   7.c

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c


User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t4 (master)
$ git reflog
b49282b (HEAD -> master) HEAD@{0}: commit: 55555555
5fa1fc5 HEAD@{1}: commit: 4444444
6977b98 HEAD@{2}: commit: 3333333
e98a5e7 HEAD@{3}: commit: 222222222
89870a3 HEAD@{4}: commit (initial): 11111

```

**执行  git reset --soft b49282b      重置到5.c的状态**

查看状态 

```shell
$ git reset --mixed b49282b
Unstaged changes after reset:
M       1.c
M       2.c

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t4 (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c
        modified:   2.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c
        7.c

no changes added to commit (use "git add" and/or "git commit -a")

```

发现  暂存区中的（2，7.c 移动到了工作区 ） ，本地库的文件没有移动

执行$ git reset --mixed e98a5e7  重置到 2.c的状态 

```shell
$ git reset --mixed e98a5e7
Unstaged changes after reset:
M       1.c
M       2.c

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t4 (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c
        modified:   2.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        3.c
        4.c
        5.c
        6.c
        7.c

no changes added to commit (use "git add" and/or "git commit -a")

```

发现 3，4，5.c 文件从本地库移动到了工作区

暂存区中的（2，7.c 移动到了工作区 ）

**执行   $ git reset --mixed 6977b98    重置到 3号状态** 

[TOC]



```shell
$ git reset --mixed 6977b98
Unstaged changes after reset:
M       1.c
M       2.c

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t4 (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c
        modified:   2.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        4.c
        5.c
        6.c
        7.c

no changes added to commit (use "git add" and/or "git commit -a")

```

发现只有4，5从本地库  移动到了 工作区 

暂存区中的（2，7.c 移动到了工作区 ）

总结：和git reset --soft一样 **撤销的只是指定版本号之后的所有提交**

但不仅会撤销版本区，还会撤销暂存区。

##  **进入新文件   执行 git reset --hard版本号**



查看状态，和日志

```shell
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   2.c
        new file:   7.c

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   1.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c


User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t5 (master)
$ git reflog
b49282b (HEAD -> master) HEAD@{0}: commit: 55555555
5fa1fc5 HEAD@{1}: commit: 4444444
6977b98 HEAD@{2}: commit: 3333333
e98a5e7 HEAD@{3}: commit: 222222222
89870a3 HEAD@{4}: commit (initial): 11111

```

执行  $ git reset --hard b49282b  重置 5.c状态

```shell
$ git reset --hard b49282b
HEAD is now at b49282b 55555555

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t5 (master)
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c

nothing added to commit but untracked files present (use "git add" to track)

```

发现工作区中只有一个6.c文件（未被追踪）

继续执行

```
ll -a 
```

 

```
$ ll
total 0
-rw-r--r-- 1 User 197121 0 Jun 13 21:22 1.c
-rw-r--r-- 1 User 197121 0 Jun 13 21:22 2.c
-rw-r--r-- 1 User 197121 0 Jun 13 18:43 3.c
-rw-r--r-- 1 User 197121 0 Jun 13 18:43 4.c
-rw-r--r-- 1 User 197121 0 Jun 13 18:43 5.c
-rw-r--r-- 1 User 197121 0 Jun 13 18:43 6.c

```

发现 暂存区中7.c被删除了

（因为 7.c 最开始就没有被提交到本地库， 同时因为 使用 --hard reset 后 ，会放弃在暂存区和工作区的所有修改。所以7.c 就恢复到没有创建的状态。

相比 同在 暂存区的 7.c，2.c 因为之前有过提交的记录，所以放弃暂存区和工作区的所有修改之后（清空暂存区和工作区） ，2.c 被恢复到最开始的了本地库。

）

再进入一个新文件，这次将 版本reset到 2.c

```shell
$ git reset --hard e98a5e7
HEAD is now at e98a5e7 222222222

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t6 (master)
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        6.c

nothing added to commit but untracked files present (use "git add" to track)

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/t6 (master)
$ ll
total 0
-rw-r--r-- 1 User 197121 0 Jun 13 21:42 1.c
-rw-r--r-- 1 User 197121 0 Jun 13 21:42 2.c
-rw-r--r-- 1 User 197121 0 Jun 13 21:41 6.c

```

发现 暂存区中依旧只有未被追踪的 6.c （这里的 1.c 2.c 重置修改，放在本地库中）

而再执行 ll 查看文件

发现文件 3，4，5，7.c均被删除了。

**总结 ： 使用 git reset --hard 版本号 会将后续版本号跟踪的文件删除（例如3，4，5，7.c） . 同时会再 --mixed 的基础上 ，又重置 工作区文件副本的修改（也就是 1.c  2.c ）**

 



## 三种命令的使用场景

**--hard：(1) 要放弃目前本地的所有改变時，即去掉所有add到暂存区的文件和工作区的文件，可以执行 git reset -hard HEAD 来强制恢复git管理的文件夹的內容及状态；(2) 真的想抛弃目标节点后的所有commit（可能觉得目标节点到原节点之间的commit提交都是错了，之前所有的commit有问题）**



**--soft：原节点和reset节点之间的【差异变更集】会放入index暂存区中(Staged files)，所以假如我们之前工作目录没有改过任何文件，也没add到暂存区，那么使用reset --soft后，我们可以直接执行 git commit 將 index暂存区中的內容提交至 repository 中。为什么要这样呢？**

**一 合并多次commit** 

**这样做的使用场景是：假如我们想合并「当前节点」与「reset目标节点」之间不具太大意义的 commit 记录(可能是阶段性地频繁提交,就是开发一个功能的时候，改或者增加一个文件的时候就commit，这样做导致一个完整的功能可能会好多个commit点，这时假如你需要把这些commit整合成一个commit的时候)時，可以考虑使用reset --soft来让 commit 演进线图较为清晰。总而言之，可以使用--soft合并commit节点。**

**二 修改某次提交出现的错误代码**



### 演示 修复某次提交 的错误   并合并多次 commit

**使用 git reset --soft [版本号]**   

步骤 1  git reset --soft  [版本号]    目的 是将本地库的提交 放到暂存区中 

步骤 2  git commit --amend  -m "commit informatio"  合并多次提交

步骤 3 （可选） 推送到某个分支 （如果有必要的话）



{

如果修改了代码，请使用 $ git commit -a --amend，使用-a来add修改的代码。
若没有修改代码，请使用 $ git commit --amend

}



**git reset --soft [版本号]**    使用场景** 

适用场景：
场景1.本地开发代码已提交，提交后发现这次提交的代码有问题，或者漏提交了一些文件，此时，希望达到以下目的：
①修改有问题的代码。
②补足漏提交的文件（一般是新增的文件没有git add .）
③把以上2点相关的代码，和前一次提交的代码合并成1个提交。
④给合并后的这个提交添加新的注释。
解决办法：

--》回退到出现错误提交的版本号

--》修改问题代码
--》git add . （把漏提交 错误交的文件假如暂存区）
--》执行git commit --amend -m "这里填写提交的注释"

场景2.新接到需求，需要基于master分支拉取一个feature分支，且这个feature分支只有你自己使用（这一点极其重要），由于开发周期较长，你不想每一次都产生一个新的commit，而是每一次commit都修改前一次提交，这样做的好处是，等到你的feature分支提测时，就只有1个干净的commit，没有乱七八糟的提交历史，你只要把这1个commit合并到master里就好了 。
解决办法：在feature分支上，
第1次提交代码时，使用git commit -am "第1次提交的注释"
第2次以后提交代码时，使用git commit --amend -m "这里填写提交的注释"
这样，整个分支可以只有1个commit。

测试场景一     （这里不创建新分支，直接在旧的分支上操作）

**初始化状态**

创建文件夹 tt ,git 初始化 ，创建  t1.c  t2.c  t3.c  test0 

test0 作为被漏交的文件，  将 t2 t3 一次性提交  t1 单独提交 。并修改 可能错误的  t2.c      



```
mkdir tt 
git init
vim t{1..3}.c  //创建写入文件内容
vim test0     //创建写入

```

```
git add t{2..3}.c
git commit -m "2/3"

git add .
git commit "1"
```



**场景一具体操作**  （使用 git  reset  --soft  ）

回退到指定版本

```shell
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$ ls
t1.c  t2.c  t3.c  test0

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$ git reflog
3994812 (HEAD -> master) HEAD@{0}: commit: 1     //提交了 t1.c
e380363 HEAD@{1}: commit (initial): 2/3         //提交了 t2 t3.c

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$ git reset --soft e380363

```

查看状态 

```shell
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   t1.c

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        test0

```

修改错误的	t2.c ，将 test 添加到暂存区

```
vim t2.c  
git add .
```

查看状态

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$ git reset --soft e380363

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   t1.c
        modified:   t2.c
        new file:   test0
```



合并所有提交 

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$ git commit -a --amend -m "120"
[master f889833] 120
 Date: Thu Jun 16 09:24:15 2022 +0800
 4 files changed, 6 insertions(+)
 create mode 100644 t1.c
 create mode 100644 t2.c
 create mode 100644 t3.c
 create mode 100644 test0
```

查看提交次数 

```
git log 
```

```
或者  git log --oneline | wc -l
```

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/tt9 (master)
$  git log --oneline | wc -l
1
```

可见合并为一次了

**如果只是关于合并 多次commit 也可以使用**  

```
如果修改了代码，请使用 $ git commit -a --amend，使用-a来add修改的代码。
若没有修改代码，请使用 $ git commit --amend
```

或者是

```
git rebase -i   版本号  
```

可以百度



## 修改某次提交的错误（创建远程分支）

也可以创建新分支 在新分支上操作 ，（推荐）

演示如下 ：

创建一个文件夹 ,初始化 ，创建一个文件，分别提交五次记录

并提交到远程分支

```
touch t1 
git add .
git commit -m "touch file"
mkdir t1

echo 1111111 >> t1
git commit -am "add 1"

echo 22222222222 >>  t1
git commit -am "add 2"
。。。。。。。。。。。。
。。。。。。。。。。。。
echo 4444444 >> t1
git commit -am "add 4"

git push origin master
```

推送到远程分支

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (master)
$ git push origin master
warning: ----------------- SECURITY WARNING ----------------
warning: | TLS certificate verification has been disabled! |
warning: ---------------------------------------------------
warning: HTTPS connections may not be secure. See https://aka.ms/gcmcore-tlsverify for more information
warning: ----------------- SECURITY WARNING ----------------
warning: | TLS certificate verification has been disabled! |
warning: ---------------------------------------------------
warning: HTTPS connections may not be secure. See https://aka.ms/gcmcore-tlsverify for more information
Enumerating objects: 15, done.
Counting objects: 100% (15/15), done.
Delta compression using up to 12 threads
Compressing objects: 100% (5/5), done.
Writing objects: 100% (15/15), 954 bytes | 318.00 KiB/s, done.
Total 15 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/DJX-TSL-ALS/Test.git
 * [new branch]      master -> master

```

现在如果发现第 add 2 的提交出现了问题 （出现了bug）。要回到 add 2的那次提交修补

**创建新分支** 

```
git checkout -b bug_fix
```



查看新分支状态 ，可以去github 上查看

```shell
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ git reflog
76bb65f (HEAD -> bt, origin/master, master) HEAD@{0}: checkout: moving from master to bt
76bb65f (HEAD -> bt, origin/master, master) HEAD@{1}: commit: add 4
517f797 HEAD@{2}: commit: add 3
6ee825b HEAD@{3}: commit: add 2
aeb2c57 HEAD@{4}: commit: add 1
738f377 HEAD@{5}: commit (initial): touch file

```



回到 状态 add 2 ,并向远程分支 推送修改

{

注意 使用的是 git --hard 

}

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ git reset --hard  6ee825b
HEAD is now at 6ee825b add 2

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ git push origin  HEAD --force
Total 0 (delta 0), reused 0 (delta 0), pack-reused 0
remote:
remote: Create a pull request for 'bt' on GitHub by visiting:
remote:      https://github.com/DJX-TSL-ALS/Test/pull/new/bt
remote:
To https://github.com/DJX-TSL-ALS/Test.git
 * [new branch]      HEAD -> bt

```

git log 查看是否 是在 add 2

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ git log
commit 6ee825bef9b1c717cbf822164b2c052db702f17f (HEAD -> bt, origin/bt)
Author: xiao <xiao@>
Date:   Thu Jun 16 17:22:54 2022 +0800

    add 2

commit aeb2c57728d4988334e3cf5803c481945e6c88a5
Author: xiao <xiao@>
Date:   Thu Jun 16 17:21:49 2022 +0800

    add 1

commit 738f37795682c74f56f5f4d8389ea44e75ee687f
Author: xiao <xiao@>
Date:   Thu Jun 16 17:20:51 2022 +0800

    touch file

```

修改第二次提交内容（比如 我追加一部分内容）

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ echo 2_2_2_2 >> t1
```

提交到本地库 ，提交到远程库

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ git add .
warning: LF will be replaced by CRLF in t1.
The file will have its original line endings in your working directory

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ git commit

[bt 4fca8cf] hot_fix
 1 file changed, 1 insertion(+)

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ cat t1

11111111111
2222222
2_2_2_2

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
$ git push origin bt  

Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Writing objects: 100% (3/3), 229 bytes | 229.00 KiB/s, done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/DJX-TSL-ALS/Test.git
   6ee825b..4fca8cf  bt -> bt

```



**将旧的分支上的内容 添加到新分支 后面**



cherry-pick 批量commit 相关命令：
Git从1.7.2版本开始支持批量cherry-pick，就是一次可以cherry-pick一个区间的commit。

git cherry-pick <start-commit-id>..<end-commit-id>
或
git cherry-pick <start-commit-id>^..<end-commit-id>

    User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt)
    $ git cherry-pick 517f797^..76bb65f   ///////////////////////////////////////////////////


​    
    Auto-merging t1
    CONFLICT (content): Merge conflict in t1
    error: could not apply 517f797... add  3
    hint: After resolving the conflicts, mark them with
    hint: "git add/rm <pathspec>", then run
    hint: "git cherry-pick --continue".
    hint: You can instead skip this commit with "git cherry-pick --skip".
    hint: To abort and get back to the state before "git cherry-pick",
    hint: run "git cherry-pick --abort".


前者表示把到之间(左开右闭，不包含start-commit-id)的提交cherry-pick到当前分支；
后者表示把到之间(闭区间，包含start-commit-id)的提交cherry-pick到当前分支。
其中，到只需要commit-id的前6位即可，并且在时间上必须早于



注意 上面的情况是出现了 代码冲突

```
git status
```

```
$ git status
On branch bt
Cherry-pick currently in progress.
  (fix conflicts and run "git cherry-pick --continue")
  (use "git cherry-pick --skip" to skip this patch)
  (use "git cherry-pick --abort" to cancel the cherry-pick operation)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   t1

no changes added to commit (use "git add" and/or "git commit -a")

```

发现 t1 文件出现了问题

```
vim  t1 
```

按普通的消除代码 冲突解决

然后再`git cherry-pick` 剩余的commit

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt|CHERRY-PICKING)
$ git cherry-pick 76bb65f
error: Cherry-picking is not possible because you have unmerged files.
hint: Fix them up in the work tree, and then use 'git add/rm <file>'
hint: as appropriate to mark resolution and make a commit.
fatal: cherry-pick failed
```

提示我们 提交 

```
git add .
git commit 
```

Try  again 

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt|CHERRY-PICKING)
$ git cherry-pick 76bb65f
Auto-merging t1
[bt 4925891] add 4
 Date: Thu Jun 16 17:46:32 2022 +0800
 1 file changed, 1 insertion(+)


```

查看状态 

```
User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt|CHERRY-PICKING)
$ git log
commit 492589101e59bbc47458b019fb0a58a661dc76d7 (HEAD -> bt)
Author: xiao <xiao@>
Date:   Thu Jun 16 17:46:32 2022 +0800

    add 4

commit 0ec5a25508e303ab6daca70d784a42864dd0573c
Author: xiao <xiao@>
Date:   Thu Jun 16 17:45:39 2022 +0800

    add  3

commit 4fca8cfb35aaac9e6e4be92cdaf0e6d09da37e2e (origin/bt)
Author: xiao <xiao@>
Date:   Thu Jun 16 18:29:17 2022 +0800

    hot_fix

commit 6ee825bef9b1c717cbf822164b2c052db702f17f
Author: xiao <xiao@>
Date:   Thu Jun 16 17:22:54 2022 +0800

    add 2

commit aeb2c57728d4988334e3cf5803c481945e6c88a5
Author: xiao <xiao@>
Date:   Thu Jun 16 17:21:49 2022 +0800

    add 1

commit 738f37795682c74f56f5f4d8389ea44e75ee687f
Author: xiao <xiao@>
Date:   Thu Jun 16 17:20:51 2022 +0800

    touch file

```

**合并旧的后续提交 完成**     （将旧的分支上的内容 添加到新分支 后面**）



之后再 把修改提交到 远程仓库即可

```

User@DESKTOP-DGQLAAQ MINGW64 /d/ws/test (bt|CHERRY-PICKING)
$ git push origin bt --force
Enumerating objects: 8, done.
Counting objects: 100% (8/8), done.
Delta compression using up to 12 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (6/6), 440 bytes | 440.00 KiB/s, done.
Total 6 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/DJX-TSL-ALS/Test.git
   4fca8cf..4925891  bt -> bt

```



## git reset --mixed 的使用场景

**-mixed（默认）**：(1)使用完**reset --mixed**后，我們可以直接执行 **git add** 将這些改变果的文件內容加入 **index 暂存区**中，再执行 **git commit** 将 **Index暂存区** 中的內容提交至**Repository**中，这样一样可以达到合并**commit**节点的效果（与上面--soft合并commit节点差不多，只是多了git add添加到暂存区的操作）；(2)移除所有Index暂存区中准备要提交的文件(Staged files)，我们可以执行 **git reset HEAD** 来 **Unstage** 所有已列入 **Index暂存区** 的待提交的文件。(有时候发现add错文件到暂存区，就可以使用命令)。(3)**commit**提交某些错误代码，或者没有必要的文件也被**commit**上去，不想再修改错误再**commit**（因为会留下一个错误**commit**点），可以回退到正确的**commit**点上，然后所有原节点和**reset**节点之间差异会返回工作目录，假如有个没必要的文件的话就可以直接删除了，再**commit**上去就OK了。



































































































