### 项目流程

1. git init		初始化本地库
2. git status		检查是否有未追踪的(是否有未提交的)
3. git add *          把所有文件添加到暂存区
4. git commit -m "日志信息"       把所有文件添加到本地仓库
5. git remote add 别名 远程地址       给远程仓库起别名
6. git pull origin master --allow-unrelated-histories    吧远程仓库的文件同步到本地,这一步会出现一个文本文件和linux操作文件一样,按下:wq保存并退出
7. git push -u origin master     把文件push到远程仓库

--------------------------------





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
| git submodule add 远程地址 路径    |                                                           |
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

4.  第一次连接该主机，SSH 会提示你验证主机密钥。手动连接到主机以触发验证 

   ```sh
   ssh -T git@github.com
   ```

    如果是 GitHub，会显示类似以下的提示： 

   ```sh
   The authenticity of host 'github.com (IP地址)' can't be established.
   RSA key fingerprint is SHA256:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.
   Are you sure you want to continue connecting (yes/no)?
   ```

    输入 `yes` 并回车，SSH 会将该主机的密钥保存到 `C:\Users\用户名\.ssh\known_hosts` 文件中。 

5. 复制ssh连接`git@github.com:qiaozhi-4/quiz.git`在Git Bash创建远程连接





## 修改远程链接

```sh
# 查看当前远程仓库
git remote -v
# 修改远程仓库链接
git remote set-url origin <新的远程仓库URL>
git remote set-url origin git@github.com:qiaozhi-4/il2cpp-bridge.git
# 验证修改
git remote -v

# 添加新的远程仓库（可选）
git remote add <远程仓库名称> <新的远程仓库URL>
git remote add origin https://github.com/username/another-repo.git
# 删除旧的远程仓库（可选）
git remote remove <远程仓库名称>
git remote remove origin
```







# 子模块相关

### 1. **添加子模块**

将外部仓库添加为当前仓库的子模块：

```
git submodule add <仓库URL> [路径]
```

- `<仓库URL>`：子模块的 Git 仓库地址。
- `[路径]`（可选）：子模块在当前仓库中的存放路径。如果不指定，默认使用仓库名称作为路径。

示例：

```
git submodule add git@github.com:qiaozhi-4/script-store.git script-store

git submodule add https://github.com/eternalfuture-e38299/IL2CppReflector.git


git submodule add https://github.com/qiaozhi-4/imgui.git app/src/main/cpp/libs/imgui

git submodule add https://github.com/MJx0/KittyMemory.git app/src/main/cpp/libs/KittyMemory

git submodule add https://github.com/Tencent/rapidjson.git app/src/main/cpp/libs/rapidjson

git submodule add https://github.com/ByNameModding/BNM-Android.git app/src/main/cpp/libs/BNM-Android
```

------

### 2. **初始化子模块**

克隆包含子模块的仓库后，需要初始化子模块：

```
git submodule init
```

这会根据 `.gitmodules` 文件中的配置初始化子模块。

------

### 3. **更新子模块**

拉取子模块的内容：

```
git submodule update
git submodule update --init
git submodule update --init --recursive
```

常用参数：

- `--init`：如果子模块未初始化，先初始化再更新。
- `--recursive`：递归更新子模块中的子模块。
- `--remote`：拉取子模块的最新提交，而不是父仓库记录的提交。

示例：

```
git submodule update --init --recursive
```

------

### 4. **克隆包含子模块的仓库**

克隆父仓库时，同时初始化并更新子模块：

```
git clone --recurse-submodules <仓库URL>
```

------

### 5. **查看子模块状态**

查看子模块的状态（是否初始化、当前提交等）：

```
git submodule status
```

------

### 6. **更新子模块的远程 URL**

如果子模块的远程 URL 发生变化，可以更新：

```
git submodule sync
```

------

### 7. **删除子模块**

删除子模块需要手动操作：

1. 删除子模块目录：

   ```
   rm -rf path/to/submodule
   ```

2. 删除 `.gitmodules` 文件中的相关配置。

3. 删除 `.git/config` 中的子模块配置。

4. 提交更改：

   ```
   git commit -am "Removed submodule"
   ```

------

### 8. **拉取子模块的最新更改**

在父仓库中拉取子模块的最新提交：

```
git submodule update --remote
```

------

### 9. **进入子模块**

进入子模块目录进行操作：

```
cd path/to/submodule
```

在子模块中可以像普通 Git 仓库一样操作（如 `git pull`、`git checkout` 等）。

------

### 10. **递归操作**

如果子模块中还有子模块，可以使用 `--recursive` 参数递归操作：

```
git submodule update --init --recursive
```

------

### 11. **更新子模块到指定分支**

将子模块切换到指定分支并拉取最新代码：

```
git submodule foreach git checkout <分支名>
git submodule foreach git pull origin <分支名>
```

------

### 12. **忽略子模块的更改**

如果你不想提交子模块的更改，可以忽略：

```
git config submodule.<子模块路径>.ignore all
```