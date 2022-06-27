---
title: 重装系统后恢复MySQL
date: 2020-01-02 18:13:17
tags: 重装系统后的坑
---

**此方法只有在装MySQL时没有装到C盘中时适用**

本人由于考研加之电脑太垃圾就重装了一下系统，很多东西都需要从新配置，所以遇到很多坑，比如接下来的MySQL！！！！！！！！

#### 1.配置环境变量

这个应该都知道

①. 和其实环境变量的配置方法一样，我们打开环境变量配置窗口（组合键win+Pause -> 更改设置 -> 系统属性里选择“高级” -> 环境变量）

②. 选中系统变量中的“path”，编辑path值，新建输入mysql安装目录下的bin文件夹所在路径：D:\proApp\MySQL\bin，保存退出

**注意：把上面的路径改为你自己电脑中的安装路径**

但把这个做完后我以为就完成了，自信的在cmd中输入mysql -u root -p，结果报错：启动MySQL报错:ERROR 2003 (HY000): Can't connect to MySQL server on 'localhost' (10061)

<img src="img/mysql4.jpg">



![alt](mysql4.jpg)

#### 2.找到安装MySQL的文件夹中的my.ini文件  

在[mysqld]下更改basedir和datadir。basedir改为MySQL安装的位置，datadir改为MySQL目录下的data文件夹，比如我的。

![](D:\proApp\Hexo\fx\source\img\mysql1.jpg)

#### 3.管理员身份打开cmd安装mysql

```
mysqld --install mysql
```

可能提示已经安装：The service already exists!，由于本机中已经安装了mysql服务，所以没关系，继续！

启动MySQL

```
net start mysql
```

这时应该已经能成功启动了，是不是很激动！

#### 4.进入mysql

```
mysql -u root -p
```

这应该每个人都很熟悉，但是别以为成功了，这时你输入密码会发现以前的密码已经失效，接下来就是重设密码。

#### 5.设置权限认证跳过

还是在my.ini文件中的[mysqld]下加上 skip-grant-tables，这样就能跳过密码输入，不需要密码就能进入MySQL。

![](D:\proApp\Hexo\fx\source\img\mysql2.png)

#### 6.重启MySQL

```
net stop mysql
net start mysql
```

如图

![](D:\proApp\Hexo\fx\source\img\mysql3.jpg)

重启后， 以 mysql -u root -p 登陆
会发现我们可以不需要密码就可以登陆

#### 7.重设密码

##### 首先先选择 mysql 数据库

```
use mysql
```

##### 然后更新 password

①.

```
update user set authentication_string = password ( '新密码' ) where user = 'root';
```

②.

```
update user  set Password=password('新密码') where user='root';
```

#### 8.在 my.ini 文件中去掉 加上的 skip-grant-tables，重启 mysql 服务，以新密码登陆就大功告成了！

