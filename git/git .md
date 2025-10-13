# Git

## 一、初识

### 1.版本控制

版本 

​	软件版本	

​	文件版本：保存重要历史记录，恢复历史数据

####版本控制软件

1.保存和管理工具 

2.提供客户端工具进行访问

3.提供不同版本文件的比对功能

###2.集中式版本控制

中央服务器（资源库）<-下载--上传->本地

多人协作开发

​	存在文件问题：同一文件，多人同时修改

​		解决：添加文件锁，但是导致效率降低

​			  多人对不同行修改

**隐患：中央服务器故障，离线造成数据丢失**			

###3.分布式版本控制

本地复制库，访问本地库

好处：资源安全、访问更快

###4.多人协作开发

---

##二、客户端

|  操作文件  | 比对文件 |     存储文件     |
| :--------: | :------: | :--------------: |
|  仓库路径  |  <提交>  |       .git       |
| （修改的） |   提交   | （仓库中实际的） |

提交码：40个16进制的数字组成的版本号

无论增删改，提交都产生不同的版本

###分支原理

对库的不同副本进行操作

分支合并->解决一次冲突问题

####实例

​	开发者a	代码1	复制库	代码代码代码

项目经理		--	版本库<		合并		

//分支 main

​	开发者b	代码2	复制库	代码代码代码

###分支操作
<<<<<<<HEAD为当前分支

### 标签

history 中 create tag

###远程仓库

github

gitee

​	在desktop里clone，url使用gitee的地址

​		设置——私人令牌——密码

本地仓库提交上传远程仓库

### README

git仓库的根目录下，描述仓库信息

### ignore

git只比对代码，不比对图片、文档等

.bak副本文件

右键ignore可选其所有副本文件 

绿增黄改红删

@@-1，+1，2@@

---

## 三、集成

IDEA内直接进行提交抓举等操作，vscode

---

## 四、版本号

###文件操作

定位仓库文件2+38

右键命令行git cat-file -p 查看文件

提交信息		文件状态		文件内容

一次提交创建很多版本号

 parent上一次提交的版本号

每次提交生成一个文件状态，文件状态关联历史的文件内容

 ```
[commit]  提交对象
 ├── message: "feat: 添加天气模块"
 ├── author: "Yu Xie"
 ├── parent → (上一次 commit)
 └── tree → [tree 对象]
              ├── src/app.js → blob(文件内容hash)
              ├── index.html → blob
              └── style.css  → blob
 ```

删除是文件状态不指向文件内容，实际文件内容仍存在作为历史快照

![76017529246](C:\Users\x\Desktop\notes\git\img\1760175292462.png)

### 分支操作

HEAD->分支->提交信息

​		main 	//指向最后提交 

​		user

![76017523616](C:\Users\x\Desktop\notes\git\img\1760175236167.png)

---

## 五、命令

 ![76017611585](C:\Users\x\Desktop\notes\git\img\1760176115853.png)

### 仓库操作

git init

​	初始化

​	默认HEAD分支master

​	工具有默认初始化提交

git clone

​	克隆远程仓库

git config	

​	仓库配置

​		--global

​				user.name

​				user.email

###文件操作

 git stauts

​	暂存区状态

git add 文件名

​	文件添加到暂存区

git rm --catched a.txt

​	移除暂存区，回到工作区

​			*.txt

​				会上传新增的.txt文件

git commit

​	提交到存储区域

​		-m 添加标签

###误删除

git restore

​	恢复工作区误删除文件，从存储区域获得

git reset

​	重置，提交操作同样消失

gir revert

​	还原到提交前的操作，但是提交操作没有丢失

### 分支操作

分支基于提交

git branch

​		user

​		-v	//带*为当前分支

git checkout branch

​	切换到当前分支

​		-b 'order'

​			创建分支并切换到

gir branch -d user

​	删除

### 分支-合并

git merge order

​	合并操作

### 标签操作

git log 版本号/标签

​	历史操作

git tag biaoqian

​	标签，给提交添加别名，标签不能重复

​	可以-d删除

###远程仓库

git remote add origin

​		remove

​		rename

git push

​	本地仓库推送远程仓库

.user/ssh认证文件/id_rsa.pub，复制提交到ssh公钥