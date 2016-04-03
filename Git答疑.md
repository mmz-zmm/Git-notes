## 工作区、暂存区和版本库的区别
1.  `git diff` ---- 比较当前工作目录（working tree）与暂存区(index file)的文件差异；
2.  `git diff --cached` ---- 比较暂存区(index file)与上次提交间（版本库）(commit)的差异；
3.  `git diff HEAD`  ---- 比较工作目录(working tree)与上次提交(commit)之间的差异；
## 删除文件
1. `rm filename.txt` ---- 在工作区删除文件，但并没有从git的记录中删除；可以用`git checkout -- file` 恢复删除的文件。
2. `git rm filename.txt` ---- 既在工作区删除文件，又删除暂存区记录；
- 如果确实要从版本库中删除该文件，可接着执行`git commit`；
- 如果不要删除，则先从版本库恢复暂存区文件，即`git reset HEAD file`, 再 `git checkout -- file` 。
## Bug 分支
1. master 分支修复的bug 如何同步到dev分支？   
Ans：bug 修复完成后，将当前分支切换到dev分支`git checkout dev`，再将master合并至dev`git merge master`。
