Git常用命令
=============

### 基础命令

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
 
 ---

### 仓库管理

* 查看仓库工作状态信息  
`$ git status`  

* 若git status显示有修改，查看仓库修改信息，即查看difference  
`$ git diff file`   

* 撤销暂存区的修改  
`git reset HEAD file` 

* 查看工作区和版本库里面最新版本的区别  
`$ git diff HEAD -- file`  

* 文件在工作区的修改全部撤销，回到最近commit或add的状态。无论是修改后还没有放到暂存区还是添加到了暂存区又作了修改  
`$ git checkout .`  
`$ git checkout -- file`(单个文件)   

* 如果工作区文件被删除，可以选择  
`$ git rm file`——从版本库（暂存区）中删除  
`$ git checkout -- file`——用版本库（暂存区）中的文件替换工作区的文件，相当于“还原”  

  
---

### 版本管理

* 查看历史记录，最近3次提交修改信息和commit的ID  
`$ git log`  
`$ git log --pretty=oneline`  
`$ git log --graph` (查看分支图)

* 查看历史版本  
`$ git reflog`  

* 回退版本到某个节点  
`$ git reset --hard HEAD`(不保留修改)   
`$ git reset --hard 0c15f14`(回退至查询到的某个版本节点，节点一般只写7位数)  
`$ git reset --hard HEAD~100`  
`$ git reset --soft HASH`(保留修改)   

---

### 标签管理

* 查看标签  
`$ git tag`

* 给指定版本打上标签  
`$ git tag -a <tagname> <version_id> -m "tag description"`

* 推送标签  
`$ git push origin <tagname>`

* 一次性推送全部标签
`$ git push origin --tags`  

* 删除标签(远程仓库中不会移除这个标签)  
`$ git tag -d <tagname>`

* 删除远程仓库的标签  
`$ git push origin :refs/tags/<tagname>`

---

### 远程仓库

* 建立远程连接，添加到远程仓库以及添加SSH Key后  
`$ git remote add origin git@github.com:GitHub ID/repo.git`  

* 新建远程分支
`$ git push origin local_branch:remote_new_branch`  

* 删除远程分支(删除相当于推送空分支到远程分支)  
`$ git push :remote_new_branch`  
或是  
`$ git push origin --delete remote_new_branch`  

* 推送本地分支到远程分支  
`$ git push <远程主机名> <本地分支名>:<远程分支名>`  
`$ git push -u origin master`——将本地仓库（首次）推送到github    
`$ git push origin master`——通过此命令推送最新修改至github    
`$ git push -f origin master`——强制推送更新
> 参数说明：若当前分支与多个主机存在追踪关系，则可以通过-u指定默认主机，之后可以不加任何参数使用git push<br/>
> 说明：git push若无本地分支名(master)则会删除远程分支（推送空的分支到origin）  

* 抓取远程分支中本地分支没有的内容(不合并本地分支)   
`$ git fetch <远程主机名> <远程分支名>`   
`$ git fetch origin dev`  

* 合并到当前分支  
`$ git merge origin/dev`

* 抓取远程分支（要合并本地分支,相当于fetch+merge）  
`$ git pull <远程主机名> <远程分支名>:<本地分支名>`  
`$ git pull origin master` 

* 从远程库克隆到本地    
`$ git clone git@github.com:**(github ID)/Hello-World.git`  

* 删除远程分支文件，保留本地文件  
`$ git rm -r --cached file_path/file_name`(删除后再推送更新)  

---

### 分支管理

* 查看当前分支  
`$ git branch`  
`$ git branch -a`——查看本地和远程所有分支  
`$ git branch -r`——查看远程版本库所有分支  
`$ git branch -vv`——查看本地分支对应的远程分支  

* 创建分支  
`$ git checkout -b dev`创建并切换到分支，相当于以下两条命令  
`$ git branch dev`——创建  
`$ git checkout dev`——切换  

* 合并分支（master合并dev）  
`$ git commit -m "commit"`  
`$ git checkout master`
`$ git merge dev`

* 删除dev分支(若存在未merge的提交，则删除会失败；参数'-D'表示强制删除)  
`git branch -d dev`  

---
