#### 启动容器
```shell
#以交互方式运行WildFly容器.-i允许与STDIN交互-t并将TTY附加到流程。开关可以组合在一起用作-it.
docker container run -it <IMAGE_NAME> 
#以分离模式重新启动容器.-d后台运行容器，并返回容器ID.
docker container run -d <IMAGE_NAME>
#生成的容器ID可用于查看容器日志
docker container logs <CONTAINER_ID>
#使用端口
docker container run -d -p <hostPort>:<containerPort> --name <CONTAINER_ID> <IMAGE_NAME>
```
#### 查看镜像 容器
```shell
docker image ls
docker container ls
```

#### 停止容器

通过ID或名称停止特定的容器：

```
docker container stop <CONTAINER ID>
docker container stop <NAME>
```

停止所有正在运行的容器：

```
docker container stop $(docker container ps -q)
```

仅停止退出的容器：

```
docker container ps -a -f "exited=-1"
```

#### 取出容器

通过ID或名称删除特定的容器：

```shell
#-f 会杀死容器。
docker container rm <CONTAINER_ID>
docker container rm <NAME>
```

删除符合正则表达式的容器

```shell
docker container ps -a | grep wildfly | awk '{print $1}' | xargs docker container rm
```

移除所有容器，没有任何条件

```shell
docker container rm $(docker container ps -aq)
```

#### 查找端口映射的其他方法

```
docker container port <CONTAINER_ID> or <NAME>
```

输出显示为：

```
8080/tcp -> 0.0.0.0:8080
```

端口映射也可以使用以下`docker inspect`命令找到：

```shell
docker container inspect --format='{{(index (index .NetworkSettings.Ports "8080/tcp") 0).HostPort}}' <CONTAINER ID>
```