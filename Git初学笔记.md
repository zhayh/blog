#Git学习笔记
参考：[廖雪峰的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

##安装Git  
  1. 在Linux上安装  
      Ubuntu：sudo apt-get install git  
  2. 在Windows上安装  
  
    > 安装[msysgit](http://msysgit.github.io)，按照默认选项安装即可  
    > 安装完成之后运行Git Bash，打开命令行窗口  

##基本概念
  1. 工作区：电脑中的目录  
  2. 版本库：  
  
      > 工作区的隐藏目录.git  
      > 其中最重要的是暂存区stage，第一个分支master和指向master的指针HEAD  
      > 不在暂存区中的文档是不能被commit到分支的  

##配置Git
  1. git config --global color.ui true  
  2. 配置别名： 
  
        git config --global alias.unstage 'reset HEAD'  
        git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"  

##创建版本库
  1. 初始化Git仓库：
  
        mkdir LearnGit  
        cd /F/LearnGit  
        git init  

  2. 添加文件  

        git add readme.txt  
        git commit -m "wrote a readme file"  

    说明：

      > 创建的文件一定放在Git仓库的目录或子目录下  
      > add将文件添加到暂存区  
      > commit将暂存区的所有内容提交到当前分支  
      > -m后面输入本次提交的说明  

##查看、修改和提交 
  1. 查看Git工作区的状态：git status  
  2. 查看文件的修改内容：git diff readme.txt  
  3. 提交修改和提交文件的步骤一样，分两步：  
      
        git add readme.txt  
        git commit -m "add git command"  

##版本回退
  1. 查看提交历史：git log / git log --pretty=oneline
  2. 回退上一个版本：git reset --hard HEAD^ 
  3. 查看历史命令：git reflog
  4. 说明：HEAD指向当前版本

##撤销修改
  1. git checkout -- readme.txt，撤销该文件在工作区的修改，说明：
      * 文件没放到暂存区，撤销修改就回到版本库的状态
      * 文件已添加到暂存区后又作了修改，撤销修改就回到添加到暂存区后的状态
      * 也就是回到最近一次git commithuo git add状态
  2. git reset HEAD readme.txt，撤销暂存区的修改
  3. 举例说明：
      * 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改，用git checkout -- file。
      * 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用git reset HEAD file，就回到了场景1，第二步按场景1操作。
      * 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，需进行版本回退操作，前提是没有推送到远程库。

##删除文件
  1. 从版本库删除
  
        rm readme.txt
        git rm readme.txt
        git commit -m "remove readme.txt"

  2. 工作区删除后从版本库恢复，会丢失最近一次提交后修改的内容
  
        git checkout -- readme.txt

##远程仓库
  1. 创建SSH Key：ssh-keygen -t rsa -C "GitHub邮箱地址"
  2. 登陆GitHub，打开Account Settings -> SSH Keys页面，点击Add SSH Key，将生成的id_rsa.pub的内容拷贝到key内容框
  3. 需要在每台推送到GitHub的电脑上生成key，并添加到GitHub

##添加远程库
  1. 登陆GitHub，点击Create a new repo，创建一个新的仓库
  2. Repository name填入仓库名称，其他保持默认设置，创建Git仓库
  3. 将本地仓库推送到GitHub仓库：
      - git remote add origin git@github.com:zhayh/LearnGit.git
      - git push -u origin master,-u参数：将本地master分支内容推送到远程新的master分支，并关联本地的master分支和远程的master分支，在以后的推送或拉取时就可以简化命令
      - 本地提交后的推送命令：git push origin master

##从远程库克隆
  1. git clone `git@github.com:zhayh/python.git`  
  2. 也可以用 `  https://github.com/zhayh/python.git  ` 克隆  
  3. Git支持多种协议，默认的git：//使用ssh，速度最快  

##创建与合并分支
  1. 创建并切换分支：git checkout -b dev 
  2. 查看分支：git branch dev, 当前分支用*标识
  3. 创建分支：git branch name
  4. 切换分支：git checkout master
  5. 合并分支到当前分支：git merge dev
  6. 删除分支：git branch -d name

##解决冲突
  1. 执行git merge dev时提示内容冲突，手动修改冲突的内容，然后执行git add readme.txt和git commit -m "message"，master和dev就合并了
  2. 查看分支的合并情况：git log --graph --pretty=oneline --abbrev-commit

##分支管理策略
  1. 合并分支时，一般Git会用"Fast forward"模式，删除分支后，会丢失分支信息  
  2. 使用"no-ff"方式merge可以强制禁用"Fast forward"模式   
  
        git merge --no-ff -m "merge with no-ff" dev

  3. 分支策略  
      master分支应非常稳定，仅用于发布新版本，工作都在dev分支上进行  
      每个成员都有自己的分支，经常往dev合并即可  
  4. Bug分支  
      * 隐藏工作现场：git stash
      * 创建一个临时分支修复Bug，修复完成后切换到master分支，完成合并，最后删除临时分支
      * 查看工作现场：git stash list
      * 恢复现场同时删除stash内容：git stash pop
      * 恢复现场：git stash apply
      * 删除stash内容：git stash drop
      * 恢复制定的stash：git stash apply stash@{0}
  5. feature分支
      1. 添加一个新功能，可以新建一个feature分支进行，完成后合并，最后删除feature分支
      2. 强行删除feature分支开发的新功能：git branch -D feature
  6. 多人协作  
      查看远程库：git remote / git remote -v
  7. 推送分支  
      * 推送到主分支：git push origin master  
      * 推送到其他分支：git push origin dev  
      * 推送的选择：  
        * master分支是主分支，要时刻与远程同步  
        * dev分支是开发分支，也需要与远程同步  
        * bug分支只用于在本地修复bug，就没必要推送到远程  
        * feature分支是否推到远程，取决于是否合作在上面开发  
        
  8. 抓取分支
      1. 从远程库clone，一般只能看到本地的master分支
      2. 创建远程origin的dev分支到本地：git checkout -b dev origin/dev
      3. 推送冲突的解决：
      
          * 使用git pull将最新的提交从origin/dev抓取下来，在本地合并，解决冲突，再推送
          * git pull失败的原因：需指定本地dev分支与远程origin/dev分支的链接
          * git branch --set-upstream dev origin/dev

##多人协作的工作模式
  1. 试图用git push origin branch-name推送自己的修改
  2. 如果推送失败，则因为远程分支比本地新，需要先用git pull试图合并
  3. 如果合并冲突，则解决冲突，并在本地提交
  4. 没有冲突或解决了冲突后，再用git push origin branch-name推送就能成功
  5. 注意：git pull提示"no tracking information"，则说明本地和远程分支的链接关系没有创建，用git branch --set-upstream branch-name origin/branch-name

##创建标签
  1. 切换到需要打标签的分支：git checkout master
  2. 创建新标签：git tag v1.0，只存储在本地
  3. 在特定commit id上创建标签：git tag v0.9 commit id
  4. 创建带有说明的标签：git tag -a v0.1 -m "message" commit id （-a指定标签名，-m指定说明文字）
  5. 用-s指定私钥签名一个标签：git tag -s v0.2 -m "message" commit id，签名采用PGP签名，必须首先安装gpg(GnuPG)
  6. 查看所有标签：git tag
  7. 查看标签信息：git show v0.9
  8. 说明：  

      > 默认标签是打在最新提交的commit上，标签按字母排序  
      > 查看commit id：git log --pretty=oneline --abbrev-commit  

##操作标签
  1. 删除标签：git tag -d v1.0  
  2. 推送标签到远程：git push origin v1.0  
  3. 一次性推送所有的标签：git push origin --tags  
  4. 删除远程标签  
      1）先从本地删除：git tag -d v0.9  
      2）再从远程删除：git push origin :refs/tags/v0.9  

##GitHub
  1. 在GitHub上，可以任意Fork开源仓库；  
  2. 自己拥有Fork后的仓库的读写权限；  
  3. 可以推送pull request给官方仓库贡献代码。  

##忽略特殊文件
  1. 在Git工作去的根目录创建.gitignore文件，添加要忽略的文件名  
  2. 参考：https://github.com/github/gitignore  
  3. .gitignore文件本身要放到版本库中，并对其进行版本管理  
  3. 忽略文件的原则：  
      1）忽略操作系统自动生成的文件，如缩略图等  
      2）忽略编译生成的中间文件、可执行文件等  
      3）忽略带有敏感信息的配置文件，如：存放口令的配置文件  

##参考资料
  * Git官方网站：http://git-scm.com  
  * Git在线练习：http://try.github.io/levels/1/challenges/1  
  * Git Cheat Sheet：http://www.git-tower.com/blog/assets/2013-05-22-git-cheat-sheet/cheat-sheet-large01.png
