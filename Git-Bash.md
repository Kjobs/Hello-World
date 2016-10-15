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

* 查看仓库工作状态信息  
`$ git status`  

* 若git status显示有修改，查看仓库修改信息，即查看difference  
`$ git diff file`  

* 查看历史记录，最近3次提交修改信息和commit的ID以供需要  
`$ git log`  
`$ git log --pretty=oneline`  

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
1. `$ git rm file`从版本库中删除；  
2. `$ git checkout -- file`用版本库中的文件替换工作区的文件，相当于“还原”
