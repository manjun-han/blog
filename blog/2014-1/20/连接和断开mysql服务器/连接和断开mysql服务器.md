###连接和断开MySQL服务器

客户端程序要连接到MySQL服务器的，它必须指定合适的连接参数，如mysql服务器的地址，用来连接的MySQL账户的用户名和相应的密码，以及连接的端口号。每个连接参数都有自己默认的值，但我们可以以选项options的形式来覆盖默认参数，不管是通过客户端命令行选项，还是选项文件中的指令行。

以下示例以**mysql**客户端程序为例，进行说明，但所有的原则适用于其它客户端，如**mysqldump**,**mysqladmin**,**mysqlshow**.

**mysql**是MySQL的命令行客户端程序或者叫做终端监视器，这是一个交互式的程序，通过**mysql**可以在操作系统提供的终端，通过命令行的方式连接到MySQL服务器上，完成对MySQL数据库服务器执行各种数据库的操作的任务。进行数据库操作的第一步是连接MySQL数据库服务器，操作完成后则要断开之前与MySQL服务器建立的链接。现对以命令行方式连接和断开MySQL数据库服务器的过程做简单的记录，本篇操作于Mac OS X下，MySQL服务器已正确的安装在本机，以`shell`指代终端命令行提示符。



####连接MySQL服务器

要在命令行中使用**mysql**客户端，首先将当前命令行的路径切到mysql安装目录下的bin目录，在Unix下，路径是`/usr/local/mysql/bin`

1. 默认连接

		shell> mysql
		
	以上连接命令未指定任何参数选项，连接会采用其默认
	
	+ 默认用户名是__localhost__
	+ 默认用户名在windows下是__ODBC__，Unix下是当前登录系统的**用户名**
	+ 省略连接密码
	+ 对于**mysql**来说，第一个非选项的参数会被识别为默认连接的数据库名字，如果没有非选项参数，那么**mysql**不会连接默认数据库

	>按照以上逻辑，假如当前Unix系统的登录名为`mjh`,那么以下的命令是等价的:
	>	
	>		shell> mysql -h localhost -u mjh
	>		shell> mysql -h localhost
	>		shell> mysql -u mjh
	>		shell> mysql
	>

2. 基本连接

	在使用**mysql**连接服务器时，需要指定如下几个基本选项：
	+ MySQL服务器所运行的主机名称
	+ MySQL账户用户名称
	+ 一个与用户名相匹配的密码
	命令的格式，如下

		shell> mysql -h host_name -u user_name -puser_pass
		
	以上，`-h`,`-p`,`-u`代表了命令行选项，`host_name`,`user_name`,`user_pass`分别对应这选项的值，其中：
	+ `host_name`代表MySQL服务器运行的主机名或ip地址，
	+ `user_name`代表了用来连接MySQL的账户名，
	+ `user_pass`是`user_name`的账户密码，

	在提供正确的参数并执行命令行后，**mysql**客户端会显示一些成功连接后的提示信息，而且**mysql>提示符**告诉你**mysql**已经准备好接受之后的命令输入,如
	
		shell> mysql -h localhost -u root -p123456
	
		Welcome to the MySQL monitor.  Commands end with ; or \g.
		Your MySQL connection id is 10
		Server version: 5.6.15 MySQL Community Server (GPL)
	
		Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.
	
		Oracle is a registered trademark of Oracle Corporation and/or its
		affiliates. Other names may be trademarks of their respective
		owners.
	
		Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
	
		mysql>

	**-p与`user_pass`**之间没有**空格**，如果以上命令行中指定了**`-p`**参数，但没有给出密码，那么在命令执行后，客户端会提示你输入密码，并且输入的密码不会显示在客户端(以下`******`代表未显示的密码)，如:
	
		shell> mysql -h localhost -u root -p
		Enter password: ******
		Welcome to the MySQL monitor.  Commands end with ; or \g.
		Your MySQL connection id is 10
		Server version: 5.6.15 MySQL Community Server (GPL)
	
		Copyright (c) 2000, 2013, Oracle and/or its affiliates. All rights reserved.
	
		Oracle is a registered trademark of Oracle Corporation and/or its
		affiliates. Other names may be trademarks of their respective
		owners.
	
		Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
	
		mysql>
		
	这种方式的好处是在命令行中隐藏了密码的显示，以确保密码的安全性。
	
	
3. 指定连接端口
	
	MySQL服务器的默认监听端口是`3306`，MySQL服务器安装时重新指定的新的端口号后，如`3000`，需要我们按照指定的端口进行连接,如下
		
		shell> mysql -h localhos -u root -p123456 -P 3000
		
	大写的`-P`选项，用来显示指定连接端口号，忽略该选项，则使用默认值为`3306`。`-P`选项的长命令模式为`--port=port_num`,所以等价的连接方式如下:
		
		shell> mysql -h localhos -u root -p123456 --port=3000
	
4. 指定连接后使用的数据库
	
	可以使用**mysql**指定在初始连接后使用的数据库，需要使用`-D`选项来指定数据库的名称,如我们要连接MySQL并使用`test`数据库，如
		
		shell> mysql -h localhost -u root -p123345 -D test
			
	`-D`选项对应的长命令方格式为`--databse=db_name`，等价连接方式如下:
		
		shell> mysql -h localhost -u root -p123345 --database=test
			
	也可以忽略`-D`选项，直接在命令行的末尾指定数据库名称，如下：
		
		shell> mysql -h localhost -u root -p123345 test
		
	此外，MySQL的命令行选项提供两种等价的命名方式，一种是添加以`--`(双破折号)开始的长选项，另一种是等价的以`-`(单破折号)开始的短选项，这两种方式的区别是长选项的名称和值需要用`=`隔离开来，所以以下命令是等价的:
		
		shell> mysql -h host_name -u user_name -puser_pass
			
		shell> mysql --host=host_name --user=user_name --password=user_pass
	例如：

		shell> mysql -h localhost -u root -p123456
	等价于
		
		shell> mysql --host=localhost --user=root -password=123456
			

	
####断开MySQL服务器连接

在使用**mysql**客户端程序操作完MySQL数据库后，可以在mysql>提示后输入`quit`命令、`exit`命令、或直接`\q`来断开链接，这三种方式是等价的，都可以用来断开连接，如下:

	mysql> quit
	Bye
	
或

	mysql> exit
	Bye
或

	mysql> \q
	Bye
	
`Bye`说明已经成功断开了连接,而且注意到三种命令的方式后面既可以添加`;`结束符，也可忽略`;`。

此外在诸如Mac OS X这样的Unix系统中，也可以在**mysql>**提示下直接使用快捷键`control+D`来断开服务器。

###参考

+ [Connecting to the MySQL Server][1]
+ [Specifying Program Options][2]
+ [Invoking MySQL Programs][3]
+ [Using Options on the Command Line][4]

[1]: http://dev.mysql.com/doc/refman/5.6/en/connecting.html
[2]: http://dev.mysql.com/doc/refman/5.6/en/program-options.html
[3]: http://dev.mysql.com/doc/refman/5.6/en/invoking-programs.html
[4]: http://dev.mysql.com/doc/refman/5.6/en/command-line-options.html
