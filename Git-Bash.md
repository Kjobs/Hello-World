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

* 查看历史记录，先死修改信息和commit的ID以供往后需要  
`$ git log`  
`$ git log --pretty=oneline`
