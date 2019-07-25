
# 1
结构分为四块
1 容器基础
2 k8s集群
3 容器编排&k8s核心
4 k8s开源与生态

Docker 镜像解决的，恰恰就是打包这个根本性的问题

?
docker build " 我的镜像 "
docker run " 我的镜像 "

单机
docker run " 我的容器
多机
docker run -H " 我的 Swarm 集群 API 地址 " " 我的容器 "


容器本身没有价值，有价值的是容器编排

# 5 
运行一个容器
docker run -it busybox /bin/sh
-it 文本输入输出环境
/bin/sh 容器里运行的程序

namespace（只看到有限的东西） 和 cgroups（限制功能）技术
容器是一种特殊的进程而已

# 7 
docfile制作镜像

# 8
# 使用官方提供的 Python 开发镜像作为基础镜像
FROM python:2.7-slim

# 将工作目录切换为 /app
WORKDIR /app

# 将当前目录下的所有内容复制到 /app 下
ADD . /app

# 使用 pip 命令安装这个应用所需要的依赖
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# 允许外界访问容器的 80 端口
EXPOSE 80

# 设置环境变量
ENV NAME World

# 设置容器进程为：python app.py，即：这个 Python 应用的启动命令
CMD ["python", "app.py"]



ls
Dockerfile  app.py   requirements.txt

docker build -t helloworld .
-t tag 

docker image ls
REPOSITORY            TAG                 IMAGE ID
helloworld         latest              653287cdf998


docker run -p 4000:80 helloworld

由于dockerfile已经有了cmd
，如果没有，等同于
docker run -p 4000:80 helloworld python app.py

-p 4000:80 告诉了 Docker，请把容器内的 8...


查看进程
docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED
4ddf4638572d        helloworld       "python app.py"     10 seconds ago


curl http://localhost:4000
<h3>Hello World!</h3><b>Hostname:</b> 4ddf4638572d<br/>


上传镜像

为了能够上传镜像，我首先需要注册一个 Docker Hub ...
docker tag helloworld geektime/helloworld:v1

docker push geektime/helloworld:v1
geektime 是我在 Docker Hub 上的用户名，...
它的“学名”叫镜像仓库（Repository）
“/”后面的 helloworld 是这个镜像的名字
而“v1”则是我给这个镜像分配的版本号


docker exec -it 4ddf4638572d /bin/sh
# 在容器内部新建了一个文件
root@4ddf4638572d:/app# touch test.txt
root@4ddf4638572d:/app# exit

# 将这个新建的文件提交到镜像中保存
$ docker commit 4ddf4638572d geektime/helloworld:v2

当前正在运行的 Docker 容器的进程号（PID）是 25...
docker inspect --format '{{ .State.Pid }}'  4ddf4638572d
25686


ls -l  /proc/25686/ns
total 0
lrwxrwxrwx 1 root root 0 Aug 13 14:05 cgroup -> cgroup:[4026531835]
lrwxrwxrwx 1 root root 0 Aug 13 14:05 ipc -> ipc:[4026532278]
lrwxrwxrwx 1 root root 0 Aug 13 14:05 mnt -> mnt:[4026532276]
lrwxrwxrwx 1 root root 0 Aug 13 14:05 net -> net:[4026532281]
lrwxrwxrwx 1 root root 0 Aug 13 14:05 pid -> pid:[4026532279]
lrwxrwxrwx 1 root root 0 Aug 13 14:05 pid_for_children -> pid:[4026532279]
lrwxrwxrwx 1 root root 0 Aug 13 14:05 user -> user:[4026531837]
lrwxrwxrwx 1 root root 0 Aug 13 14:05 uts -> uts:[4026532277]

一个进程，可以选择加入到某个进程已有的 Namespace ...


docker run -v /test ...
$ docker run -v /home:/test ...

临时目录 /var/lib/docker/volumes/[...
宿主机的 /home 目录挂载到容器的 /test


docker run -d -v /test helloworld
cf53b766fa6f

docker volume ls
DRIVER              VOLUME NAME
local               cb1c2f7221fa9b0971cc35f68aa1034824755ac44a034c0c0a1dd318838d3a6d

ls /var/lib/docker/volumes/cb1c2f7221fa/_data/

docker exec -it cf53b766fa6f /bin/sh
cd test/
touch text.txt
回到宿主机
ls /var/lib/docker/volumes/cb1c2f7221fa/_data/
text.txt
