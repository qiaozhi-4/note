### 项目流程

1. git init		初始化本地库
2. git status		检查是否有未追踪的(是否有未提交的)
3. git add *          把所有文件添加到暂存区
4. git commit -m "日志信息"       把所有文件添加到本地仓库
5. git remote add 别名 远程地址       给远程仓库起别名
6. git pull origin master --allow-unrelated-histories    吧远程仓库的文件同步到本地,这一步会出现一个文本文件和linux操作文件一样,按下:wq保存并退出
7. git push -u origin master     把文件push到远程仓库

--------------------------------





### 索引文件损坏修复

```sh
rm -f .git/index
git read-tree HEAD
```





### github访问过慢

```sh
#刷新dns
ipconfig /flushdns
```

-----------------------------







### git每次提交都要输入密码

```sh
#设置全局的名字,随便写就是显示是谁提交的
git config --global user.name "乔治"
#设置全局的邮箱
git config --global user.email 18890176107@163.com
#设置全局验证过的证明
git config --global credential.helper store
```





### 回退到某次提交

```sh
#按q退出查看
git log
git log --oneline #一行的形式显示每个提交
#在命令中替换 <commit-hash> 为你想要回退到的提交的哈希值。
git reset --hard <commit-hash>
#强制推送到远程分支  注意：强制推送会改变远程分支的历史记录
git push -f origin master
```







### git常用命令

| 命令                               | 介绍                                                      |
| :--------------------------------- | --------------------------------------------------------- |
| git init                           | 初始化本地库                                              |
| git status                         | 是否有未追踪的                                            |
| git add 文件名                     | 把指定文件添加到暂存区                                    |
| git rm --cached 文件名             | 把指定文件从暂存区删除                                    |
| git commit -m "日志信息"           | 提交到本地仓库                                            |
| git commit -m "日志信息" 文件名    | 提交指定文件到本地仓库                                    |
| git reflog                         | 查看日志                                                  |
| git log                            | 查看详细日志                                              |
| git reset --hard 9199de9           | 切换到 9199de9  这个版本                                  |
| git branch 分支名                  | 创建分支                                                  |
| git branch -v                      | 查看分支                                                  |
| git checkout 分支名                | 切换分支                                                  |
| git merge 分支名                   | 把指定的分支合并到当前分支上                              |
| git remote -v                      | 查看当前所有远程地址别名                                  |
| git remote add 别名 远程地址       | 给远程地址起别名 一般 origin                              |
| git remote rm 别名                 | 删除远程别名                                              |
| git push 远程库地址别名 分支名     | 推送本地分支上的内容到远程仓库                            |
| git clone 远程地址                 | 将远程仓库的内容克隆到本地                                |
| git pull 远程库地址别名 远程分支名 | 将远程仓库对于分支最新内容拉下来后与 当前本地分支直接合并 |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |
|                                    |                                                           |

​        



​        







### 提交被拒绝

 切换到自己项目所在的目录，右键选择GIT BASH Here ，然后依次输入

```shell
git pull 
git pull origin master 
git pull origin master --allow-unrelated-histories 
git push -u origin master
```







git init                           //初始化仓库

```java
https://gitee.com/georgedbddbbd/baiyun
```

git add .(文件name)                //添加文件到本地 
git commit -m “first commit”      //添加文件描述信息
git remote add  origin 远程仓库地址 //链接远程仓库 

```java
git remote add origin https://gitee.com/georgedbddbbd/baiyun
```

git pull origin 仓库分支名      // 把本地仓库的变化连接到远程仓库master                                     分支

```java
git pull origin master     
```

git push -u origin 仓库分支名        //把本地仓库的文件推送到远程仓库master    

```java
git push -u origin master
```



### ssh 解决 github 提交失败

1. 打开Git Bash。

2.  将示例中使用的电子邮件替换为 GitHub 电子邮件地址。 

   ```sh
   ssh-keygen -t ed25519 -C "your_email@example.com"
   ssh-keygen -t ed25519 -C "18890176107@163.com"
   ```

   - 系统提示您“Enter a file in which to save the key（输入要保存密钥的文件）”时，可以按 Enter 键接受默认文件位置。 `C:\Users\用户名\.ssh`

   - 在提示符下，键入安全密码。 (如果设置密码,每次提交都要输入,我建议不输入)
   
   
      ```sh
      > Enter passphrase (empty for no passphrase): [Type a passphrase]
      > Enter same passphrase again: [Type passphrase again]
      ```

3. 设置完成之后会生成两个文件,默认文件位置和文件名,会在 `C:\Users\用户名\.ssh`路径下生成`id_ed25519.pub(公钥)`和`id_ed25519(私钥)`文件,打开公钥复制全部内容,添加到github的ssh密钥

4. 复制ssh连接`git@github.com:qiaozhi-4/quiz.git`在Git Bash创建远程连接