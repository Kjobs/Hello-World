Git常用命令
=============

* 选择一个文件夹创建工作目录  
`$ mkdir Testgit`  
`$ cd Testgit`  

* 初始化仓库，新建版本库  
`$ git init`  

* 添加文件到暂存区  
`$ git add file`  

* 添加commit完成添加文件到仓库  
`$ git commit -m "commit"`  
 参数说明:-m 可以直接在命令中输入注释，若没有参数则会打开编辑器进行注释<br/>

* 查看仓库工作状态信息  
`$ git status`  

* 若git status显示有修改，查看仓库修改信息，即查看difference  
`$ git diff file`  

* 查看历史记录，最近3次提交修改信息和commit的ID  
`$ git log`  
`$ git log --pretty=oneline`  
`$ git log --graph` (查看分支图)

* 查看命令历史  
`$ git reflog`  

* 回退版本  
`$ git reset --hard HEAD^`  
`$ git reset --hard HEAD~100`  

* 查看工作区和版本库里面最新版本的区别  
`$ git diff HEAD -- file`  

* 文件在工作区的修改全部撤销，回到最近commit或add的状态。无论是修改后还没有放到暂存区还是添加到了暂存区又作了修改  
`$ git checkout -- file`  

* 撤销暂存区的修改  
`git reset HEAD file`  

* 如果工作区文件被删除，可以选择  
`$ git rm file`从版本库（暂存区）中删除  
`$ git checkout -- file`用版本库（暂存区）中的文件替换工作区的文件，相当于“还原”  

* 建立远程连接，添加到远程仓库以及添加SSH Key后  
`$ git remote add origin git@github.com:GitHub ID/repo.git`  
`$ git push -u origin master`将本地仓库（首次）推送到github    
`$ git push origin master`通过此命令推送最新修改至github    
参数说明：若当前分支与多个主机存在追踪关系，则可以通过-u指定默认主机，之后可以不加任何参数使用git push<br/>
说明：git push若无本地分支名(master)则会删除远程分支（推送空的分支到origin）

* 从远程库克隆到本地    
`$ git clone git@github.com:**(github ID)/Hello-World.git`  

* 抓取远程分支中本地分支没有的内容(不合并本地分支)  
`$ git fetch origin master`  
若要合并:`$ git merge origin/master`

* 抓取远程分支（要合并本地分支）  
`$ git pull origin master`

* 创建分支  
`$ git checkout -b dev`创建并切换到分支，相当于以下两条命令  
`$ git branch dev`  
`$ git checkout dev`  

* 合并分支（master合并dev）  
`$ git commit -m "commit"`  
`$ git checkout master`
`$ git merge dev`  

* 查看当前分支  
`$ git branch`  

* 删除dev分支  
`git branch -d dev`  
