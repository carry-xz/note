理解：
     一个镜像可以生成多个容器。可以形象的理解镜像是一个类，容器是镜像的实例对象。
实例对象只有在运行状态才存在，所以容器需是镜像运行的状态，如果不保存容器，当容器停止后所有对镜像的修改将会丢失。
可以将容器保存为镜像，可以通过docker stop停止容器（状态还在），来维持容器状态。
删除镜像前（sudo docker rmi 镜像id），要先把依赖于这个镜像的容器删除（sudo docker rm 容器id）
     nvidia内部的东西向下兼容性还是可以的，cuda driver驱动版本高于cuda runtime与cudnn版本时就能正常调用显卡，但是具体的程序如tensorflow与cuda,cudnn之间有严格的对应关系,必须对应才能正常调用。所以把cuda driver更新并不会影响docker
     使用nvidia docker只需要安装显卡cuda驱动程序，docker内部不需要做其他事情，只需要注意目标软件与cuda runtime、cudnn之间的对应关系即可。

常用参数解释：
-t 表示让Docker分配一个伪终端并绑定到容器的标准输入上
-i 表示让容器的标准输入保持打开
-d 表示以守护方式打开（即非交互模式，后台运行）
-p 设置端口映射 -p 宿主机端口:容器端口 
将宿主机的3222端口映射到容器的22端口、将宿主机的3280端口映射到容器的22端口，用法如下：
sudo docker run -t -i -p 3222:22 -p 3280:80 87e5b6b3ccc1 /bin/bash
-v 设置文件挂载
-v 宿主机目录：容器目录
--name 设置容器名称，不指定将随机生成一个
--name newContinerName imageName[/bin/bash]

版本查看
cat /usr/local/cuda/version.txt
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2

常用命令
1.查看已启动的容器
sudo docker ps

2.查看所有容器
sudo docker ps -a　　

3.删除容器
容器必须是停止状态的才可以删除，否则-f
sudo docker rm 8d98fd43acd4

4.查看镜像
docker system df
docker images

5.删除镜像
docker irm imagename 删除镜像时必须先删除建立在其之上的容器

6.启动容器
docker exec -it containerId /bin/bash 用exec时退出后docker继续运行，一般用这个。退出用exit命令
docker attach 退出时可能会关闭docker

7.停止容器
docker stop containerId 此时容器任然保存了运行时的状态。
docker start containerId 可以继续运行容器，状态任然在。

8.导出容器
导出时需要停止容器
sudo docker export containerId > containerId.tar
sudo docker import 仓库：Tag

保存镜像
sudo docker save -o newTarName.tar imageName
sudo docker load -i docker.tar

9.通过容器创建新镜像
docker commit containerId newImageName

10.镜像改名
docker tag IMAGEID(镜像id) REPOSITORY:TAG（仓库：标签）

pycharm调试docker内python程序
做几件事即可
1.创建容器时做好宿主机与docker的端口映射关系
2.宿主机与docker都安装好ssh
3.在docker中设置好ssh，修改docker密码，允许root，暴露端口，启动ssh

最重要步骤：
1.宿主机Ubuntu与docker安装ssh
apt update && apt install openssh-server

2.以下配置在docker container中完成
echo 'root:123456' | chpasswd
将Root的密码修改为123456或其他
sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
允许使用root身份登录
sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
端口操作
echo "export VISIBLE=now" >> /etc/profile
启动ssh服务，注意关注ssh服务状态，很多时候都是这个问题
service ssh restart

3.其他电脑通过ssh连接docker
外部连接时ssh user@ip -p portNumber
其中user是docker中的user，一般是root。ip是宿主机ip，端口号portNumber是docker映射到主机上的宿主机端口。
之后根据提示输入自己设置docker密码即可。

cudnn安装：
下载与cuda版本对应的cudnn
     https://developer.nvidia.com/rdp/cudnn-archive
    sudo tar -xzvf cudnn-8.0-linux-x64-v5.1.tgz
    sudo cp cuda/include/cudnn.h /usr/local/cuda/include
    sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
    sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*
    vim ~/.bashrc 
翻到最底部加上： 
    export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64:/usr/local/cuda/extras/CUPTI/lib64" 
    export CUDA_HOME=/usr/local/cuda
    source ~/.bashrc

版本对应关系tensorflow与cudnn
https://www.tensorflow.org/install/install_sources#common_installation_problems

docker加速
注册国内镜像网站，按提示加入到docker中

nvidia-docker使用Dockerfile构建镜像
nvidia-docker build -t newimageName /path_to_Dockerfile

例子：
sudo nvidia-docker run -itd --name cuda9_cudnn7_xz_t --shm-size=1g --ulimit memlock=-1 --ulimit stack=67108864 -p 8026:22 -v /home/asus/user/xz/dockerShare:/data cuda9_cuddn7_xz /bin/bash
docker exec -it cuda_9_cudnn7_xz /bin/bash

重装docker时可能需要删除system下docker文件夹中的json文件。

