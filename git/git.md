# git
git是一个开源的分布式版本控制系统，用于敏捷高效地处理任何或小或大的项目。

## 操作

### git init
初始化仓库。

### git add
`git add <file>`添加文件到暂存区中。
`git add .`表示添加目录下所有文件到暂存区中。
例：
`git add README.md`
### git commit
`git commit <file> -m "message"`
若`<file>`使用`-a`则表示所有暂存区中的修改
message格式：
```
<类型>[（可选）范围]: <概述>

[（可选）具体内容]

[（可选）页脚]
```
类型有：
`feat`：新特性。
`fix`: 修复bug。
`refactor`: 代码重构。
`docs`: 文档修改。
`style`: 代码格式修改。
`test`: 测试用例修改。
`chore`: 其他修改，比如构建流程，依赖管理。
例：
`git commit -a -m "fix:fixed random crash"`
### git push
`git push <远程主机名> <本地分支名>:<远程分支名>`<br/>
将本地的分支版本上传到远程并合并<br/>

### git rm
从暂存区中移除文件。
`git rm --cached <file_name>`
`file_name`：文件名。
`git rm -r --cached <directory_name>`
`-r`：递归，表示移除文件夹中所有在暂存区的文件。
`directory_name`：文件夹名。
例：
`git rm --cached README.md`
`git rm -r --cached test`