
### git常用命令

```js
1. 创建远程仓库
2. 创建本地仓库
    a. 配置.gitignore
    b. git init
    c. git add .
    d. git commit -m "init"
3. 将本地仓库推送到远程仓库
    git remote add origin url
    git push origin master
4. 在本地创建dev分支, 并推送到远程
    git checkout -b dev
    git push origin dev
5. 如果本地有修改
    git add .
    git commit -m "xxx"
    git push origin dev
6. 新的同事: 克隆仓库
    git clone url
    git checkout -b dev origin/dev
    git pull origin dev
7. 如果远程修改
    git pull origin dev
git config --global user.
```

```js
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
git branch -d //删除分支
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
```

> 如果坚持要删除该分支的话，就需要在命令中使用-D选项

```
git branch -D <branch_name>
```

### 恢复被删除的分支

> git reflog查看 `<hash_val>`

```
git branch <branch_name> <hash_val>
```

### 删除远程分支

```
git push origin --delete <branch_name>
```

### 删除本地缓存的远程分支

```
git remote prune origin
```