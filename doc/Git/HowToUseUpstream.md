# Git 如何使用upstream进行同步
---
背景：我们在fork源仓库后可能会面对如下状况：  
- 远程仓库更新了，自己的本地仓库没有更新，导致无法导致无法上传    
- 不知道怎么将本地文件更新到和源仓库一样      

总而言之本文的宗旨是教会大家如何使用upstream，使本地仓库、源仓库、本地文件保持同步    

---
upstream是什么？    

举个例子大家就可以知道，当你clone了自己的仓库到本地（git clone “自己的仓库”
，你会得到一个在本地得到托管该仓库的拷贝（也就是origin），也就是说origin指向你自己fork的
那份远程仓库。    
知道origin是什么以后，upstream是什么也就很容易理解了，upstream就是指向源仓库的本地托管
---
如何创建upstream？    

git remote add upstream 原作者源仓库的URL(不是自己fork的)    
这样就创建好了upstream，可以使用git remote -v查看

---      
如何使用upstream？    
通常我们在开发的时候仓库会有两个分支master和dev，我们通常需要对dev分支进行操作
，所以我以dev分支为例，教大家如何操作（如果想对master分支进行操作，只需要把一下的操作中dev改成master就行了）    

- 首先我们要在origin创建dev分支，使用命令git checkout -b dev
- 然后我们要从upstream将源分支搞到本地，使用命令    
git fetch upstream    
git merge upstream/dev    
这个时候本地已经更新为dev分支的内容了。
- 然后我们要将本地的内容同步到自己项目的dev分支上，使用命令    
git push origin dev

这个时候所有的内容就保持同步了。
---
希望大家继续补充内容。
