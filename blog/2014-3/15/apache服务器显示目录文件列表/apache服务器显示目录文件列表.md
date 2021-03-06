###Apache服务器显示目录文件列表

####请求路径的默认处理

访问Apache的目录地址时，如访问`http://localhost/`,或访问`http://locahost/dir/`,默认会返回该目录下的`index.html`页面，也可以使用[DirectoryIndex]指令来改变访问目录时索要返回的文件名称，如：

	DirectoryIndex dirPage.html
	
如果保持默认设置情况下，目录不存在`index.html`，或目录不存在`DirectoryIndex`重新指定的文件，那么Apache服务器会将当前目录的文件列表返回回去。返回目录列表的行为是通过`Options`指令下的`Indexes`选项来控制的。`Indexes`选项的作用是：

>如果请求的URL与当前目录匹配，并且当前目录不存在`DirectoryIndex`指令指定的文件（如index.html），则调用[mod_autoindex](1)模块来返回一个格式化的目录列表。

可以看出`DirectoryIndex`指令的权重，要高于`Options Indexes`的权重，这在`DirectoryIndex`指令的定义中也有说明，如

>Description:	List of resources to look for when the client requests a directory

>Syntax:	DirectoryIndex local-url [local-url] ...

>Default:	DirectoryIndex index.html

>Context:	server config, virtual host, directory, .htaccess

>Override:	Indexes

>Status:	Base

>Module:	mod_dir

它们是一个override的关系。

####示例

比如在`DocumentRoot`有目录`a`,目录下面有多个子文件和目录，并且包含一个index.html文件并且配置如下

	<Directory "/Library/WebServer/Documents/a">
    	Options Indexes FollowSymLinks
	    AllowOverride None
    	Order allow,deny
	    allow from localhost
	    DirectoryIndex index.html
	</Directory>

那么在请求`http://localhost/a/`目录时，则返回`index.html`文件，如果将

	DirectoryIndex index.html
	
指向当前目录下不存的的文件，如

	DirectoryIndex notExsit.html	

那么在请求`http://localhost/a/`目录时，会将`a`目录下的文件结构以列表形式返回，这样会暴露整个目录的文件结构，屏蔽的方式是去掉`Indexes`选项，如
	
	<Directory "/Library/WebServer/Documents/a">
    	Options FollowSymLinks
	    AllowOverride None
    	Order allow,deny
	    allow from localhost
	    DirectoryIndex notExsit.html
	</Directory>
	
这样之后再次请求`http://localhost/a/`目录时，服务器会返回目录不存在的错误信息，如下

>Not Found

>The requested URL /a was not found on this server.
	
####参考

+ [Apache Module mod_autoindex](1)
+ [Options指令 - Options Directive](2)
+ [\<Directory\> Directive](3)

[1]: http://httpd.apache.org/docs/2.2/mod/mod_autoindex.html
[2]: http://httpd.apache.org/docs/2.2/mod/core.html#options
[3]: http://httpd.apache.org/docs/2.2/mod/core.html#directory