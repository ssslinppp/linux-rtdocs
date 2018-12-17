## 概述

[果壳中的 Git](https://github.com/geeeeeeeeek/git-recipes/wiki)   


---
  
## Git仓库

```
# 创建新的仓库
git init

# 添加到缓存区
git add readme.txt

# 提交到本地仓库
git commit -m "add readme.txt, firtst commit"
   
# 将项目分享到远程仓库（origin），其对应的地址为 https://xxxxx   
git remote add origin https://xxxxx   

# clone指定分支
git clone -b develop ssh://xxx@xxxxx

# 图像化展示 提交历史
git log --graph --oneline --decorate --all --pretty=oneline

# 提交本地分支develop 到远程仓库（origin）的refs/for/develop分支
git push origin develop:refs/for/develop

```

---

## 代码提交

修改最后一次提交的信息(可以保持 change-id 不变)
```
git commit --amend
```

---

## 分支操作
#### 查看所有分支
```
git branch -a
```

#### 查看本地分支
```
git branch
```

#### 切换分支
```
git checkout newBranch
```

---

## 分支间的cherry-pick

在release_1.3.1提交了代码，希望将这次的代码提交合并到develop分支上

```
git checkout release_1.3.1     （切换到release_1.3.1分支上）
git add xxx
git commit -m "xxxx" (假设该次提交对应的 commit-id=caaaa)
git push origin release_1.3.1:ref/for/release_1.3.1

git checkout develop
git cherry-pick caaaa  (将release_1.3.1的修改，同步到develop分支)
git push origin develop:refs/for/develop
```

---

## 代码回退

```
git reset cb7c3540f4cd67f9c9aa3780484fc08bcab80e3f  # 回退到指定的提交，扔掉该提交之后的所有提交

git reset HEAD~2  # 扔掉最后两次的提交
相关选项

--soft – 缓存区和工作目录都不会被改变
--mixed – 默认选项。缓存区和你指定的提交同步，但工作目录不受影响
--hard – 缓存区和工作目录都同步到你指定的提交

git reset --mixed HEAD  #将你当前的改动从缓存区中移除
git reset --hard HEAD   #完全舍弃你没有提交的改动

```
[代码回滚：Reset、Checkout、Revert的选择](https://github.com/geeeeeeeeek/git-recipes/wiki/5.2-%E4%BB%A3%E7%A0%81%E5%9B%9E%E6%BB%9A%EF%BC%9AReset%E3%80%81Checkout%E3%80%81Revert%E7%9A%84%E9%80%89%E6%8B%A9)