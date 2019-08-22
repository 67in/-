# Docker
## docker是什么
### docker出现原因
解决运维和开发之间的协作问题（操作系统、运行环境、应用配置、各种版本的迭代、不同版本环境的兼容）
### docker理念
build, ship and run any app, anywhere   构建、安装、运行 任何应用在任何地点，一次封装，到处运行
### docker
解决了运行环境和配置问题软件容器，方便做持续集成并有助于整体发布的容器虚拟化技术。
## docker能干嘛
开发自运维devOps
容器虚拟化技术，是linux的精简版，只有内核，硬件等不重要的和宿主机共用
docker的镜像小，便于存储和传输，部署速度快，与宿主机共享OS
![docker1](https://raw.githubusercontent.com/67in/photo_md/master/docker1.PNG)
容器/镜像/仓库
## 阿里云/网易镜像加速器配置
ps -ef|grep docker这个命令搜出来可以看出那个设置好的阿里云镜像
## docker怎么工作
Client-Server结构系统，Docker守护进程运行在主机上，然后通过Socket连接从客户端访问，守护进程从客户端接受命令并管理运行在主机上的容器（集装箱）。

![docker2怎样工作](https://raw.githubusercontent.com/67in/photo_md/master/docker2.PNG)

## Docker常用命令
![docker常用命令](https://raw.githubusercontent.com/67in/photo_md/master/docker%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4.png)
### 帮助命令
- docker version
- docker info
- docker --help 
### 镜像命令（镜像是分层的，就像千层饼）
- docker images(本地镜像) -a(列出所有镜像，包括中间镜像层)/-qa(只显示镜像ID)/--digests/--no-trunc
- docker search 某镜像名字
docker search -s 30 tomcat    是罗列点赞数超过30的tomcat镜像
docker search -s 30 --no-trunc tomcat tomcat    镜像说明更完整
- docker pull 某镜像名字[:TAG]      下载镜像
- docker rmi 某镜像名字  删除
docker rmi -f 某镜像 强制删除
docker rmi -f hello-world nginx 删除多个镜像
docker rmi -f $(docker images -qa)删除全部镜像
### 容器命令
- 新建并启动容器    docker run [OPTIONS]IMAGE[COMMAND][ARG...]
    docker run -it 镜像id
    -i: 以交互模式运行容器，通常与-t同时使用
    -t: 为容器重新分配一个伪输入终端，通常与-i同时使用
    --name=“容器新名字”：为容器指定一个名称
    -d: 后台运行容器，并返回容器ID，即 启动守护式容器
- 列出所有运行的容器    docker ps（Linux中：ps -ef 查看所有进程）
    docker ps -a: 列出当前所有正在运行的容器+历史上运行过的
    docker ps -l: 显示最近创建的容器
    docker ps -n: 显示最近n个创建的容器
    docker ps -q: 静默模式，只显示容器编号
    docker ps --no-trunc: 不截断输出
- 退出容器
    exit 容器停止退出
    ctrl+p+q 容器不停止退出
- 启动容器
    docker start 容器ID或容器名
- 重启容器
    docker restart 容器ID或容器名
- 停止容器
    docker stop 容器ID或容器名
- 强制停止容器
    docker kill 容器ID或容器名
- 删除已停止的容器
    docker rm 容器ID
    一次性删除多个容器
    - docker rm -f $(docker ps -a -q)
    - docker ps -a -q|xargs docker rm
- **重要**
    - 启动守护式容器 
        docker run -d 容器名
        docker ps 查看运行容器，发现没有运行
        docker ps -a 进行查看，会发现容器已经退出
        **Docker容器后台运行，就必须有一个前台进程**，容器运行的命令如果不是那些一直挂起的命令(如top,tail)，就是会自动退出的。
        **交互式容器**
        例：
        docker images
        docker run -it centos **/bin/bash**
    - 查看容器日志
        docker logs -f -t --tail 容器ID
        -t: 加入时间戳，-f:跟随最新的日志打印，--tail数字： 显示最后多少条
        例：
        docker run -d centos /bin/sh -c "while true; do echo hello zzyy; sleep 2; done"
        docker ps
        docker logs 容器id
        docker logs -t 容器id
        docker logs -t -f 容器id
        docker logs -t -f --tail 3 容器id
    - 查看容器内运行的进程
        docker top 容器id
    - 查看容器内部细节
        docker inspect 容器id
    - 进入正在运行的容器并以命令行交互
        docker exec -it 容器id bashShell
        重新进入docker attach 容器id
        - 区别：
            attach: 直接进入容器启动命令的终端，不会启动新的进程
            exec: 时在容器中打开新的终端，并且可以启动新的进程
        例：
        docker ps
        docker attach 容器Id
        ls -l
        ls -l /tmp

        docker ps
        docker exec -t 容器id ls -l /tmp 
        docker exec -t 容器id /bin/bash
    - 从容器内拷贝文件到主机上
        docker cp 容器id:容器内路径 目的主机路径
        docker ps
        docker attach id
        pwd
        docker cp id:/tmp/yum.log /root
## Docker 镜像（千层饼，花卷）
### 镜像是什么
    轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，包含运行某个软件所需的所有内容，包括代码、环境变量、配置文件等

    UnionFS(联合文件系统)：一种分层、轻量级并且高性能的文件系统，支持对文件系统的修改作为一次提交来一层层的叠加，同时可将不同目录挂载到同一个虚拟文件系统下
    Union文件系统是Docker镜像的基础，镜像可通过分层来进行继承，基于基础镜像，可制作各种具体的应用镜像

    tomcat镜像为什么400多M，这么大？ 是一层层叠加起来，内核，Centos，idk8,tomcat叠加起来的

    Docker镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部。这一层通常被称为“容器层”，“容器层”之下的都叫“镜像层”
### 镜像commit操作补充
    docker commit 提交容器副本使之成为一个新的镜像
    docker commit -m="提交的描述信息" -a="作者" 容器ID要创建的目标镜像名：[标签名]

### 操作实例
- 从Hub上下载tomcat镜像到本地并成功运行
     docker run -it -p 8080：8080 tomcat 
      -p: 主机端口:docker容器端口
      -P：随机分配端口
      -i: 交互
      -t: 终端
- 故意删除上一步镜像产生tomcat容器的文档
- 当前的tomcat运行实例是一个没有文档内容的容器，以它为模板commit一个没有doc的tomcat新镜像atguigu/tomcat01
- 启动新镜像和原来的对比   
## Docker容器数据卷 
- 有点类似Redis里面的rdb和aof文件
- 作用
        - 容器的持久化
        - 容器间继承+共享数据
- 数据卷 容器内添加
    - 直接命令添加
        - 命令:docker run -it -v /宿主机绝对路径目录：/容器内目录 镜像名
        - 查看数据卷是否挂载成功
                docker ps
                docker inspect id
                "Volumes":{"":""}
        - 容器和宿主机之间数据共享
                touch 
                cd /dataVolumeContainer
        - 容器停止退出后，主机修改后数据是否同步
                exit
                是同步的
        - 命令(带权限)
                docker run -it -v /宿主机绝对路径目录：/容器内目录:ro 镜像名
                ro:只读，只允许主机这边去读写，可同步
    - DockerFile添加
            DockerFile是镜像的描述文件
            - 根目录下新建mydocker文件夹并进入
            - 可在Dockerfile中使用VOLUME指令来给镜像添加一个或多个数据卷
            - File构建
            ```
            FROM centos
            VOLUME ["/dataVolumeContainer1","/dataVolumeContainer2"]
            CMD echo "finished,--------success1"
            CMD /bin/bash
            ```
            在容器中挂了两个数据卷，宿主机docker中有默认
            用**docker inspect id**来查看容器细节，数据卷是否挂载成功
        - build后生成镜像
                docker build -f /mydocker/Dockerfile -t zzyy/centos
                docker images zzyy/centos
        - run容器
                docker run -it zzyy/centos /bin/bash
    - 备注
    如果docker挂载主机目录Docker访问出现cannot open directory.:Permission denied
    解决办法：在挂载目录后多加一个--privileged = true 参数即可
- 数据卷容器
    docker run -it --name dc01 zzyy/centos
    //启动一个父容器dc01
    touch dc01_add.txt
    docker run -it --name dc02 --volumes-from dc01 zzyy/centos
    docker run -it --name dc03 --volumes-from dc01 zzyy/centos
    //容器dc02、dc03都继承自dc01
    //回到dc01可以看到02/03各自添加的都能共享了
    //删除dc01,dc02修改后dc03可否访问，可以
    //删除dc02后dc03可否访问
    //新建dc04继承dc03后再删除dc03
    docker run -it --name dc04 --volumes-from dc03 zzyy/centos
    结论：容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止
### DockerFile解析
  1. 手动编写一个dockerfile文件，当然，必须要符合file的规范
  2. 有这个文件后，直接docker build命令执行，获得一个自定义的镜像
  3. run
#### Docker是什么
  - Docker:用来构建Docker镜像的构建文件，由一系列命令和参数构成的脚本
  - 构建三步骤：编写Dockerfile文件----> docker build -----> docker run
#### Docker内容基础知识
  - 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
  - 指令按照从上到下，顺序执行
  - #表示注释
  - 每条指令都会创建一个新的镜像层，并对镜像进行提交
##### DockerFile、docker镜像、docker容器
  分别代表软件的三个不同阶段
  - Dockerfile是软件的原材料
  - Docker镜像是软件的交付品
  - Docker容器则可认为是软件的运行态
  Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维
  ![dockerfile,镜像，容器](https://raw.githubusercontent.com/67in/photo_md/master/docker3.PNG)
#### DockerFile体系结构（保留字指令）
    - FROM  基础镜像
    - MAINTAINER  镜像维护者的姓名和邮箱
    - RUN  容器构建时需要运行的命令
    - EXPOSE  当前容器对外暴露出的端口
    - WORKDIR  指定在创建容器后，终端默认登陆的进来工作目录，一个落脚点
    - ENV  用来在构建镜像过程中设置环境变量
    - ADD  将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar压缩包
    - COPY  类似ADD，拷贝文件和目录到镜像中
            将从构建上下文目录中<源路径>的文件/目录复制到新的一层的镜像内的<目标路径>位置
    - VOLUME  容器数据卷，用于数据保存和持久化工作
    - CMD  指定一个容器启动时要运行的命令
           Dockerfile中可以有多个CMD指令，但只有最后一个生效，CMD会被docker run 之后的参数替换
    - ENTRYPOINT  指定一个容器启动时要运行的命令，和CMD一样，都是在指定容器启动程序及参数
    - ONBUILD  当构建一个被继承的Dockerfile时运行命令，父镜像在被子继承后父镜像的onbuild被触发
### 自定义镜像mycentos
 原版centos没有vim/ifconfig命令
    - 编写
        FROM centos
        MAINTAINER zzyy<zzyy167@126.com>

        ENV MYPATH /usr/local
        WORKDIR $MYPATH

        RUN yum -y install vim
        RUN yum -y install net-tools

        EXPOSE 80

        CMD echo $MYPATH
        CMD echo "success---------ok"
        CMD /bin/bash

    - 构建
        docker build -t 新镜像名字：TAG.   注意最后有个. 代表当前路径
        docker build -f /mydocker/Dockerfile2 -t mycentos:1.3.
        docker run -it mycentos:1.3

    - 历史
        docker images mycentos
        docker history id
### CMD和ENTRYPOINT镜像案例  
    docker ps
    docker images tomcat
    docker run -it -p 7777:8080 tomcat成功启动tomcat
    若执行docker run -it -p 7777:8080 tomcat ls -l, 相当于dockerfile中的最后一行CMD没有用，执行这里的ls -l,被这个给覆盖了
    而ENTRYPOINT这个命令不会被覆盖