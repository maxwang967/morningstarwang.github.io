---
layout: post
title:  "Oracle实现监听表变化并利用Java调用WebService获取数据思路"
date:   2019-07-31 20:30:28 +0800
categories: solution
---
## 场景定义
有两套网络隔绝的Oracle数据库（假设为数据库A和数据库B）（A可以连接互联网，B处于封闭局域网内不可连接互联网），两者进行数据交换是通过某设备将A的表的变化通过insert插入到B的对应表中，同样地，B在进行数据处理后要通过某设备将B的表的变化通过insert插入到A的对应表中。详细流程如下图所示。
![Not Avaliable](https://res.cloudinary.com/do3aoy3xf/image/upload/v1532685258/morningstarwang.github.io/_posts/Oracle实现监听表变化并利用Java调用WebService获取数据思路/Oracle实现监听表变化并利用Java调用WebService获取数据思路-1.png)

从整个流程中我们可以找到该项目的技术要点在于Oracle调用WebService取回JSON数据并进行解析。我们很容易的想到要利用Oracle触发器+存储过程来完成这一任务。
## 解决方案设计
在查阅相关资料后，我们找到了实现调用WebService的三种途径，它们分为两大思路。第一个思路是通过Oracle自身的存储过程调用，这需要引入UTL_DBWS或者UTL_HTTP进行调用。笔者曾经尝试了这两种方式，发现：这两种方式要求严格的SOAP报文格式，普适性并不够强。由于笔者所调用WebService的环境较为特殊，不能经常调用进行测试。因此，笔者抛弃了这两种普适性不强的策略。有兴趣的读者可以自行Google以上两个关键字，这里不再赘述。而第二个思路是通过Oracle调用Java程序进行WebService，因为Java环境下存在AXIS这样的第三方库，我们可以方便的获取WebService数据。这种思路的难点便变成了向Oracle中导入相关JAR包。
## 解决方案实现
首先介绍一下本次项目的环境：
- 操作系统：RHEL 6.4 (Linux)
- Oracle数据库：Oracle 11g
- Oracle JVM版本：jdk_1_5_17

### 确定Oracle JVM版本
确定Oracle JVM版本对于Java项目的编译至关重要，由于不同Oracle版本的数据库自带JVM版本不同（且比较低），因此，我们需要进行版本的确认。方法如下：
在终端依次输入以下命令：
①切换至Oracle用户
```bash
$ su oracle
$ [your password]
```
②初始化Oracle环境
```bash
$ . oraenv
$ [the name of instance]
```
③切换至$ORACLE_HOME目录
```bash
$ cd $ORALCE_HOME/jdk/bin
```
④调用java -version命令
```bash
$ ./java -version
```
至此我们可以确定JVM的版本，然后在后续编程中需要把eclipse的编译器和Library版本全部改为刚才查到的版本，小版本号也要确保一致。（jdk需要从Oracle官网下载指定版本）
### 编写Java程序
我们这里采用的是AXIS2方案，首先从WSDL文件生成相关的WebService类，然后新建一个Main类，声明一个main方法，在这里我只列举主要代码，如果各位读者需要进一步的帮助，请Email联系我。
```java
public  static void main(String arg1, String arg2){
    IDataQueryWSPortTypeProxy proxy = new IDataQueryWSPortTypeProxy();
    proxy.setEndpoint("http://XX.XX.XX.XX:XXXX/dataquery");
    JSONObject jo= new JSONObject();
    JSONArray ja = new JSONArray();
    ja.add(arg1);
    jo.put("aaaa", "123");
    jo.put("bbbb",ja);
    String senderID = "0523";
    String serviceID = "2356";
    String [] endUser = null; 
    String dataObjectCode = null;
    String [] requiredItems = {"cccc","dddd"};
    String condition = jo.toString();
    String infoCodeMode = null;
    String res = proxy.query(senderID, serviceID, endUser, dataObjectCode, condition, requiredItems, infoCodeMode, arg1, arg2);
}
```
注意，main函数的参数根据大家不同的需求最好更改为需要查询的条件参数，这样的函数在eclipse是无法运行的，但是在Oracle下可以运行，在大家调试Java程序时可以先使用String[] args参数进行，最后在打包生成JAR包前改为笔者这样的参数格式，方便Oracle调用。
由于笔者这里的搭建环境特殊，使用了eclipse进行Java的编写，因此笔者这里使用了Fatjar插件进行打包，可以轻松的将全部JAR引用包打在一起。打包成JAR包的过程这里不再赘述。
### 配置Oracle用户权限
考虑到Oracle数据库安全，在对数据库B进行操作时，要独立使用一个用户进行管理。现在要求该用户权限至少等同于默认SCOTT用户权限，并且还要附加以下权限。先以sysdba身份登录sqlplus然后执行以下命令授权（用户名必须为英文大写字母）（请将SCOTT替换为你所使用的用户）：
```sql
grant CREATE PUBLIC SYNONYM to SCOTT;
call dbms_java.grant_permission('SCOTT','SYS:java.lang.RuntimePermission', 'shutdownHooks', '' );
call dbms_java.grant_permission('SCOTT','SYS:java.util.logging.LoggingPermission', 'control', '' );
call dbms_java.grant_permission('SCOTT','SYS:java.util.PropertyPermission','http.proxySet','write');
call dbms_java.grant_permission('SCOTT','SYS:java.util.PropertyPermission','http.proxyHost', 'write');
call dbms_java.grant_permission('SCOTT','SYS:java.util.PropertyPermission','http.proxyPort', 'write');
call dbms_java.grant_permission('SCOTT','SYS:java.lang.RuntimePermission','getClassLoader','');
call dbms_java.grant_permission('SCOTT','SYS:java.net.SocketPermission','*','connect,resolve');
call dbms_java.grant_permission('SCOTT','SYS:java.util.PropertyPermission','*','read,write');
call dbms_java.grant_permission('SCOTT','SYS:java.lang.RuntimePermission','setFactory','');
call dbms_java.grant_permission('SCOTT','SYS:java.lang.RuntimePermission', 'accessClassInPackage.sun.util.calendar','');
```
请确认以上语句全部执行成功后在进行后续操作。
### 导入编译好的JAR包
在准备好Oracle环境后，用SSH将打包好的JAR包导入到$ORACLE_HOME/sqlj/lib下，在终端执行以下命令完成导包工作：
①切换至lib目录下
```bash
$ cd $ORACLE_HOME/sqlj/lib
```
②执行导包命令(假设JAR文件名为rpcinvoke.jar，用户名密码分别为SCOTT/tiger)
```bash
$ loadjava -u SCOTT/tiger -r -v -f -s -grant public -genmissing rpcinvoke.jar >& loadjava.txt
```
请耐心等待导包结束，在本文末尾我会提供我调用WebService时引用的全部第三方JAR包，各位读者可以参考使用。导包完成后的日志文件将存储在该目录下的loadjava.txt中，请查看并确定除了oracle jdbc以外其他包导入成功未报异常，这说明WebService的JAVA程序将可以成功运行。
### 编写存储过程
①编译JAVA SOURCE
在PL/SQL或者sqlplus以SCOTT（你所使用的用户）身份登录，执行下面的语句：
```sql
create or replace and compile java source named InvokeIDRPC as package wms;
import com.wms.wsdldemo.Main;
public class InvokeIDRPC{
    public static void invokeIDRPC(String arg1, String arg2){
        Main.main(arg1,arg2);
   }
}
```
注意其中的Main.main()方法，代表你所使用的类名.main方法，并且确认你需要传入的参数（这里是arg1和arg2），执行此语句可以将JAVA SOURCE编译到Oracle中。
②编写存储过程
```sql
create or replace procedure InvokeIDRPC_P( arg1 in VARCHAR2,arg2 in VARCHAR2) as language java name 'wms.InvokeIDRPC.invokeIDRPC(java.lang.String,java.lang.String)';
```
这里要注意指定参数在存储过程的类型以及在JAVA程序中的类型，对应我的字符串格式就是VARCHAR2和java.lang.String类型。同时也要注意wms指明了刚才java source的package，InvokeIDRPC指明了JAVA SOURCE类的名称，而invokeIDRPC指明了JAVA SOURCE类中方法的名称。
③编写触发器
```sql
create or replace trigger InvokeIDRPC_T
before insert
on request_data
for each row
begin
InvokeIDRPC_P(:new.arg1,:new.arg2);
end;
```
这个触发器指明了在request_data表插入数据后取得arg1和arg2字段的值并传入刚才编写好的存储过程中执行。
## 总结
以上是Oracle实现监听表变化并利用Java调用WebService获取数据的完整思路，笔者经过了实际项目测试，爬了好多坑，如果各位读者遇到其他问题可以随时email我。
## 文章涉及到的文件
- [JAR包](https://github.com/morningstarwang/oracle_wsdl/raw/master/JARs.zip)
- [Script](https://github.com/morningstarwang/oracle_wsdl/raw/master/Script.sql)
