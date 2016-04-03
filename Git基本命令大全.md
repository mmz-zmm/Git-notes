#  Git 常用基本命令总结

## 创建版本库
1. 在windows下，建议**将目录切换到自己的工作目录**。   
    ```
    $ mkdir learngit   # 创建一个空目录
    $ cd learngit      # 目录切换
    $ pwd              # 显示当前目录
    ```
2.  基本命令
    
- `git init`---- 初始化一个Git仓库
 
- `git add` ---- **工作区**向**暂存区**添加一个或多个文件
    ```
    $ git add readme.txt # 添加一个文件
    $ git add file1.txt file2.txt file3.txt # 添加多个文件
    $ git add '*.txt' # 添加后缀名一致的所有文件
    ```
- `git commit -m "提交说明"` ---- **暂存区**提交至**工作区**（-m 包含本次提交的说明）
- `git status` ---- 显示仓库当前的状态：文件的添加、删除、修改
- `git diff readme.txt` ---- 查看具体修改的内容（difference）
- `git log ` & `git log --pretty=oneline` ---- 显示从近到远的**提交日志**
- `cat readme.txt` ---- 查看文档的内容

## 版本退回
1. 想要实现版本的退回，必须知道版本的ID号码，Id可由 `git log` 查到。
```
$ git log --pretty=oneline
3628164fb26d48395383f8f31179f24e0882e1e0 append GPL
ea34578d5496d7dd233c827ed32a8cd576c5ee85 add distributed
cb926e7ea50ad11b8f9e909c05226233bf755030 wrote a readme file
```
2. `git reset` 命令
```
$ git reset --hard HEAD^
HEAD is now at ea34578 add distributed
```   
在 Git 中，`HEAD` 表示当前的版本，`HEAD^` 表示上一个版本，`HEAD^^` 表示上上个版本。      

3. 当回退到某个版本后，又**反悔**，想回到新的版本   

    `git reflog` ---- 记录每一次命令和commit ID

通过`git reflog` 可以查到想要恢复的版本的ID好，再通过 `git reset --hard commit_ID` 就可以反复的切换版本。


## 管理修改
1. Git 跟踪并管理的是文件的**修改**不是文件本身。
2. `git checkout -- file(readme.txt)` ---- （用**暂存区**文件覆盖）撤销对**工作区**file的修改。      
    2.1  readme.txt 自修改后，还**没有**被放进**暂存区**，因此撤消后回到和版本库一样的状态；       
    2.2  readme.txt **已经**添加到暂存区，又作了修改，撤消后回到和暂存区一样的状态；      
也就是说，让文件回到最近一次`git add` 和 `git commit` 的状态。    
3. 针对2.2 的问题，文件被add到暂存区，但没有commit，为了撤销暂存区的修改，可以使用 `git reset HEAD file(readme.txt)` 完成撤销(unstage)。
4. 总结
    - 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。
    - 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD file`，就回到了场景1，第二步按场景1操作。
    - 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考**版本回退**一节，不过**前提是没有推送到远程库**。   
## 删除文件
1. `rm filename.txt` ---- 在工作区删除文件，但并没有从git的记录中删除；可以用`git checkout -- file` 恢复删除的文件。
2. `git rm filename.txt` ---- 既在工作区删除文件，又删除暂存区记录；
    - 如果确实要从版本库中删除该文件，可接着执行`git commit`；
    - 如果不要删除，则先从版本库恢复暂存区文件，即`git reset HEAD file`, 再 `git checkout -- file` 。
## 远程仓库（Github）
1. 创建SSH Key：
    `$ ssh-keygen -t rsa -C "youremail@example.com"`
    生成两个文件 `id_rsa` 和 `id_rsa.pub` ，前者为私钥，后者为公钥。

2. 登陆Github-> Account setting -> SSH Keys，添加`id_rsa.pub` 公钥内容。
3. 添加远程库。
    登陆Github，点击“Create a new repo”。
4. 由于新创建的远程库为空，根据Github提示：          
    - 本地无库：
        ```
        echo "# 文件内容" >> README.md    # echo 用法，创建README.md并添加内容
        git init
        git add README.md
        git commit -m "first commit"
        git remote add origin git@github.com:moon-monster/Repo_name.git
        git push -u origin master
        ```   
    - 本地已经有库：
        ```
        git remote add origin git@github.com:moon-monster/Repo_name.git
        git push -u origin master    # 第一次push需要`-u`，之后不需要
        ``` 

5. 从远程库克隆

    ```
    git clone  git@github.com:moon-monster/Repo_name.git
    ```
## 分支管理
### 创建与合并分支
1. `git checkout -b <dev>` ---- **创建并切换**至dev
2. `git branch` ---- **查看**所有分支，当前分支前标有`*` 号
3. 在 dev 分支上的操作与 master 分支无异，分支工作完成后，`git checkout masterr`切换回 master 主分支。
4. `git merge <dev>` ---- 将指定分支**合并**到当前分支，一般采用`Fast-forward` 模式合并
5. `git branch -d <dev>` ---- **删除分支**（合并完成后即可删除）
### 解决冲突
1. 当master分支和dev分支**各自有了新的提交**，在合并是就会出现冲突，需要**手动解决**
2. `git log --graph` ---- 查看分支合并图
### 分支管理策略
`Fast forward` 模式下，分支删除后，会丢失分支的信息，因此可换用`git merge --no-ff <dev>`
### Bug 分支
1. 修复bug时，一般通过创建新的bug分支进行修复，之后合并，最后删除。
2. 当手头工作没有完成时，可以把工作现场`git stash` 一下，然后去修复bug，修复后，在`git stash pop` 回到工作现场（用于找回第一个stash文件的情况，如果stash了多次内容，需要先`git stash list` 查找文件，再`git stash apply stash@{0}`）,最后`git stash drop stash@{0}`。
3. `git stash list` ---- 查看“储藏”的所有工作现场。
4. `git branch -D branch_name` ---- 删除一个没有被合并过的分支（强删）；
    `git branch -D branch_name` ---- 删除一个被合并过的分支。
### 多人协作的一般模式
1. 首先，可以试图用`git push origin branch-name`推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
3. 如果`git pull`提示`“no tracking information”`，则说明**本地分支和远程分支的链接关系没有创建**，
用命令`git branch --set-upstream branch-name origin/branch-name`。
4. 如果合并有冲突，则解决冲突，并在本地提交；
5. 没有冲突或者解决掉冲突后，再用`git push origin branch-name`推送就能成功！
6. `git remote -v` ---- 查看远程库信息;
7. `git checkout -b branch-name origin/branch-name` ---- 在本地创建和远程分支对应的分支;
8. `git branch --set-upstream branch-name origin/branch-name` ---- 建立本地分支和远程分支的关联。

## 标签管理
- 标签是版本库的一个快照；
- `git tag <name> commit_ID` ---- 打一个新标签，默认为HEAD；
- `git tag` ---- 查看所有标签；
- `git show <tagname>` ---- 查看标签信息；
- `git tag -a v0.1 -m "version 0.1 released" 3628164` 创建带有说明的标签；
- `git tag -d v0.1` ---- 标签本地删除；
- `git push origin v1.0` ---- 推送某个标签到远程；
- `git push origin --tags` ---- 推送所有的标签到远程；
- 删除远程标签
    ```
    git tag -d v0.9   # 删本地
    git push origin :refs/tags/v0.9
    ```
### 忽略特殊文件
- 创建.gitignore 文件，模板在[Github/gitignore](https://github.com/github/gitignore);
- .gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理！
- 示例如下：
    ```
    # Windows:
    Thumbs.db
    ehthumbs.db
    Desktop.ini

    # Python:
    *.py[cod]
    *.so
    *.egg
    *.egg-info
    dist
    build

    # My configurations:
    db.ini
    deploy_key_rsa
    ```
## 配置别名
```
$ git config --global alias.st status   # 用st 表示status
$ git config --global alias.co checkout # 用co 表示checkout
$ git config --global alias.ci commit   # 用ci 表示commit
$ git config --global alias.br branch   # 用br 表示branch
``` 