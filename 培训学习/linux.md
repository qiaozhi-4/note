# linux文件说明

【/ 】系统的根目录
【/bin】 存放可执行文件
【/boot 】系统引导文件夹
【/dev】 存放设备文件
【/etc 】系统、软件配置相关文件
【/home 】用户文件的根目录，下面的每一个文件夹代表一个普通用户
【/root】 系统管理员、root用户的主目录
【/tmp】 临时文件夹
【/usr 】系统应用程序存放位置
【/var 】存放数据文件，比如日志文件
【/opt 】额外安装的可选应用程序位置
【/lib】 共享库，存放动态链接库











# linux相关命令

#### 网络

查看ip

windows 是 【ipconfig】
linux是 【ifconfig 】











#### 基本命令

【/ 】开头表示绝对路径，比如 /home ，Linux并没有直接的C盘、D盘一说

【~ 】表示当前用户的目录，一般是在 /home 文件夹下面，比如 /home/a 和 /root

【-】进入上一次进入的目录

【..】 表示上一级目录，即父目录

【./】 表示相对目录，相对当前】





#### 与文件，文件夹相关命令

文件夹查看命令：【ls 】文件形式、【ll 】列表形式

【pwd】 查看当前工作目录

【cd】 打开指定目录

【touch】 创建文件

【clear】 清空控制台，可以使用快捷键：【ctrl+L】

创建文件夹：【mkdir】，创建多级文件夹：【mkdir -p xxx/yyy】，删除文件夹：【rmdir】

【cat 】查看文件内容

【less】 查看部分文件内容，使用上下方向键移动，空格快速移动，q 键退出

【head/tail -10 文件名】：查看文件最前、最后10行

【cp】 源文件 新文件：拷贝文件

【mv】 源文件 目标文件：移动文件，或者重命名

【rm】 文件：删除文件，使用 【-rf】 表示强制删除（危险操作：【rm -rf /*】）

【./xxx 】或者 【/bin/xxx】 运行可执行文件









#### 用户相关

【useradd 】添加用户，-g 指定组，-G 指定附加组

【userdel -r】 删除用户

【usermod 】修改账户

【passwd】 修改密码

【groupadd/groupdel/groupmod】 添加、删除、修改组
查看所有用户基本信息：
【cat /etc/passwd】
用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
【cat /etc/group】
【id】 用户
示例：【useradd -g root -G g1,g2,g3 user001】







#### 权限：

|  -  -  -  |     ---  |  ---

|  r  w  x  |     rwx  |  r--
   |       |  |  |         |       |
文件类型   读 写 执行(用户)  组    其他用户

文件类型：-表示文件，d表示目录，l表示链接（快捷方式）
	r=4 表示读取权限
	w=2 表示修改写入权限
	x=1 表示执行权限
分组：
	u 当前用户
	g 当前组
	o 其他组
使用 ll 或者 ls -l 命令可以看到到每个文件、文件夹的权限
使用 chmod 可以改变文件、文件夹的权限：
	示例1：【chmod u=rwx,g=r,o=r 文件名】
	示例2：【chmod u=7,g=4,o=4 文件名】
	示例3：【chmod 744 文件名】
	所有权限：【chmod 777 a.txt】
	关闭权限：【chmod 000 a.txt】
使用chown改变文件、目录的用户和组：chown 用户名:用户组 文件
	示例：chown u1:g1 a.txt













#### VIM相关命令：

 安装参考：Linux_CentOS_7_安装教程.pdf

vi/vim 文件名：用于编辑文件

三种模式：
1. 命令行模式，默认状态就是命令行模式，在编辑模式下按下ESC进入该模式
	
	【i/o/a】 进入插入模式
	
	【:】 输入冒号进入底行模式
	
	【wq】 保存退出
	
	【q 】退出
	
	【q!】 强制退出
2. 插入模式：在命令行模式下，输入 【i/o/a 】进入编辑模式
3. 底行模式：在命令模式下输入“:”可以进入该模式，在底部输入命令，可以进行查找、特殊指令等操作

常用命令：
【gg 】- 第一行
【G】 - 最后一行
【dd】 - 删除一行，x/X - 删除一个
【/字符 】- 向下查找，【?字符】 - 向上查找
【v】 - 块选择
【y】 - 复制
【u】 - undo回退
【p】 - 粘贴
【f】 - 查找
数字【h/l/j/k】 上下左右，加数字表示移动多少
参考：https://juejin.cn/post/7001841807994814501











#### 打包、解压、查找、进程

​	tar打包或者解压缩：
​		【tar -cvf 目标文件名  源文件或者文件夹】：压缩源文件到指定文件名
​		【tar -zcvf】：打包后压缩
​		【tar -xvf tar文件】：解压
​		【tar -zxvf tar文件】：解压gz压缩文件
​	使用find查找文件，grep查找内容：
​		【find  xxx.txt】 查找指定文件夹下指定名字的文件，可以使用正则：比如 *.log
​		【grep "xxx" xxx.txt】 在指定文件中查找指定的内容，参数--color表示颜色，-A1表示后1行，-B2表示前2行











#### 重定向、管道

​	【>】表示重定向输出，将指定内容输出到指定目标，比如 cat a.txt > b.txt
​	【>> 】同上，但是不会覆盖，而是追加，比如 echo "xxx" >> b.txt
​	【| 】表示管道，连接两个命令，相当于将第1个命令的结果作为第2个命令的参数输入
​		示例：ps -ef | grep java 将所有的进程中查找含有java的显示出来
​	【&& 】逻辑控制，拼接两个操作，如果坐标的操作没有执行成功，后面的操作将不会继续
​		示例：mkdir tmp1 && cd tmp1 如果第一个命令失败，第二个不会继续；否则都会运行















#### 进行、配置：

​	【ps 】查看进程，参数 -ef 查看所有的进程
​	【kill】 杀死进程，比如：【kill 1111】，使用 【kill -9 1111】 强制杀死1111进程
​	【ifconfig/ip addr 】查看显示、设置网络设备
​	【netstat】 查看网络端口，常用命令： netstat an | grep 9090 查询所有9090端口占用应用
​	【service】 开启、停止服务，比如：service iptables stop 关闭防火墙
​	【systemctl】 开启、停止服务
​	配置文件：
​		/etc/sysconfig/network-scripts/ifcfg-eth0 配置网络
​		/etc/sysconfig/network 主机相关信息
​		/etc/hosts 配置网络主机对应IP地址等











#### 安装软件：

​	使用yum安装软件：【yum -y install xxx】
​	可以搜索可以用的安装包：【yum search jdk*】
​	安装mysql：

如果需要复制Win10中的文件到Linux虚拟机，可以使用如下命令：
scp 要复制的文件 root@虚拟机ip:~/路径
比如：【scp win10.txt root@192.168.0.200:~/linux/temp】

如果忘记密码：
mysqld_safe --skip-grant-tables & 
use mysql;
UPDATE user SET password=password('12345678') WHERE user='root';
flush privileges; exit;
需要关闭服务、kill杀死mysqld进程









# mysql

#### 安装

```
[root@localhost src]# yum  install mariadb-server    安装mariadb数据库
[root@localhost src]# yum  clean   all               清空已安装文件如果下载失败之后执行的
```









####  数据库启动

	1.   启动命令    [root@localhost src]# systemctl  start  mariadb
	2.   重启命令    [root@localhost src]# systemctl  restart  mariadb
	3.   关闭命令    [root@localhost src]# systemctl  stop  mariadb
	4.   设定开机自起 [root@localhost src]# systemctl  enable mariadb 
	5.   关闭开机自起 [root@localhost src]# systemctl  disable mariadb 
















#### 数据库初始化操作

```
//初始化mysql
mysql_secure_installation


Enter current password for root (enter for none): 
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MariaDB
root user without the proper authorisation.
出现这些按回车即可

//设置密码
Set root password? [Y/n] y
New password: 【这里输入密码】
Re-enter new password: 【这里再次输入密码】
Password updated successfully! 		//密码更新成功！
Reloading privilege tables..
 ... Success!		//重选特权表。...成功！



By default, a MariaDB installation has an anonymous user, allowing anyone
to log into MariaDB without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] y  //删除匿名用户
 ... Success!

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n //禁止root远程登录?
 ... skipping.

By default, MariaDB comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] y //删除测试数据库并访问它?
 - Dropping test database...
 ... Success!
 - Removing privileges on test database...
 ... Success!

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y //现在重新加载特权表?
 ... Success!

Cleaning up...

All done!  If you've completed all of the above steps, your MariaDB
installation should now be secure.

Thanks for using MariaDB!

完成！！

```









#### 数据库连接

```sql
[root@localhost src]#  mysql -u root -p
```











#### 建库

创建数据库：create database 【数据库名】;
查看所有数据库：show databases;
删除数据库：drop database 【数据库名】;







#### 建表

打开数据库：use【数据库名】

查看所有表： show tables; 

如果表不存在就创建

```sql
CREATE TABLE if not exists `user1`  (
  `id` int NOT NULL AUTO_INCREMENT,
  `username` varchar(30) NOT NULL,
  `password` varchar(66) NOT NULL,
  `money` double NOT NULL DEFAULT 0,
  PRIMARY KEY (`id`) USING BTREE
);

-- 插入
INSERT INTO `user1` VALUES (1, 'admin', '123', 980);
```









#  环境变量

【 /etc/profile 】











# 部署tomcat

#### 启动tomcat

查看进程：【 jps】

下面是进程名以及pid

1675 Bootstrap
3326 Jps



杀死tomcat进程：【kill -9 1675】

启动：【 sh /usr/local/apache-tomcat-9.0.54/bin/startup.sh】







#### 项目打包到linux

pom.xml设置：<packaging>war</packaging>【设置之后打包就是war包】

把war包上传到【/usr/local/apache-tomcat-9.0.54/webapps】然后重新运行tomcat

访问方法：

​	【192.168.113.200:8080/linux1-1.0-SNAPSHOT/】

​	【linuxIP地址：端口号/war包的名字/】











# 部署nginx

#### 启动nginx

启动：【 /usr/local/nginx/sbin/nginx 】

关闭：【 /usr/local/nginx/sbin/nginx  -s stop】

重启：【 /usr/local/nginx/sbin/nginx  -s relosd】



   














# Git

安装软件和配置：
	JDK
		安装命令：yum install -y java-1.8.0-openjdk-devel.x86_64
		版本：java -version
		安装位置：/usr/lib/jvm
		设置环境变量：
			vim /etc/profile
			查找JAVA安装位置：/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.xxx
			export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.xxx
			export PATH=$PATH:$JAVA_HOME/bin
			source /etc/profile
	TOMCAT
		Win10拷贝：scp apache-tomcat-9.0.54.tar.gz root@192.168.0.200:/opt
		新建文件夹，解压：
			cd /usr/local
			mkdir tomcat
			cd tomcat
			tar -zxvf /opt/apache-tomcat-9.0.54.tar.gz
		配置：
			vim /etc/profile
			export CATALINA_HOME=/usr/local/tomcat/apache-tomcat-9.0.54
			source /etc/profile
		启动服务器并访问：
			./startup.sh
			http://192.168.0.200:8080/
			./shutdown.sh
	GIT
		老版本：
			yum install -y git
			git version 查看版本，1.8旧版本
			yum remove git 必须卸载旧版本
		新版本：
			安装依赖：yum install -y curl-devel expat-devel zlib-devel bzip2-devel gettext-devel openssl-devel ncurses-devel gcc perl-ExtUtils-MakeMaker package
			安装工具：yum install -y wget
			cd /opt
			下载：wget https://www.kernel.org/pub/software/scm/git/git-2.33.1.tar.gz --no-check-certificate
			cd /usr/local/git
			解压：tar -zxvf /opt/git-2.33.1.tar.gz
			cd git-2.33.1
			编译：
				make prefix=/usr/local/git all
				make prefix=/usr/local/git install
			环境配置：
				vim /etc/profile
				export PATH=$PATH:/usr/local/git/bin
				source /etc/profile
				git version
			个人配置：
				vim ~/.gitconfig
				git config --global user.name "xxx"
				git config --global user.email "xxx@xxx.com"
				git config --list
		

GIT工作流程
	1．从远程仓库中克隆git为本地仓库
	2．从本地仓库中checkout代码，然后进行代码修改
	3．在提交前先将代码提交到stage暂存区
	4．提交修改add，提交到本地仓库commit，本地仓库中保存修改的各个历史版本
	5．将代码push到远程仓库，共享代码，其他开发中pull拉取更新


远程服务：
	1. 注册gitee账号：xxx@gitee.com
	2. 创建远程创库：用户名/仓库名
		3. （暂时使用用户名、密码）生成密钥：ssh-keygen -t rsa
		4. （暂时使用用户名、密码）复制密钥到gitee设置上：SSH and GPG Keys
	5. 克隆到本机：mkdir repos && git clone https://gitee.com/用户名/仓库名.git
		5.1 或者拉取仓库：git fetch
		5.2 或者拉取并覆盖：git pull
	6. 配置远程仓库：git remote add origin https://gitee.com/用户名/仓库名.git
	7. 添加内容、修改内容、提交内容：touch xxx.txt; vim xxx.txt; git add .; git commit -m "xxx"
	8. 推送到远程仓库：git push -u origin master
	说明：在使用SSH后将【https://gitee.com/用户名/仓库名.git】 替换为【git@gitee.com:用户名/仓库名.git】
		1. git remote rm origin
		2. git remote add origin git@gitee.com:用户名/仓库名.git
		3. git push -u origin master

常用命令：
	git init 初始化一个仓库
	git status 查看当前状态
	git log 查看日志
	git add <file> 添加指定文件或者文件夹
	git reset HEAD 恢复到上一个状态
	git rm --cached <file> 删除文件、缓存
	git checkout . 或者 git checkout -- <file> 查看文件
	git checkout HEAD . 或者 git checkout HEAD <file>
	git checkout <branchname> 查看分支

git branch <branchname> 创建一个分支
git branch -d <branchname> 删除一个分支
git tag -a v1.0 创建一个标签
git merge 合并
git pull 拉取并覆盖
git fetch 拉取
git push 推送

私服：
	创建git用户和git分组，专门用于处理git仓库：
		groupadd git       # 创建用户组
		useradd git -g git # 创建用户
		passwd git         # 设置密码
	配置服务端：
		su git             # 切换到git用户
		cd ~/.ssh          # 创建.ssh文件夹
		touch authorized_keys  # 新建授权文件
		chmod 700 ~/.ssh       # 设置权限
		chmod 600 ~/.ssh/authorized_keys
	客户端配置：
		ssh-keygen -t rsa -C "邮箱@..." # 运行后三次回车
		打开文件夹：C:\Users\用户名.ssh，看到id_rsa.pub公钥，右键Git Bash Here
		scp test.txt root@192.168.0.200:/home/git/.ssh # 拷贝到服务器端
	服务器配置SSH：
		su git
		cd ~/.ssh
		cat id_rsa.pub >> authorized_keys # 将公钥添加到 authorized_keys 文件
		su                                # 切换为root用户
		vim /etc/ssh/sshd_config          # 配置
			RSAAuthentication yes					# 秘钥认证（centos7.4及以上，该配置默认开启）
			PubkeyAuthentication yes				# 允许密码认证
			AuthorizedKeysFile .ssh/authorized_keys	# 公钥存放路径
			PermitUserEnvironment yes               # 环境
		systemctl restart sshd.service    # 重启ssh服务
		su git                            # 切回git
		cd ~ && mkdir -p repo/git-demo.git# 创建文件夹
		cd repo/git-demo.git              # 打开文件夹
		git init --bare                   # 初始化仓库
	客户端使用：
		ssh -T git@192.168.0.200          # 测试链接
		git clone git@192.168.0.200:~/repos/git-demo.git # 克隆仓库，以及其他操作
	如果遇到git-upload-pack: command not found的问题：
		su
		vim /etc/profile # 修改内容：
			export PATH=$PATH:/usr/local/git/bin
		su git
		env > ~/.ssh/environment
		systemctl restart sshd.service





