pkgship
===

# 介绍
pkgship是一款管理OS软件包依赖关系，提供依赖和被依赖关系完整图谱的查询工具，pkgship提供软件包依赖查询、生命周期管理、补丁查询等功能。
1. 软件包依赖查询：方便社区人员在软件包引入、更新和删除的时候了解软件的影响范围。
2. 生命周期管理：跟踪上游软件包发布状态，方便维护人员了解当前软件状态，及时升级到合理的版本。
3. 补丁查询：方便社区人员了解openEuler软件包的补丁情况以及提取补丁内容。

# 架构
系统采用flask-restful开发，使用SQLAlchemy ORM查询框架，同时支持mysql和sqlite两种数据库，可以通过配置文件的更改使用哪种数据库。

# 软件下载
* Repo源挂载地址：https://repo.openeuler.org/
* 源码获取地址：https://gitee.com/openeuler/openEuler-Advisor/tree/master/packageship
* rpm包获取地址：https://117.78.1.88/project/show/openEuler:Mainline

# 安装工具
工具安装可通过以下两种方式实现。

* 先使用dnf挂载pkgship软件在所在repo源（具体方法可参考[应用开发指南](https://openeuler.org/zh/docs/20.03_LTS/docs/ApplicationDev/%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87.html)），然后执行如下指令下载以及安装pkgship及其依赖。

    ```
    dnf install pkgship
    ```

* 先下载pkgship的rpm包，然后执行如下命令进行安装（其中“x.x-x”表示版本号，请用实际情况代替）。

    ```
    rpm -ivh pkgship-x.x-x.oe1.noarch.rpm
    ```

    或者

    ```
    dnf install pkgship-x.x-x.oe1.noarch.rpm
    ```

# 配置参数
1. 在配置文件中对相应参数进行配置，系统的默认配置文件存放在 /etc/pkgship/packge.ini，请根据实际情况进行配置更改。

    ```
    vim /etc/pkgship/package.ini
    ```

2. 创建初始化数据库的yaml配置文件：
    conf.yaml 文件默认存放在 /etc/pkgship/ 路径下，pkgship会通过该配置读取要建立的数据库名称以及需要导入的sqlite文件。conf.yaml 示例如下所示。

    ```
    - dbname:openEuler-20.03-LTS
     src_db_file:
    - /etc/pkgship/src.sqlite
     bin_db_file:
    - /etc/pkgship/bin.sqlite
     status:enable
     priority:1
    ```

> 如需更改存放路径，请更改package.ini下的 init_conf_path 选项


# 服务启动和停止
pkgship使用uWSGI web服务器,启动和停止命令如下所示。
```
pkgshipd start

pkgshipd stop
```
# 工具使用
1. 数据库初始化。

    ```
    pkgship init
    ```
    
2. 单包查询。

    查询源码包(sourceName)在所有数据库中的信息 。

    ```
    pkgship single sourceName
    ```

     查询当前源码包(sourceName)在指定数据库(dbName)中的信息。
    ```
     pkgship single sourceName -db dbName
    ```
    
3. 所有包查询。
    查询所有数据库下包含的所有包的信息。
    
    ```
     pkgship list
    ```

    查询指定数据库(dbName)下的所有包的信息。
    ```
    pkgship list -db dbName
    ```
    
4. 安装依赖查询。
    查询二进制包(binaryName)的安装依赖，按照默认优先级查询数据库。
    
    ```
      pkgship installdep binaryName
    ```

    在指定数据库(dbName)下查询二进制包(binaryName)的所有安装依赖，按照先后顺序指定数据库查询的优先级。
   ``` 
   pkgship installdep binaryName -dbs dbName1 dbName2...
   ```
   
5. 编译依赖查询。
    查询源码包(sourceName)的所有编译依赖，按照默认优先级查询数据库。
    
    ```
    pkgship builddep sourceName
    ```

   在指定数据库(dbName)下查询源码包(sourceName)的所有安装依赖，按照先后顺序指定数据库查询的优先级。
   ``` 
   pkgship builddep sourceName -dbs dbName1 dbName2...
   ```
   
6. 自编译自安装依赖查询。
    查询二进制包(binaryName)的安装和编译依赖，按照默认优先级查询数据库。
    
    ```
     pkgship selfbuild binaryName
    ```

     查询源码包(sourceName )的安装和编译依赖，按照默认优先级查询数据库。
     ``` 
     pkgship selfbuild sourceName -t source
     ```
     其他参数:

     -dbs 指定数据库优先级。

     ``` 
     示例:pkgship selfbuild binaryName -dbs dbName1 dbName2 
     ```
     -s 是否查询自编译依赖。其中0表示不查询自编译依赖，1表示查询自编译依赖，默认为0，可以指定1。
     
     ``` 
     查询自编译示例:pkgship selfbuild sourceName -t source -s 1
     ```
     -w 是否查询对应包的子包。其中0表示不查询对应子包，1表示查询对应子包，默认为0，可以指定1。
    
     ``` 
     查询子包示例:pkgship selfbuild binaryName -w 1
     ```
    
7. 被依赖查询。
    查询源码包(sourceName)在某数据库(dbName)中被哪些包所依赖，查询结果默认不包含对应二进制包的子包 。
    
    ```
     pkgship bedepend sourceName -db dbName
    ```

    使查询结果包含二进制包的子包加入参数 -w。
    ``` 
    pkgship bedepend sourceName -db dbName -w 1 
    ```
    
8. 包信息记录修改。
    变更数据库中(dbName)源码包(sourceName)的maintainer为Newmaintainer 。
    
    ```
     pkgship updatepkg sourceName db dbName -m Newmaintainer 
    ```

    变更数据库中(dbName)源码包(sourceName)的maintainlevel为Newmaintainlevel，值在1～4之间。
    
    ```
      pkgship updatepkg sourceName db dbName -l Newmaintainlevel 
    ```

    同时变更数据库中(dbName)源码包(sourceName)的maintainer 为Newmaintainer和变更  maintainlevel为Newmaintainlevel。
    
    ```
      pkgship updatepkg sourceName db dbName -m Newmaintainer -l   Newmaintainlevel
    ```
9. 数据库删除。
    删除指定数据库(dbName)。

    ```
    pkgship rm db dbName
    ```


