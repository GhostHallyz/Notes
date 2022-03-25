git

## 1.Git 常用命令速查表

![Git-command](image\Git-command.png)

```
Git常用操作命令收集：
1) 远程仓库相关命令
检出仓库：   $ git clone git://github.com/jquery/jquery.git
查看远程仓库：$ git remote -v
添加远程仓库：$ git remote add [name] [url]
删除远程仓库：$ git remote rm [name]
修改远程仓库：$ git remote set-url --push[name][newUrl]
拉取远程仓库：$ git pull [remoteName] [localBranchName]
推送远程仓库：$ git push [remoteName] [localBranchName]
 
2）分支(branch)操作相关命令
查看本地分支：$ git branch
查看远程分支：$ git branch -r
创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
切换分支：   $ git checkout [name]
创建新分支并立即切换到新分支：$ git checkout -b [name]
删除分支：   $ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
合并分支：   $ git merge [name] ----将名称为[name]的分支与当前分支合并
创建远程分支(本地分支push到远程)：$ git push origin [name]
删除远程分支：$ git push origin :heads/[name]


提交本地test分支作为远程的master分支
$ git push origin test:master    
提交本地test分支作为远程的test分支
$ git push origin test:test 

如果想删除远程的分支呢？类似于上面，如果:左边的分支为空，那么将删除:右边的远程的分支。

$ git push origin :test              // 刚提交到远程的test将被删除，但是本地还会保存的，不用担心
3）版本(tag)操作相关命令
查看版本：$ git tag
创建版本：$ git tag [name]
删除版本：$ git tag -d [name]
查看远程版本：$ git tag -r
创建远程版本(本地版本push到远程)：$ git push origin [name]
删除远程版本：$ git push origin :refs/tags/[name]
 
4) 子模块(submodule)相关操作命令
添加子模块：$ git submodule add [url] [path]
如：$ git submodule add git://github.com/soberh/ui-libs.git src/main/webapp/ui-libs
初始化子模块：$ git submodule init ----只在首次检出仓库时运行一次就行
更新子模块：$ git submodule update ----每次更新或切换分支后都需要运行一下
删除子模块：（分4步走哦）
（1） $ git rm --cached [path]
（2） 编辑“.gitmodules”文件，将子模块的相关配置节点删除掉
（3） 编辑“.git/config”文件，将子模块的相关配置节点删除掉
（4） 手动删除子模块残留的目录
 
5）忽略一些文件、文件夹不提交
在仓库根目录下创建名称为“.gitignore”的文件，写入不需要的文件夹名或文件，每个元素占一行即可，如
target
bin
*.db

6）git删除文件
rm add2.txt
git rm add2.txt
git commit -m "rm test"
git push web
```



## 2.Git修改已经提交的用户名信息

```bash
# 第一步，（n）代表提交次数
git rebase -i HEAD~n
# 第二步
然后按'i'编辑，把'pick' 改成 'edit'，按'Esc'退出编辑，按':wq'保存退出
# 第三步
git commit --amend --author="作者 <邮箱@xxxx.com>" --no-edit
# 第四步
git rebase --continue
# 第五步
git push --force
```

## 3.GIt合并多次提交

```bash
# 第一步，（n）代表提交次数
git rebase -i HEAD~n
# 第二步
然后按'i'编辑，留下第一个'pick', 把后面的'pick' 改成 's'，按'Esc'退出编辑，按':wq'保存退出
# 第三步
删除不想留下的commit消息，只留下一个（可以重新修改留下的 commit 消息），按':wq'保存退出
# 第四步（可忽略）
git rebase --continue
# 第五步
git push --force
```

## 4.Git 回退代码到指定版本

```bash
# 查看所有的历史版本，获取你git的某个历史版本的id， 
git log
# 回退本地代码库：
git reset --hard ID
# 推送到远程服务器：
git push -f -u origin master
# 重新拉代码：
git pull
```

## 5.Git拉取某个分支的某段提交

```bash
# 要将A分支的提交 commit_id,拉取到 B 分支（获取A分支对应提交的commit_id，切换到B分支）
git cherry-pick <commit_id>
```

## 6.Git 远端覆盖本地仓库

```bash
git fetch --all
git reset --hard <remote>/<branch_name>
#示例：
git fetch --all
git reset --hard origin/master
```

## 7.Git 设置提交邮箱和用户

```bash
#查看当前仓库本地设置
git config --local --list
#查看全局配置
git config --global --list

#设置当前仓库
git config user.name 你的目标用户名；
git config user.email 你的目标邮箱名;

#设置全局
git config  --global user.name 你的目标用户名；
git config  --global user.email 你的目标邮箱名;
```

## 8.Git 将某个分支的代码完全覆盖另一个分支

```
# 将test分支上的代码完全覆盖dev分支，首先切换到dev分支
git checkout dev
# 然后直接设置代码给远程的test分支上的代码
git reset --hard origin/test
# 将本地分支强行推到远程分支。
git push -f
```

