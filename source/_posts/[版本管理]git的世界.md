---
title: 【版本管理】gitの世界
categories: ['git']
tags: ['git']
toc: true
---

# 配置
```
git --version
git config --global user.name 'xxx'
git config --global user.email 'xxx'
git config --list
```
- local
- global
- system

# 工作区/暂存区
```
git add -u // 已经被管理的文件 update
```
- 重命名，git的命令类比linux的系统命令，可直接git commit 
```
git rm xxx // 删除
git mv old new
```

- git log
```
git log --oneline
git log -n // 最近几次commit
git log -all --graph// 所有分支
```

- git branch
```
git branch -v // 本地分支
git branch -av
```
- .git文件夹

1. HEAD 指向某个commit
```
git checkout -b branch 
git diff HEAD HEAD^^ / HEAD~n
```
2. config
3. refs
- heads 分支
- tags 里程碑
4. objects
blob文件对象 tree commit三者之间的关系
只要文件的内容相同，在git里就是唯一的，和文件名没有关系
加入暂存区的内容，才会再objects里面有体现
commit -> tree -> blob
```
git cat-file -t xxx 看文件的类型
git cat-file -p xxx 看内容
```

- 分离头指针 detached HEAD 没有经过某个分支做修改
git checkout commit-id 开发后切换分支易丢失，会提示创建新分支git branch name commit-id
在分支上做变更

- branch 清理

```
git branch -v
git branch -d
git branch -D
```
- 修改commit message
```
git commit --amend // 最近一次commit
git rebase -i   // 变基要修改的父commit，在为同步到远端，合并commit也可以实现
```

- diff
```
git diff --cached //暂存区和HEAD的不同
git diff // 工作区和暂存区的差异，不加参数是比较所有变动
git diff -- filename filename filename 
git diff branch_name/commit-id/ branch_name/commit-id -- filename
```

```
git reset HEAD // 暂存区恢复到工作区
git reset HEAD -- filename
git reset --hard/--soft commit-id
git checkout -- filename // 工作区内容恢复到暂存区一样
```

- stash
```
git stash
git stash list
git stash pop // stash 
git stash apply // stash 列表内的信息还在
```

- .gitignore

```
xxx/ // xxx的文件夹会被忽略
xxx // xxx的文件和文件夹都会忽略
```

- 备份
1. 哑协议：没有协议头 xxx/xx.git，看不到传输速度，速率没有智能协议高
2. 智能协议：带协议头 > file:///xxx/xxx.git
```
git clone --bare
git remote add xxx.git
```

- 远端操作，fast-forwards，解决冲突，集成的分支不要做变基，注意fast-forwards
```
git fetch
git merge
git pull
git push 
```

- git vs svn
1. svn需要获取批准
2. 权限问题，效率
3. 可以托管代码，协同合作

- 工作流

- rerere
```
git config --local rerere.enabled true
git merge // 解决冲突
git add
git commit
git reset
git rebase
```