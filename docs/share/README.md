## git常用命令

```
git config --global user.name "xxx"   //配置git 用户名
git config --global user.email "xxx@xx.com" //配置git 邮箱
git clone xxx.git  //克隆
git init //初始化
git pull //拉取
git pull origin xxx:xxx //拉取远程分支
git status //查看状态
git branch //查看本地分支
git branch -a //查看远程分支
git branch xxx //创建分支
git branch -d //删除本地分支
git checkout xxx //切换分支
git checkout -b xxx //创建并切换分支
git stash //暂存
git stash list //暂存列表
git stash apply //恢复暂存
git stash pop //删除一个缓存区
git stash drop stash@{0} //清除暂存区第一个
git add . //全部添加
git commit -m 'xxx'  //提交修改信息
git push origin xxx //推送到xxx分支
git log --oneline //查看简洁历史
git tag -a v1.0 //打版本标签
git merge //合并分支
git rebase master //修改主分支基础点
git remote rm origin  //删除源
git rm -r --cache . //移除所有文件

git reflog  //查看 `<hash_val>`
git branch <branch_name> <hash_val>  //恢复被删除的分支

git push origin --delete <branch_name>  //删除远程分支
git remote prune origin  //删除本地缓存的远程分支

git remote add [shortname] [url] # 添加远程版本库：
git remote rm name  # 删除远程仓库
git remote rename old_name new_name  # 修改仓库名

git reset [--soft | --mixed | --hard] [HEAD] # 版本回撤
git revert  # 撤销某次操作，此次操作之前和之后的commit和history都会保留，并且把这次撤销作为一次最新的提交
```

## git revert 和 git reset的区别 

1. git revert是用一次新的commit来回滚之前的commit，git reset是直接删除指定的commit。 
2. 在回滚这一操作上看，效果差不多。但是在日后继续merge以前的老版本时有区别。因为git revert是用一次逆向的commit“中和”之前的提交，因此日后合并老的branch时，导致这部分改变不会再次出现，但是git reset是之间把某些commit在某个branch上删除，因而和老的branch再次merge时，这些被回滚的commit应该还会被引入。 
3. git reset 是把HEAD向后移动了一下，而git revert是HEAD继续前进，只是新的commit的内容和要revert的内容正好相反，能够抵消要被revert的内容。