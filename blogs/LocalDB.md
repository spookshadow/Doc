### LocalDB介绍

LocalDb是SqlServer Express版本的轻量级版本，用于植入到visual studio应用程序中去。
调试程序没有安装 sql server时,可以使用localdb.这是一个简易的sql server数据库,用于本地测试是很方便,省去安装SQL SERVER的工作。

#### LocalDB数据库实例查找
<p>安装好LocalDB后，默认有个实例名注意vs2015前默认实例名“v11.0”，之后为“mssqllocaldb”
查看LocalDB实例存放的位置%Localappdata%\Microsoft\Microsoft SQL Server Local DB\Instances,其下的每个文件加都是一个LocalDB实例，
如mssqllocaldb文件夹下主要包含：系统数据文档(master、model、msdb、tempdb)、错误记录、记录追踪、加密密钥等</p>
<b>注意这里不包含用户数据库</b>
<br/>
用户数据可以放在任意位置，如果在创建时为指定路径默认创建在%userprofile%目录下

#### 连接LocalDB
LocalDB连接的服务器名称为(localdb)\实例名称，可以使用sql server的“windows 身份验证”连接服务。
在.net网站下设置web.config的connectionstring可为：
````
Data Source=(LocalDb)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\EShop.mdf;Integrated Security=SSPI;
````
<b>注意:</b>最好指明AttachDbFilename，如果指明Initial Catalog(数据库名称)则创建的数据库在%userprofile%目录下增加麻烦

#### 管理LocalDB
通过SqlLocalDB.exe管理LocalDB;可以在cmd下通过where sqllocaldb.exe搜索路径

| 命令 | 作用 |
| ------ | ------ | ------ |
|sqllocaldb.exe |start 实例名|	启动数据库实例
|sqllocaldb.exe |info 实例名|	查询数据库实例信息
|sqllocaldb.exe |stop 实例名|	关闭数据库实例
|sqllocaldb.exe |create 实例名|	创建数据库实例
|sqllocaldb.exe |info|	列出所有数据库实例
|sqllocaldb.exe |delete 实例名|	删除数据库实例

> refer

* [LocalDB介绍](https://www.cnblogs.com/LoveTomato/p/9838210.html)
* [LocalDB安装](https://www.cnblogs.com/cncc/p/9517721.html)

