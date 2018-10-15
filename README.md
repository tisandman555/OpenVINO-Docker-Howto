# OpenVINO-Docker-Howto
This repo shows how to use OpenVINO Docker Image on your local host


使用OpenVINO Docker文件指南
====

## 导入OpenVINO Docker文件到本机环境
先决条件:
一台已经安装好ubuntu16.04的ubuntu主机

搭建步骤:
1. 按照https://docs.docker.com/install/linux/docker-ce/ubuntu/ 这里的步骤安装docker-ce

	```Bash
    $ sudo apt-get update 
    $ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - 
    $ sudo apt-key fingerprint 0EBFCD88
    $ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs)   stable" 
    $ sudo apt-get update
    $ sudo apt-get install docker-ce 
    ```
2. 加载本地docker镜像文件<br>
    ```Bash
    $ sudo docker load -i $IMG.tar
    ```
    
    或者从阿里云上加载(此路径下的镜像内包含了OpenVINO 2018r3版本)<br>
    ```Bash
    $ sudo docker pull registry.cn-qingdao.aliyuncs.com/openvino/workshop:pure-r3_model-downloader
    ```
    这时可以用`sudo docker image ls -a`来看导入的docker镜像的信息
3. 创建本地容器<br>
	```Bash
    $ sudo docker create --net=host --name openvino-2018r3 -it -e DISPLAY=$DISPLAY --device=/dev/dri/card0:/dev/dri/card0 --device=/dev/dri/renderD128:/dev/dri/renderD128 --privileged -v /dev:/dev -v /opt/intel/workshop:/opt/intel/workshop registry.cn-qingdao.aliyuncs.com/openvino/workshop:pure-r3_model-downloader
    ```
    &ensp;&ensp;参数解释<br>
    &ensp;&ensp;&ensp;&ensp;--name <font color=#dd0000>openvino-2018r3</font><br>
    &ensp;&ensp;&ensp;&ensp;openvino-2018r3是本地容器的名字,自己随便起<br><br>
    &ensp;&ensp;&ensp;&ensp;-v <font color=#dd0000>/opt/intel/workshop:/opt/intel/workshop</font><br>
    &ensp;&ensp;&ensp;&ensp;将本地host机器目录/opt/intel/workshop挂载到容器内的/opt/intel/workshop目录上<br><br>
    &ensp;&ensp;&ensp;&ensp;<font color=#dd0000>registry.cn-qingdao.aliyuncs.com/openvino/workshop:pure-r3_model-downloader</font><br><br>
    此为创建容器时基于的本地docker镜像的名字<br>
    
到这里,本机就建好OpenVINO Docker的容器,接下来就可以用了<br><br>

## 导入Intel OpenVINO workshop内容
将Intel OpenVINO workshop的内容包解压缩到本机的/opt/intel/workshop目录下<br>

## 启动OpenVINO Docker
每次使用前,首先在host系统里启动一个console, 运行 `$ xhost +local:docker` （允许docker访问host端的display硬件,这样docker内运行workshop里有GUI的应用才能有显示）<br><br>
运行`$ sudo docker container start openvino-2018r3`,启动前面创建好的容器<br><br>
运行$ `sudo docker attach openvino-2018r3`,把当前console的STDIN/STDOUT挂载到容器上<br><br>
此时你就可以在这个Docker console里编译运行你的openvino程序了<br><br>


注意<br>
1. docker内无法运行VS code之类的代码编辑器GUI, 所以你需要在host这边用VS code编辑/opt/intel/workshop下的内容, 此目录下的内容会自动在dock对应的目录下更新<br><br>
2. /opt/intel/workshop/smart-video-workshop/object-detection下的tutorial1和ROIViewer 2个应用需要在docker环境内重新编译生成,编译方法见https://github.com/intel-iot-devkit/smart-video-workshop/tree/master/object-detection <br><br>


## 退出OpenVINO Docker
在当前console里运行`$ exit`,即可从Docker环境内退回到host的console<br>

### Docker镜像的兼容性测试
阿里云上的Docker image在以下平台上做过测试,运行完好<br>
ubuntu 18.04 pass (kernel 4.15)<br>
centos 7.5.1804 pass (kernel 3.10.0)<br>
fedora 28 pass(kernel 4.18.9)<br>
ubuntu 16.04.4 pass(kernel 4.13)<br><br>
UP2 dev kit with ubuntu 16.04.4 pass(kernel 4.13) <br><br>
在UP2这类凌动处理器平台上使用OpenVINO docker镜像时,需要重新编译docker里openvino的sample, 因为Intel® Celeron™ N3350的CPU不支持AVX,docker里的openvino sample在编译时打开了支持avx的编译参数<br>
<br>
*注意:目前此版本mediasdk部分工作不正常<br>
