# git 分支管理
    [参考链接](https://git-scm.com/book/zh/v2/Git-%E5%88%86%E6%94%AF-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)
    [参考文章](https://blog.csdn.net/qq_32452623/article/details/54340749)

# 命令行
```sh
# 查看所在分支
git branch
git branch -a # 包含远程分支

# 根据所在分支，创建新分支
git checkout -b dev
git branch dev && git checkout dev
# 推送远程
git push --set-upstream origin dev

# 合并回主干
git checkout master
git merge dev

# 合并某次提交
# 查看commitID
git log
# 合并
git cherry-pick <commitID>

# 删除开发分支
git branch -d dev
# 删除远程分支
git push origin --delete dev
```