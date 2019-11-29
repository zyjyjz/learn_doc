
```shell
#初始化git仓库
git init
#添加文件到仓库
git add <file>
#提交文件到仓库 -m 本次提交说明
git commit -m "wrote a readme file"
#查看当前仓库状态
git status
#查看文件修改内容
git diff <file>
#查看提交日志 commit id 单行显示 HEAD显示的是当前版本
git log --pretty=oneline
#查看合并提交日志
git log --graph --pretty=oneline --abbrev-commit
git log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
#版本回退	上一个版本就是HEAD^，上上一个版本就是HEAD^^，上一百歌版本HEAD~100。
git reset --hard HEAD^
#记录每一次的git命令
git reflog
#丢弃工作区修改
git checkout -- <file>
#丢弃已提交到暂存区的修改
git reset HEAD <file>
#删除 删除也是修改
git rm <file>
#查看远程仓库 -v更详细的信息
git remote
#关联远程仓库
git remote add origin <repositoryurl>
#删除关联的远程仓库
git remote rm origin
#推送本地分支到远程 -u本地master分支和远程master分支关联 之后可简化
git push -u origin <branch>
#抓取分支内容
git pull
#从远程克隆仓库到本地
git clone <repository>
#创建一个分支
git branch <branch>
#列出所有分支 *代表HEAD指向当前分支
git branch
#删除分支
git branch -d <branch>
#强行删除未合并的分支
git branch -D <branch>
#关联远程分支到本地分支
git branch --set-upstream-to=origin/<branch> <branch>
#切换到该分支
git switch <branch>
git checkout <branch>
#创建分支并切换
git switch -c <branch>
git checkout -b <branch>
#关联远程分支并创建本地分支
git checkout -b <branch> origin/<branch>
git switch -c <branch> origin/<branch>
#合并指定分支到当前分支
git merge <branch>
#--no-ff参数，表示禁用Fast forward 新建一个commit
git merge --no-ff -m "merge with no-ff" <branch>
#储藏当前工作区
git stash
#查看储藏的工作区
git stash list 
#恢复到储藏的工作区 储藏的工作区不删除
git stash apply stash@{x}
#删除储藏的工作区
git stash drop stash@{x}
#恢复到储藏的工作区并删除储藏的工作区
git stash pop stash@{x}
#复制一个特定的提交到当前分支
git cherry-pick <commit id>
#rebase操作可以把本地未push的分叉提交历史整理成直线
#rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。
git rebase
#查看标签
git tag 
#当前分支创建标签
git tag <name>
#提交打标签
git tag <tagname> <commit-id>
#-a指定标签 -m说明文字 
git tag -a <tagname> -m "version 0.1 released" <commit-id>
#查看标签信息
git show <tagname>
#删除标签
git tag -d <tagname>
#推送标签到远程 --tags一次性推送全部
git push origin <tagname>
#删除远程标签
git push origin :refs/tags/<tagname>
#检验.gitignore规则对文件是否错误
git check-ignore -v <filename>
#命令别名
git config --global alias.<别名> <命令>
```





