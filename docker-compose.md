# 使用Docker Compose的多容器应用程序

目录

- [什么是Docker Compose](https://github.com/docker/labs/blob/master/developer-tools/java/chapters/ch05-compose.adoc#what-is-docker-compose)
- [配置文件](https://github.com/docker/labs/blob/master/developer-tools/java/chapters/ch05-compose.adoc#configuration-file)
- [开始申请](https://github.com/docker/labs/blob/master/developer-tools/java/chapters/ch05-compose.adoc#start-application)
- [验证申请](https://github.com/docker/labs/blob/master/developer-tools/java/chapters/ch05-compose.adoc#verify-application)
- [关机应用](https://github.com/docker/labs/blob/master/developer-tools/java/chapters/ch05-compose.adoc#shutdown-application)

## 什么是Docker Compose

> Docker Compose是用于使用Docker定义和运行复杂应用程序的工具。使用Compose，您可以在一个文件中定义一个多容器应用程序，然后在一个命令中旋转应用程序，该命令完成了运行该应用程序所需的一切。

— github.com/docker/compose

使用Docker容器的应用程序通常将包含多个容器。使用Docker Compose，无需编写Shell脚本来启动您的容器。使用*services*在配置文件中定义所有容器，然后使用`docker-compose`脚本来启动，停止和重新启动应用程序以及该应用程序中的所有服务以及该服务中的所有容器。命令的完整列表为：

| 命令      | 目的                       |
| --------- | -------------------------- |
| `build`   | 构建或重建服务             |
| `help`    | 获取有关命令的帮助         |
| `kill`    | 杀死容器                   |
| `logs`    | 查看容器的输出             |
| `port`    | 打印公共端口以进行端口绑定 |
| `ps`      | 列出容器                   |
| `pull`    | 拉服务图像                 |
| `restart` | 重新启动服务               |
| `rm`      | 取出停止的容器             |
| `run`     | 运行一次性命令             |
| `scale`   | 设置服务容器的数量         |
| `start`   | 启动服务                   |
| `stop`    | 停止服务                   |
| `up`      | 创建并启动容器             |

本节中使用的应用程序是与数据库对话的Java EE应用程序。该应用程序发布了可以使用`curl调用的REST端点。它使用与MySQL数据库通信的[WildFly Swarm](http://wildfly-swarm.io/)进行部署。

WildFly Swarm和MySQL将在两个单独的容器中运行，因此使它成为一个多容器应用程序。

## 配置文件

Docker Compose的入口点是一个Compose文件，通常称为`docker-compose.yml`。创建一个新目录`javaee`。在该目录中，创建一个新文件`docker-compose.yml`。使用以下内容：

```
version: '3.3'
services:
  db:
    container_name: db
    image: mysql:8
    environment:
      MYSQL_DATABASE: employees
      MYSQL_USER: mysql
      MYSQL_PASSWORD: mysql
      MYSQL_ROOT_PASSWORD: supersecret
    ports:
      - 3306:3306
  web:
    image: arungupta/docker-javaee:dockerconeu17
    ports:
      - 8080:8080
      - 9990:9990
    depends_on:
      - db
```

在此撰写文件中：

1. 此Compose中的两个服务由名称`db`和`web`属性定义
2. 使用`image`属性定义的每个服务的图像名称
3. 该`mysql:8`映像启动MySQL服务器。
4. `environment` 属性定义环境变量以初始化MySQL服务器。
   1. `MYSQL_DATABASE` 允许您指定要在映像启动时创建的数据库的名称。
   2. `MYSQL_USER`和`MYSQL_PASSWORD`一起用于创建新用户并设置该用户的密码。将为该用户授予该`MYSQL_DATABASE`变量指定的数据库的超级用户权限。
   3. `MYSQL_ROOT_PASSWORD` 是必填项，并指定将为MySQL超级用户超级帐户设置的密码。
5. Java EE应用程序使用`db`在指定的服务`connection-url`在为指定https://github.com/arun-gupta/docker-javaee/blob/master/employees/src/main/resources/project-defaults.yml/。
6. 该`arungupta/docker-javaee:dockerconeu17`映像将启动WildFly Swarm应用程序服务器。它包含从https://github.com/arun-gupta/docker-javaee构建的Java EE应用程序。如果要构建自己的映像，请克隆该项目。
7. 使用`ports`属性实现端口转发。
8. `depends_on`属性允许表达服务之间的依赖关系。在这种情况下，MySQL将在WildFly之前启动。应用程序级运行状况检查仍由用户负责。

## 开始申请

可以通过以下命令以分离模式启动应用程序中的所有服务：

```
docker-compose up -d
```

可以使用`-f`option 指定备用的Compose文件名。

可以使用`-p`option 指定组成文件所在的备用目录。

输出显示为：

```
docker-compose up -d
Creating network "javaee_default" with the default driver
Creating db ...
Creating db ... done
Creating javaee_web_1 ...
Creating javaee_web_1 ... done
```

如果还下载了图像，则输出可能会略有不同。

可以使用以下命令来验证已启动的服务`docker-compose ps`：

```
    Name                  Command               State                       Ports
------------------------------------------------------------------------------------------------------
db             docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp
javaee_web_1   /bin/sh -c java -jar /opt/ ...   Up      0.0.0.0:8080->8080/tcp, 0.0.0.0:9990->9990/tcp
```

这提供了所有服务及其每个中的容器的统一视图。

另外，可以使用常规`docker container ls`命令来验证此应用程序中的容器以及在此Docker主机上运行的所有其他容器：

```
    Name                  Command               State                       Ports
------------------------------------------------------------------------------------------------------
db             docker-entrypoint.sh mysqld      Up      0.0.0.0:3306->3306/tcp
javaee_web_1   /bin/sh -c java -jar /opt/ ...   Up      0.0.0.0:8080->8080/tcp, 0.0.0.0:9990->9990/tcp
javaee $ docker container ls
CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                                            NAMES
e862a5eb9484        arungupta/docker-javaee:dockerconeu17   "/bin/sh -c 'java ..."   38 seconds ago      Up 36 seconds       0.0.0.0:8080->8080/tcp, 0.0.0.0:9990->9990/tcp   javaee_web_1
08792c20c066        mysql:8                                 "docker-entrypoint..."   39 seconds ago      Up 37 seconds       0.0.0.0:3306->3306/tcp                           db
```

服务日志可以使用`docker-compose logs`命令查看，如下所示：

```
Attaching to dockerjavaee_web_1, db
web_1  | 23:54:21,584 INFO  [org.jboss.msc] (main) JBoss MSC version 1.2.6.Final
web_1  | 23:54:21,688 INFO  [org.jboss.as] (MSC service thread 1-8) WFLYSRV0049: WildFly Core 2.0.10.Final "Kenny" starting
web_1  | 2017-10-06 23:54:22,687 INFO  [org.wildfly.extension.io] (ServerService Thread Pool -- 20) WFLYIO001: Worker 'default' has auto-configured to 8 core threads with 64 task threads based on your 4 available processors

. . .

web_1  | 2017-10-06 23:54:23,259 INFO  [org.jboss.as.connector.subsystems.datasources] (MSC service thread 1-3) WFLYJCA0001: Bound data source [java:jboss/datasources/ExampleDS]
web_1  | 2017-10-06 23:54:24,962 INFO  [org.jboss.as] (Controller Boot Thread) WFLYSRV0025: WildFly Core 2.0.10.Final "Kenny" started in 3406ms - Started 112 of 124 services (21 services are lazy, passive or on-demand)
web_1  | 2017-10-06 23:54:25,020 INFO  [org.wildfly.extension.undertow] (MSC service thread 1-4) WFLYUT0006: Undertow HTTP listener default listening on 0.0.0.0:8080
web_1  | 2017-10-06 23:54:26,146 INFO  [org.wildfly.swarm.runtime.deployer] (main) deploying docker-javaee.war
web_1  | 2017-10-06 23:54:26,169 INFO  [org.jboss.as.server.deployment] (MSC service thread 1-3) WFLYSRV0027: Starting deployment of "docker-javaee.war" (runtime-name: "docker-javaee.war")
web_1  | 2017-10-06 23:54:27,748 INFO  [org.jboss.as.jpa] (MSC service thread 1-2) WFLYJPA0002: Read persistence.xml for MyPU
web_1  | 2017-10-06 23:54:27,887 WARN  [org.jboss.as.dependency.private] (MSC service thread 1-7) WFLYSRV0018: Deployment "deployment.docker-javaee.war" is using a private module ("org.jboss.jts:main") which may be changed or removed in future versions without notice.

. . .

web_1  | 2017-10-06 23:54:29,128 INFO  [stdout] (ServerService Thread Pool -- 4) Hibernate: create table EMPLOYEE_SCHEMA (id integer not null, name varchar(40), primary key (id))
web_1  | 2017-10-06 23:54:29,132 INFO  [stdout] (ServerService Thread Pool -- 4) Hibernate: INSERT INTO EMPLOYEE_SCHEMA(ID, NAME) VALUES (1, 'Penny')
web_1  | 2017-10-06 23:54:29,133 INFO  [stdout] (ServerService Thread Pool -- 4) Hibernate: INSERT INTO EMPLOYEE_SCHEMA(ID, NAME) VALUES (2, 'Sheldon')
web_1  | 2017-10-06 23:54:29,133 INFO  [stdout] (ServerService Thread Pool -- 4) Hibernate: INSERT INTO EMPLOYEE_SCHEMA(ID, NAME) VALUES (3, 'Amy')
web_1  | 2017-10-06 23:54:29,133 INFO  [stdout] (ServerService Thread Pool -- 4) Hibernate: INSERT INTO EMPLOYEE_SCHEMA(ID, NAME) VALUES (4, 'Leonard')

. . .

web_1  | 2017-10-06 23:54:30,050 INFO  [org.wildfly.extension.undertow] (ServerService Thread Pool -- 4) WFLYUT0021: Registered web context: /
web_1  | 2017-10-06 23:54:30,518 INFO  [org.jboss.as.server] (main) WFLYSRV0010: Deployed "docker-javaee.war" (runtime-name : "docker-javaee.war")
web_1  | 2017-10-06 23:56:01,766 INFO  [stdout] (default task-1) Hibernate: select employee0_.id as id1_0_, employee0_.name as name2_0_ from EMPLOYEE_SCHEMA employee0_
db     | Initializing database

. . .

db     |
db     |
db     | MySQL init process done. Ready for start up.
db     |

. . .

db     | 2017-10-06T23:54:29.434423Z 0 [Note] /usr/sbin/mysqld: ready for connections. Version: '8.0.3-rc-log'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server (GPL)
db     | 2017-10-06T23:54:30.281973Z 0 [Note] InnoDB: Buffer pool(s) load completed at 171006 23:54:30
```

`depends_on`“撰写”定义文件中的“属性”可确保容器启动顺序。但是应用程序级别的启动需要通过容器内运行的应用程序来确保。在我们的情况下，这可以通过WildFly Swarm使用[https://reference.wildfly-swarm.io/fractions/datasources.html上](https://reference.wildfly-swarm.io/fractions/datasources.html)`swarm.datasources.data-sources.KEY.stale-connection-checker-class-name`定义的方法来实现。

## 验证申请

现在已经配置了WildFly Swarm和MySQL，让我们访问该应用程序。您需要指定运行WildFly的主机的IP地址（`localhost`在我们的示例中）。

在这种情况下，可以按以下方式访问端点：

```
curl -v http：// localhost：8080 / resources / employees
```

输出显示为：

```
curl -v http://localhost:8080/resources/employees
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET /resources/employees HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.51.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Connection: keep-alive
< Content-Type: application/xml
< Content-Length: 478
< Date: Sat, 07 Oct 2017 00:05:41 GMT
<
* Curl_http_done: called premature == 0
* Connection #0 to host localhost left intact
<?xml version="1.0" encoding="UTF-8" standalone="yes"?><collection><employee><id>1</id><name>Penny</name></employee><employee><id>2</id><name>Sheldon</name></employee><employee><id>3</id><name>Amy</name></employee><employee><id>4</id><name>Leonard</name></employee><employee><id>5</id><name>Bernadette</name></employee><employee><id>6</id><name>Raj</name></employee><employee><id>7</id><name>Howard</name></employee><employee><id>8</id><name>Priya</name></employee></collection>
```

这显示了查询数据库的结果。

可以获得单个资源：

```
curl -v http：// localhost：8080 / resources / employees / 1
```

它显示输出：

```
curl -v http://localhost:8080/resources/employees/1
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8080 (#0)
> GET /resources/employees/1 HTTP/1.1
> Host: localhost:8080
> User-Agent: curl/7.51.0
> Accept: */*
>
< HTTP/1.1 200 OK
< Connection: keep-alive
< Content-Type: application/xml
< Content-Length: 104
< Date: Sat, 07 Oct 2017 00:06:33 GMT
<
* Curl_http_done: called premature == 0
* Connection #0 to host localhost left intact
<?xml version="1.0" encoding="UTF-8" standalone="yes"?><employee><id>1</id><name>Penny</name></employee>
```

## 关机应用

使用`docker-compose down`以下命令关闭应用程序：

```
Stopping javaee_web_1 ... done
Stopping db           ... done
Removing javaee_web_1 ... done
Removing db           ... done
Removing network javaee_default
```

这将停止每个服务中的容器并删除所有服务。它还会删除作为此应用程序一部分创建的任何网络。