---
layout: post
title: "从0开始搭建RTAB-Map（手把手从入门到入土）"
subtitle: "Yeah it's on."
author: 曦秋
catalog: true
header-img: "/img/stu-note-bg.jpg"
header-mask: 0.5
permalink: /post/rtabmap.html
date: 2020-06-06 06:06:06
tags: 
    - SLAM
    - 学习笔记
---

&nbsp;&nbsp;&nbsp;&nbsp;心累……毕设选题又跑到了SLAM方向，虽然就结果而言还不错，但是碰到疫情真的只能在家做实验，不仅没器材还没心情，幸亏导师人好把需要的传感器都寄来了(๑•̀ㅂ•́)و✧ 然而导师最后给了我个B直接把我心态搞崩……WTF！我在组里干得最久，底层的代码我先试了个水排坑，结果就给了我B？？！得亏做的东西其他评委老师懂得有多困难，再加上仪表大方，穿上整齐的西装，总算把最终成绩搞到A了恍恍惚惚(￣_,￣ )
 
&nbsp;&nbsp;&nbsp;&nbsp;说回正事，虽然我不算是SLAM大佬（毕竟连其他SLAM的demo都没怎么碰过QwQ），但是RTABMap给我的感觉更好一些，无论是跑出来的效果还是安装时的要求，和ORB-SLAM2相比还是更好一些，最重要的是可以直接得到二维栅格地图和带像素信息的三维点云图，就不会写底层代码的我来说，这一点就很舒服~接下来主要讲我是怎么做的以及碰到的一些坑。
 
硬件：Thinkpad W530 8G内存+256GB SSD；REALSENSE D435i；RPLIDAR S1

<hr>
 
### 操作系统及配置

先装系统，采用Ubuntu 16.04.5，分区为： 
 
/ &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;100G主分区  
 
/boot &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2G主分区 
 
/swap &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8G逻辑分区 
 
/home &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;剩下空间
 
挂载点在硬盘上（尝试过挂载点在/boot上但是无用）。swap和自己内存保持一致，如果内存小于2G，则调整为内存大小的2-3倍。
其实也能用18.04的，界面也更舒服一些，而且RTABMap也支持，但是我还是用16.04的原因在于：
 
1、18.04的很多函数名称改了，这个就属于牵一发而动全身的事，很繁琐而且不一定能改完整，不适合我这种粗心的人；
 
2、18.04在跑pyrealsense的时候有bug，而且没找到问题在哪里……当时还指望能用pyrealsense调一下参，没想到自己被调了……
 
所以在条件允许的情况下，还是建议18.04；但是16.04更全面一些，适用度也更广。
 
装好系统的第一件事先换源，采用清华源，再更新数据（`sudo apt-get update`）：

``` shell
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted universe multiverse
```
 
其次安装Chrome+V2Ray，同步其余移动端数据的同时保证git-clone能顺利，也可一直使用Firefox。采用QV2Ray：<a href="https://mahongfei.com/1776.html" target="_blank"><u>Linux配置v2ray详细教程-Ubuntu为例</u></a>。其实最开始并不想装VPN，但是最后装摄像头固件的时候，找不到可以替代亚马逊云的链接了！没办法只好用VPN下载下来了，啊当然SSR也可以，取决于你用的是什么了，这里不展开说。装Chrome也只是因为很多资料在账号云端上，直接用是最方便的hhhhhh

<hr>

### 安装次级操作系统

之后安装ROS Kinetic （Kinetic对应Ubuntu 16.04，Melodic对应Ubuntu 18.04）<a href="#cankao1">①</a> 。

``` shell
~$ sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
~$ sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116
~$ sudo apt-get update
~$ sudo apt-get install ros-kinetic-desktop-full
```

一般而言到这里就基本没有问题了；如果有其他需要装的包，把最后一个命令改成`sudo apt-get install ros-kinetic-PACKAGE`，其中`PACKAGE`表示你所需要的包。随后需要ROS的初始化：

``` shell
~$ sudo rosdep init
~$ rosdep update
```

<b>存在问题：`sudo rosdep init`可能无法连接，`rosdep`可能直接报错：</b>

```
ERROR: cannot download default sources list from:
https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/sources.list.d/20-default.list
Website may be down.
```
 
有两种方法：(1) 用手机热点连接电脑进行初始化（玄学操作）； (2)根据报错目录（`/etc/ros/rosdep/sources.list.d/20-default.list`)创建`20-default.list`，在上述链接中直接复制内容，然后直接编译初始化更新（可能会使用sudo）。`20-default.list`内容如下：

``` shell
# os-specific listings first
yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/osx-homebrew.yaml osx

# generic
yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/base.yaml
yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/python.yaml
yaml https://raw.githubusercontent.com/ros/rosdistro/master/rosdep/ruby.yaml
gbpdistro https://raw.githubusercontent.com/ros/rosdistro/master/releases/fuerte.yaml fuerte

# newer distributions (Groovy, Hydro, ...) must not be listed anymore, they are being fetched from the rosdistro index.yaml instead
```

如果还不行，参考<a href="#cankao2">②</a>，其主要思路是：将需要下载的包先下载到本地，将url地址全部改为本地的下载的包的地址。
 
之后需要配置环境变量并安装pyrosinstall：

``` shell
~$ echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
~$ source ~/.bashrc 
~$ sudo apt-get install python-rosinstall
```

至此ROS安装完成，可以运行`roscore`尝试（当前终端处于拒绝接受指令状态，且有`rosout`字样说明成功了）。安装ROS成功后，在Beginner Tutorials中有一个简单的示例程序，可以验证ROS是否可以正常运行：

``` shell
~$ roscore
~$ rosrun turtlesim turtlesim_node
~$ rosrun turtlesim turtle_teleop_key
此时选中乌龟的控制窗口，可以使用键盘控制乌龟移动
~$ rosrun rqt_graph rqt_graph #可以看到ROS的图形化界面，展示节点的关系
```

接下来需要建立工作区<a href="#cankao3">③</a>，以免因为找不到数据与代码：

``` shell
~$ mkdir -p ~/catkin_ws/src
~$ cd ~/catkin_ws/src
~$ catkin_init_workspace
~$ source ~/catkin_ws/devel/setup.bash

``` 
并再配置工作区的环境变量：

``` shell
~$ sudo gedit ~/.bashrc
在文件的最后添加以下代码：
#set ros kinetic
source /opt/ros/kinetic/setup.bash
source ~/catkin_ws/devel/setup.bash
#set ros network
export HOME_DIR=~/catkin_ws
export ROS_PACKAGE_PATH=~/catkin_ws:~/catkin_ws/src:/opt/ros/kinetic/share
#set Ros alias command
alias cw=’cd ~/catkin_ws’
alias cs=’cd ~/catkin_ws/src’
alias cm=’cd ~/catkin_ws&&catkin_make’

最后再更新配置即可：
~$ source ~/.bashrc 
```
 
RTABMap里面自带OpenCV的二进制库，因此如果没有其他需求的话可以不装，安装可以参考这个：<a href="https://www.cnblogs.com/eczhou/p/7860586.html" target="_blank"><u>Ubuntu16.04下安装OpenCV2.4.13</u></a>。版本在2.4.11到2.11之间是没问题的，其他的我还没有试验。
 
<hr>

### 安装硬件必要固件

#### 安装rtabmap及其依赖:<a href="#cankao4">④</a><a href="#cankao5">⑤</a> 
 
在配置好工作空间后需要先安装相关依赖（Qt, PCL, VTK, OpenCV,……），最简单的方法是安装二进制dev版本再删除：

``` shell
~$ sudo apt-get install ros-kinetic-rtabmap ros-kinetic-rtabmap-ros
~$ sudo apt-get remove ros-kinetic-rtabmap ros-kinetic-rtabmap-ros
```

接下来需要安装几个必要的库：g2o、gtsam、cvsba，选装freeConnect。
    
* 安装g2o：

``` shell
~$ git clone https://github.com/RainerKuemmerle/g2o.git
~$ cd g2o
~$ mkdir build
~$ cd build
~$ cmake ..
~$ make
~$ sudo make install
```

* 安装gtsam：

``` shell
~$ git clone https://bitbucket.org/gtborg/gtsam.git
~$ cd gtsam
~$ mkdir build
~$ cd build
~$ cmake ..
~$ make check
~$ make install
```

* 安装cvsba：需要先下载<a href="https://sourceforge.net/projects/cvsba/files/" target="_blank">cvsba的压缩包</a>

``` shell
~$ sudo apt-get install liblapack-dev libf2c2-dev 
~$ tar -zxvf cvsba-1.0.0.tgz 
~$ mkdir cvsba-1.0.0.tgz/build
~$ cd cvsba-1.0.0.tgz/build
~$ cmake ..
~$ make
~$ make install
~$ mkdir /usr/local/lib/cmake/cvsba   #为了让RTABMap能找到cvsba
~$ mv /usr/local/lib/cmake/Findcvsba.cmake /usr/local/lib/cmake/cvsba/cvsbaConfig.cmake
```
 
用源码安装RTABMap：

``` shell
~$ cd ~
~$ git clone https://github.com/introlab/rtabmap.git rtabmap
~$ cd rtabmap/build
~$ cmake -DCMAKE_INSTALL_PREFIX=~/catkin_ws/devel ..
~$ make -j4
~$ make install
```

接下来需要安装rtabmap-ros包：

``` shell
~$ cd ~/catkin_ws
~$ git clone https://github.com/introlab/rtabmap_ros.git src/rtabmap_ros
~$ catkin_make   #如果内存较小的话，可以使用catkin_make -j1
```

#### 之后安装realsense D435i驱动<a href="#cankao6">⑥</a>：

``` shell
~$ sudo apt-key adv --keyserver keys.gnupg.net --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE || sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE    #注册公钥
~$ sudo add-apt-repository "deb http://realsense-hw-public.s3.amazonaws.com/Debian/apt-repo xenial main" -u #添加包仓库列表
~$ sudo apt-get install librealsense2-dkms librealsense2-utils librealsense2-dev librealsense2-dbg  #安装库以及开发包
~$ realsense-viewer
```

<b>注意：包仓库的amazonaws被墙，国内最好翻墙下载</b>
     
下载过程中如果有报错，先设置代理etc（`export 'HTTP_PROXY=http://localip:port'`)，再在`sudo`命令后增加`-E`，使得root权限可以运行代理。
 
某些电脑在安装过程中会出现configure UEFI Secure Boot的窗口，意思是开启了UEFI Secure Boot，而这会阻止你使用第三方的硬件驱动，不必更改这一模式，只需要按照窗口提示设定一个Secure Boot的密码确认，然后重启就会自动进入到MOK界面，选择第二项，yes之后输入刚才设定的密码，即可重新进入系统，此时realsense的驱动也就安装好了。
 
> D435i包含RGB图像、深度图像和IMU的信息，如果插上没反应，或者只能出现Stereo深度图这一个信息，一般是因为<u>USB端口不是3.0造成的，或者是端口识别错了</u>，换口或重新插拔试试。
 
#### 下载并安装realsense2-camera：

``` shell
~$ cs   # cd ~/catkin_ws/src
~$ git clone https://github.com/intel-ros/realsense.git
~$ cm   # cd ~/catkin_ws&&catkin_make
```

#### 安装realsense端的ros依赖
按照官网要求即可<a href="#cankao7">⑦</a>：(官网其实写的很清楚，建议先看完再安装）

``` shell
~$ cs
~$ git clone https://github.com/IntelRealSense/realsense-ros.git
~$ cd realsense-ros/
~$ git checkout `git tag | sort -V | grep -P "^\d+\.\d+\.\d+" | tail -1`
~$ cw   # cd ~/catkin_ws
~$ catkin_make -DCATKIN_ENABLE_TESTING=False -DCMAKE_BUILD_TYPE=Release
~$ catkin_make install
~$ echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
~$ source ~/.bashrc
```

#### 安装librealsense<a href="#cankao8">⑧</a>：

``` shell
~$ git clone https://github.com/IntelRealSense/librealsense
~$ sudo apt-get update && sudo apt-get upgrade && sudo apt-get dist-upgrade
~$ sudo apt-get install libusb-1.0-0-dev pkg-config
~$ sudo apt-get install libudev-dev pkg-config libgtk-3-dev
~$ sudo apt-get install libglfw3-dev
```

使用gcc -v确认当前gcc的版本，如果之前的步骤都正确完成的话，可以看到gcc 5.0.0或以上（对于ubuntu16 LTS最好也确认下gcc版本是否在4.9以上）。<br>
进入librealsense的目录路径下，执行以下指令：
``` shell
~$ mkdir build && cd build
~$ cmake ../ -DBUILD_EXAMPLES=true
~$ sudo make uninstall && make clean && make && sudo make install 
```

安装Video4Linux（在执行接下去的指令之前，确保realsense的摄像头没有连接到对应的电脑）

``` shell
~$ sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/
~$ sudo udevadm control --reload-rules && udevadm trigger
~$ sudo apt-get install libssl-dev
~$ ./scripts/patch-realsense-ubuntu-xenial-joule.sh 
```

在询问（等待键盘输入）的时候，选择y（会询问非常多次），只有中间一部分只有m/n选项的直接安enter键。(大约持续5min)<br>
之后运行`sudo dmesg | tail -n 50`即可完成安装。<br>
可以插上摄像头进行测试，在librealsense下运行：

``` shell
~$ cd build/examples/capture
~$ ./rs-capture
```

可以显示BGR图像以及深度图，就已经说明安装成功。<br>
单摄像头即可跑通RTAB-Map，要求一定按照官网<a href="#cankao9">⑨</a>。 ####

先写这么多吧，慢慢完善总结。还有很多工作量需要去总结和完善。这是第一篇文章，也是我第一次做项目类的总结，也希望以后我能做的越来越好吧QwQ

<hr>

### 参考链接：

<a name="cankao1">①</a> <a href="https://cnblogs.com/longronglang/p/11386522.html" target="_blank">Ubuntu16.04安装ROS(Kinetic版本)</a>

<a name="cankao2">②</a> <a href="https://blog.csdn.net/nanianwochengshui/article/details/105702188" target="_blank">sudo rosdep init 出现 ERROR: cannot download default sources list from:</a>                    

<a name="cankao3">③</a> <a href="https://blog.csdn.net/hhg337372083/article/details/82689912" target="_blank">ubuntu16.04 Ros-kinetic 安装流程</a>
                                  
<a name="cankao4">④</a> <a href="https://www.ncnynl.com/archives/201709/1991.html" target="_blank">ROS与VSLAM入门教程-rtabmap_ros-安装</a>
                    
<a name="cankao5">⑤</a> <a href="https://blog.csdn.net/Iriving_shu/article/details/69372380" target="_blank">rtabmap安装</a>
                    
<a name="cankao6">⑥</a> <a href="https://blog.csdn.net/sinat_36502563/article/details/89174282" target="_blank">Ubuntu 驱动Intel RealSense D435i 深度相机及ROS应用</a>
                    
<a name="cankao7">⑦</a> <a href="https://github.com/IntelRealSense/realsense-ros " target="_blank">ROS Wrapper for Intel® RealSense™ Devices</a>
                    
<a name="cankao8">⑧</a> <a href="https://blog.csdn.net/kou_ching/article/details/85271550" target="_blank">ubuntu16.04 安装 librealsense</a>
                    
<a name="cankao9">⑨</a> <a href="http://wiki.ros.org/rtabmap_ros/Tutorials/HandHeldMapping" target="_blank">RGB-D Handheld Mapping </a>
