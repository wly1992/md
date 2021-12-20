
### 删除本地分支

> 如果需要删除的分支不是当前正在打开的分支，使用branch -d直接删除

```
git branch -d <branch_name>
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