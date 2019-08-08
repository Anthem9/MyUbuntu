# [How]定制Ubuntu操作系统

- 实体机 Dell XPS 15 9570
- Ubuntu 18.04

## BIOS设置

1. **System Configuration -> SATA Operation** Change it from "Raid on" to "AHCI"
2. **Secure Boot -> Secure Boot Enable** Un-check the box for "Secure Boot Enable"
3. **General -> Advanced Boot Options** Check "Enable Ateempt Legacy Boot" and "Enable Legacy Options ROMs"
4. Save Change

## 制作启动U盘

1. 推荐UltralSO（软碟通）
我尝试了Rufus、Etcher都不行，而且安装时触摸板驱动都没有，软碟通制作的启动U盘貌似自带触摸板驱动，非常方便。
2. 文件 -> 打开 选择你要刻录的`.iso`文件
3. 这个时候导航栏会变一下，所以你一开始找不到“启动”
4. 启动 -> 写入硬盘映像 使用默认参数即可

## 安装操作系统

正常安装即可，选择最小安装，更新和第三方软件都不选。
到最后点击重启以后会卡住，长按电源键强制重启即可。
如果你BIOS未正确设置，或者安装时出错，或者制作的启动盘有问题，重启后可能进入黑乎乎的grub命令行，输入exit退出，重新检查前面的条件，重装。
如果操作都正确，现在就能进入系统了

## 系统基本设置

### 第一步就是科学上网

SS
[Github地址](https://github.com/shadowsocks/shadowsocks-qt5)

[我仓库里的地址](https://github.com/Anthem9/MyUbuntu/raw/master/shadowsocks-qt5/Shadowsocks-Qt5-3.0.1-x86_64.AppImage)

配置好后设置终端代理
```bash
export all_proxy=socks5://127.0.0.1:1080
```
> 这里有个坑，不要在系统设置里设置socks代理，因为他会设置成`ALL_PROXY=socks://127.0.0.1:1080`
> 大小写无所谓，但是系统的设置是没有socks版本的，很多程序会因此连接失败
> 以后如果有网络任务卡住，也请检查这个环境变量是否设置正确
> 最后可以把这条命令添加到终端的启动脚本里

```bash
export all_proxy=socks5://127.0.0.1:1080
alias set_ss="export all_proxy=socks5://127.0.0.1:1080"
alias unset_ss="unset all_proxy"
```

### 安装常用工具

```bash
sudo apt update
sudo apt install curl git  wget -y
```

先安装这些的目的是因为，后续可能要用他们安装软件或者脚本


### 适应XPS 15 9570的脚本

[Github](https://github.com/JackHack96/dell-xps-9570-ubuntu-respin)

```bash
sudo bash -c "$(curl -fsSL https://raw.githubusercontent.com/JackHack96/dell-xps-9570-ubuntu-respin/master/xps-tweaks.sh)"
```
需要先翻墙，不然会卡住

> 如果不安装这个脚本，你的显卡会一直呼呼地响，这根安装时重启卡住的问题一样，都是Linux跟独显不兼容问题的体现，安装完这个脚本问题就解决了，还有针对系统其他的一些优化，感谢前辈们的工作
> 这个Github项目还提供了另一种方案，就是把驱动程序直接添加到`.iso`镜像文件中，我尝试了这个方法，但是没有成功，没有制作出正确的镜像文件，可能是我制作镜像文件时的网络出了问题（墙）。

输入命令：
Intel:
```bash
sudo prime-select intel
```
Nvidia:
```bash
sudo prime-select nvidia
```
重启就可以切换显卡驱动

## 写作Workflow建设

### 基础设施

#### 中文输入法

不喜欢ibus的中文输入法

搜狗拼音输入法
1. 官网Deb包下载，双击安装
2.  打开设置 -> 区域和语言 -> 管理已安装的语言 这里会提示语言包不完整，安装即可
 3. 键盘输入法系统 从ibus切换到Fcitx
 4. 重启
 5. Ctrl + Space 切换输入法

> 现在的Ubuntu直接双击`.deb`文件就可以使用Gnome桌面系统自带的软件仓库进行安装，不必安装`gdebi`等软件解决依赖问题了，而且如果检测到软件的PPA源，还会自动为你添加源，方便以后的更新，非常方便。

#### 网页浏览器

- Chrome 官网下载`deb`包，双击安装
- 设置代理后登录Google，同步书签，插件等

- Firefox 系统自带

- Tor 未安装

#### 同步网盘

推荐Nextcloud同步网盘，可以自己搭建，也可使用服务商免费的5GB云空间

```bash
sudo add-apt-repository ppa:nextcloud-devs/client
sudo apt-get update
sudo apt install nextcloud-client
```

添加PPA源的时候需要翻墙

> 网盘要先设置代理，才能登录，不然会卡在登录界面
> 取消大于500M就不同步的设置

#### MarkDown笔记管理/编辑软件

##### 笔记管理软件 VNote

VNote
Github下载

创建快捷方式
1. 把程序包放到`/opt/`目录
```bash
sudo mkdir /opt/VNote
sudo cp ~/桌面/VNote-2.7.1-x86_64.AppImage /opt/VNote/
```
2. 启动“启动应用程序”
3. 添加，浏览选择程序包路径，
4. 将启动项拖到桌面
5. 右键 -> 属性 -> 权限 勾选 允许作为程序执行文件
6. 从Github下载图标，并放到`/opt/VNote/`目录下
```bash
sudo mv vnote.png /opt/VNote/
```
7. 右键快捷方式，点击icon设置icon，再将制作好的快捷方式也放到`/opt/VNote/`目录下
```
sudo mv ~/桌面/VNote-2.7.1-x86_64.AppImage.desktop  /opt/VNote/
```
8. 向`/usr/share/applications/VNote.desktop`创建一个硬链接，这里可以改一下名
```
sudo ln /opt/VNote/VNote-2.7.1-x86_64.AppImage.desktop /usr/share/applications/VNote.desktop
```
9. 打开应用程序列表就能看到制作好的程序快捷方式了

##### Typora
官网下载
```bash
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
# install typora
sudo apt-get install typora
```

> 有了VNote，为啥还需要Typora呢？
> 我一般用到它的两个功能
> 1. 复制HTML网页到Typora会自动生成MarkDown格式的文本，然后直接Ctrl + /就可以复制到VNote里
> 2. 编辑表格的功能很强大，可以方便地添加行列，还能拖动行列交换位置
> 安装完Typora可以在设置里下载喜欢的主题

### 高级设施

#### 原型图设计工具

pencil
官网deb包下载

#### GIF录制

silentcast
[Github地址](https://github.com/colinkeenan/silentcast)

```bash
sudo add-apt-repository ppa:sethj/silentcast
sudo apt-get update
sudo apt-get install silentcast

```

#### 图像处理工具

```bash
sudo add-apt-repository ppa:otto-kesselgulasch/gimp
sudo apt-get install gimp
```

## 娱乐设施

### Transmission
Transmission 是一个 BitTorrent 客户端软件，它支持速度限制、制作种子、远程控制、磁力链接、数据加密、损坏修复、数据来源交换等功能。

```bash
sudo apt-get install transmission
```

### 视频播放器VLC

```bash
sudo apt install vlc
```
### PDF阅读器

Okular 是一个 PDF 文档阅读软件，支持 PDF、TIFF、CHM、ODF、EPUB、mobi 等文档格式。

```bash
sudo apt install okular -y
```

### Telegram

官网下载单个可执行文件
Telegram
### 百度网盘

官网下载deb包

### Tim
[文章](https://www.lulinux.com/archives/1319)

### 网易云音乐

官网下载地址打不开

## DevOps Workflow建设 

### 语言环境

#### Java

网上有很多添加源来安装Oracle JDK的方法，但是现在这个方法已经用不了了。
作者说因为Oracle官网现在需要登录才能下载JDK所以这个源已经不再维护了。
所以我使用官网下载的方式安装Oracle JDK8

```bash
tar xf jdk-8u221-linux-x64.tar.gz
sudo mv jdk1.8.0_221/ /usr/local/
sudo ln -s /usr/local/jdk1.8.0_221/ /usr/local/jdk
sudo nano /etc/profile
```
在结尾添加如下内容
```bash
export JAVA_HOME=/usr/local/jdk/
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
export CLASSPATH=.$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib:$JAVA_HOME/lib/tools.jar
```
如果不生效，就添加到`~/.bashrc`
安装PyCharm时提示未正确配置JAVA_HOME
```bash
sudo nano /etc/environment
```
添加，**重启生效**
```bash
JAVA_HOME=/usr/local/jdk
```

检查是否安装成功
```bash
java -version
javac
```


#### Python

官网安装Anaconda的安装脚本
给予执行权限，按照提示执行即可

#### Rust

```bash
curl https://sh.rustup.rs -sSf | sh
source $HOME/.cargo/env
```
可能需要的依赖
```bash
sudo apt install build-essential
```

#### Go

```bash
# 官网下载 go1.12.7.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.12.7.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
echo "export PATH=$PATH:/usr/local/go/bin">>$HOME/.profile
# 修改Go的Workspace
echo "export GOPATH=$HOME/文档/go" >> ~/.bash_profile
source ~/.bash_profile
```

#### Ruby

```bash
sudo apt-get install ruby-full
```

### IDE/编辑器


#### 文本编辑器subline

apt安装

Install the GPG key:

```
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
```

Ensure apt is set up to work with https sources:

```
sudo apt-get install apt-transport-https
```

Select the channel to use:

- Stable

  `echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list `

- Dev

  `echo "deb https://download.sublimetext.com/ apt/dev/" | sudo tee /etc/apt/sources.list.d/sublime-text.list `

Update apt sources and install Sublime Text

```
sudo apt-get update
sudo apt-get install sublime-text
```

#### JB全家桶

##### Pycharm

执行安装脚本的时候提示需要安装JDK
正确安装JDK并配置好环境后

```bash
sudo mv ~/下载/pycharm-2019.1.3 /opt/
sudo chmod a+x /opt/pycharm-2019.1.3/bin/pycharm.sh
sudo /opt/pycharm-2019.1.3/bin/pycharm.sh
```
配置并激活PyCharm
学生邮箱可以免费激活

##### Goland


#### VSCode

直接官网deb包安装


### 辅助开发工具

#### 安装Docker
##### Install using the repository

Before you install Docker Engine - Community for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

##### SET UP THE REPOSITORY

1. Update the `apt` package index:

   ```
   sudo apt-get update
   ```

2. Install packages to allow `apt` to use a repository over HTTPS:

   ```
    sudo apt-get install \
       apt-transport-https \
       ca-certificates \
       curl \
       gnupg-agent \
       software-properties-common
   ```

3. Add Docker’s official GPG key:

   ```
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

   Verify that you now have the key with the fingerprint `9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88`, by searching for the last 8 characters of the fingerprint.

   ```
   sudo apt-key fingerprint 0EBFCD88
       
   pub   rsa4096 2017-02-22 [SCEA]
         9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
   uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
   sub   rsa4096 2017-02-22 [S]
   ```

4. Use the following command to set up the **stable** repository. To add the **nightly** or **test** repository, add the word `nightly` or `test`(or both) after the word `stable` in the commands below. [Learn about **nightly** and **test** channels](https://docs.docker.com/install/).

   > **Note**: The `lsb_release -cs` sub-command below returns the name of your Ubuntu distribution, such as `xenial`. Sometimes, in a distribution like Linux Mint, you might need to change `$(lsb_release -cs)` to your parent Ubuntu distribution. For example, if you are using `Linux Mint Tessa`, you could use `bionic`. Docker does not offer any guarantees on untested and unsupported Ubuntu distributions.

   - x86_64 / amd64
   - armhf
   - arm64
   - ppc64le (IBM Power)
   - s390x (IBM Z)

   ```
    sudo add-apt-repository \
      "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
      $(lsb_release -cs) \
      stable"
   ```

   

##### INSTALL DOCKER ENGINE - COMMUNITY

1. Update the `apt` package index.

   ```
   $ sudo apt-get update
   ```

2. Install the *latest version* of Docker Engine - Community and containerd, or go to the next step to install a specific version:

   ```
   $ sudo apt-get install docker-ce docker-ce-cli containerd.io
   ```

   > Got multiple Docker repositories?
   >
   > If you have multiple Docker repositories enabled, installing or updating without specifying a version in the `apt-get install`or `apt-get update` command always installs the highest possible version, which may not be appropriate for your stability needs.

3. To install a *specific version* of Docker Engine - Community, list the available versions in the repo, then select and install:

   a. List the versions available in your repo:

   ```
   $ apt-cache madison docker-ce
   
     docker-ce | 5:18.09.1~3-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
     docker-ce | 5:18.09.0~3-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
     docker-ce | 18.06.1~ce~3-0~ubuntu       | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
     docker-ce | 18.06.0~ce~3-0~ubuntu       | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
     ...
   ```

   b. Install a specific version using the version string from the second column, for example, `5:18.09.1~3-0~ubuntu-xenial`.

   ```
   $ sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io
   ```

4. Verify that Docker Engine - Community is installed correctly by running the `hello-world` image.

   ```
   $ sudo docker run hello-world
   ```

   This command downloads a test image and runs it in a container. When the container runs, it prints an informational message and exits.

Docker Engine - Community is installed and running. The `docker` group is created but no users are added to it. You need to use `sudo`to run Docker commands. Continue to [Linux postinstall](https://docs.docker.com/install/linux/linux-postinstall/) to allow non-privileged users to run Docker commands and for other optional configuration steps.


#### docker-compose

[帮助文档](https://docs.docker.com/compose/install/)

```bash
sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
给予执行权限
```bash
sudo chmod +x /usr/local/bin/docker-compose
```

#### 远程桌面管理

Remmina
```zsh
sudo apt-get install remmina
# usage
remmina
```
支持SSH，SFTP等协议

#### 本地API文档浏览器 Zeal

zeal

Ubuntu

Install Zeal from the official repositories:

```
sudo apt-get install zeal
```

When the official package becomes outdated, our own PPA repository provides an up-to-date version. Run the following commands to use the PPA:

```
sudo add-apt-repository ppa:zeal-developers/ppa
sudo apt-get update
sudo apt-get install zeal
```

## 渗透Workflow建设

### 基础设施

#### 虚拟机Vmvare Workstation 15

官网下载脚本

激活码
实测第一个就好用

UG5J2-0ME12-M89WY-NPWXX-WQH88

GA590-86Y05-4806Y-X4PEE-ZV8E0

YA18K-0WY8P-H85DY-L4NZG-X7RAD

UA5DR-2ZD4H-089FY-6YQ5T-YPRX6

ZF582-0NW5N-H8D2P-0XZEE-Z22VA

需要安装gcc和make作为依赖

```bash
sudo apt install gcc make
```

#### 虚拟机VBox

官网下载deb包安装

### 高级设施

## 系统

### 系统管理


#### CPU温度监控

```bash
 sudo apt install lm-sensors
```

usage
```bash
sensors
```
然而并不能看懂



#### Linux注册表管理工具

gsetting 系统自带

dconf-editor

```bash
sudo apt install dconf-editor
```


#### Ubuntu Gnome快捷键
Screenshots
Print Screen: Capture screenshot to a file (under ~/Images)
Ctrl + Print Screen: Capture screenshot to clipboard
Alt + Print Screen: Capture screenshot only of active window to a file
Shift + Print Screen: Capture region of screen to a file. Mouse turns into a region selector

Alt + Tab 切换应用程序
Alt + F2 打开应用程序 好像还能Ctrl + Enter
Ctrl + Alt + T 打开终端
Super + L 锁定屏幕
Super  显示已经打开的所有程序
Ctrl + Alt + 上下方向键 切换工作区

- System (Windows) key: Switch between overview and desktop
- Alt+F1: Switch between overview and desktop
- Alt+F2: Pop up command dialog
- Alt+Tab: Pop up application switcher
- Alt+Shift+Tab: Cycle in reverse direction in the application switcher
- Alt+*[key above Tab]*: Switch between windows of the same application in Alt+Tab
- Ctrl+Alt+Tab: Pop up [accessibility](https://wiki.gnome.org/Accessibility) switcher
- Ctrl+Shift+Alt+R: Start and end screencast recording
- Ctrl+Alt+Up/Down arrow: Switch between workspaces
- Ctrl+Alt+Shift+Up/Down arrow: Move the current window to a different workspace
- System (Windows) key+Right/Left arrow: resize & move window to take up right/left half of current workspace
- System (Windows) key+Shift+Right/Left arrow: move window to right/left monitor

Most keybindings can be viewed under the User Menu -> System Settings -> Keyboard -> Shortcuts

Control+Shift+Alt+R keybinding starts and stops the recording.

### 系统美化

#### 准备工作
```bash
sudo apt-get install gnome-tweak-tool
sudo apt-get install gnome-shell-extensions
sudo apt-get install chrome-gnome-shell
```

命令行下输入`gnome-`加两下Tab可以看到Gnome的各种小工具

1. 访问[Gnome扩展](https://extensions.gnome.org/)，需要科学上网
2. 点击安装浏览器扩展插件，安装完成后刷新页面

#### 插件推荐

- User Themes —— Load shell themes from user directory.

- Blyr —— 模糊效果

- Dash to Dock

- Appfolders Management extension —— 更方便的管理应用程序文件夹

- NetSpeed —— 显示网络上行下行速度

- Coverflow Alt-Tab —— 更酷炫的应用程序切换
- Clipboard Indicator —— 提示是否粘贴成功和保留粘贴板历史记录
- Caffeine —— 控制系统是否睡眠
- Places Status Indicator —— 直达文件夹的快捷方式

3. 
更换桌面壁纸、锁屏壁纸等 TODO：使用命令行进行设置
```zsh
# 更换桌面壁纸
gsettings set org.gnome.desktop.background picture-uri "file:/home/leon/pic/111.jpg"
```


#### 桌面程序图标编写


方法一：


1、搜索进入“启动应用程序”

2、在启动用程序首选项中选择“添加”

3、设置完名称和命令（路径）后，点击“添加”，启动器就被添加到列表里了

4、用鼠标选中刚添加的启动器，将其拖到桌面，这时就会发现产生了一个同名的.desktop文件

5、右击新建的起动器，选择属性，在权限选项卡中勾选——执行：允许以程序执行文件

6、在基本选项卡中点击图标，可以设置启动器的桌面图标。(这里感觉有点坑，Navicat里没找到icon，自己下了一个)

7、想放到启动栏的话直接拖过去就好了，不要打开之后点"锁定到启动器"

方法二

linux下.desktop桌面程序图标文件编写

linux下编写.desktop文件,此处以eclipse为例. 

```
[Desktop Entry]         # 每个desktop文件都已这个标签开始，说明这是一个Desktop Entry 文件.
Version = 1.0           # 标明Desktop Entry的版本(可选).
Name=eclipse            # 程序名称(必须),这里是以创建一个eclipse的快捷方式为例.
GenericName = eclipse   # 程序通用名称(可选).
Comment = eclipse       # 程序描述(可选).
Exec = /home/liudezhi_text/eclipse/java-oxygen/eclipse/eclipse  # 程序的启动命令(必选),可以带参数运行
                                                                #+当下面的Type为Application，此项有效
Icon = /home/liudezhi_text/eclipse/java-oxygen/eclipse/icon.xpm # 设置快捷方式的图标(可选).
Terminal = false                    # 是否在终端中运行(可选),当Type为Application,此项有效.
Type = Application                  # desktop的类型(必选),常见值有“Application”和“Link”.
Categories = GNOME;Application;Network;                         # 注明在菜单栏中显示的类别(可选)              
```

#### 网站快捷方式编写

从`/user/share/applications/`复制Chrome的快捷方式到桌面，修改其命令即可。

#### 终端美化
```bash
sudo apt install zsh -y
```
Oh-My-Zsh
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
这个过程没有进度条，耐心等待

修改默认shell
```zsh
chsh -s `which zsh`
```
打开终端->编辑->首选项
- 设置终端字体、颜色
- 设置透明背景

编辑`~/.zshrc`
- 设置环境变量
- 修改主题`ys`
- 设置zsh插件 TODO

### 系统优化

### 系统备份

#### 系统硬盘备份

Clonezilla
[下载地址](http://clonezilla.nchc.org.tw/clonezilla-live/download/download.php?branch=alternative)

官网下载iso镜像刻录到U盘即可
这个工具可以将整个硬盘备份成一个`ISO`文件进行备份，且对存储空间的格式没有要求

### 系统快照还原 Timeshift

```bash
sudo apt-add-repository -y ppa:teejee2008/ppa
sudo apt-get install timeshift
```

可以创建快照进行还原备份，但存储空间需要Linux文件系统

如果你的文件系统是btrfs，还可以使用这种模式
```bash
sudo apt install btrfs-tools
```

#### 硬盘分区格式化gparted

```bash
sudo apt-get install gparted
```

### 其他工具

#### 小工具

- flglet
- 字符logo生成
```zsh
sudo apt install flglet
# usage
flglet anitya
```

#### U盘刻录工具

etcher
[官网地址](https://www.balena.io/etcher/)

#### Todo List 

Gnome-待办事项
gnome软件商店可以安装


#### PowerShell

1.  Download the Microsoft repository GPG keys
```bash
wget -q https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb
```
2. Register the Microsoft repository GPG keys
```bash
sudo dpkg -i packages-microsoft-prod.deb
```
3. Update the list of products
```bash
sudo apt-get update
```
4. Install PowerShell
```bash
sudo apt-get install -y powershell
```
5. Start PowerShell
```bash
pwsh
```

#### WPS

官网下载DEB包安装

启动WPS for Linux后，出现提示”系统缺失字体” 。

出现提示的原因是因为WPS for Linux没有自带windows的字体，只要在Linux系统中加载字体即可。

具体操作步骤如下：

1、下载[缺失的字体文件](https://link.zhihu.com/?target=https%3A//pan.baidu.com/s/1mh0lcbY)，然后复制到Linux系统中的/usr/share/fonts文件夹中。

下载完成后，解压并进入目录中，继续执行：

```bash
$ sudo cp * /usr/share/fonts
```

2、执行以下命令,生成字体的索引信息：

```bash
$ sudo mkfontscale
$ sudo mkfontdir
```

3、运行fc-cache命令更新字体缓存。

```bash
$ sudo fc-cache
```

4、重启wps即可，字体缺失的提示不再出现。


#### 使Ubuntu能够挂载exfat格式的移动硬盘

```bash
sudo apt-get install exfat-utils
```

#### Ubuntu设置默认程序

1. 在设置里设置，这里能设置的很少
2. 在要打开的文件右键，属性中设置


#### OWASP ZAP

官网下载 Linux Installer
管理员权限执行

很jb好用


#### nmap zenmap

```bash
sudo apt install namp zenmap
```


#### ifconfig等网络工具

```bash
sudo apt install net-tools
```

不知道除了ifconfig之外还有什么工具


#### arp

Ubuntu自带

```bash
arp
arp -a
```

#### traceroute

```bash
sudo apt install traceroute
```

#### whois

```bash
sudo apt install whois
```

#### Vagrant

官网下载deb包安装，我选择的是Debian的版本


#### Metasploit Framework

##### 官网安装
```bash
curl https://raw.githubusercontent.com/rapid7/metasploit-omnibus/master/config/templates/metasploit-framework-wrappers/msfupdate.erb > msfinstall && \
  chmod 755 msfinstall && \
  ./msfinstall
```
会同时安装Ruby和PostgreSQL

##### MSF Web 服务API
<pre><font color="#06989A">    ############################################################</font>
<font color="#06989A">    ##              </font><font color="#34E2E2"><u style="text-decoration-style:single"><b>MSF Web Service Credentials</b></u></font><font color="#06989A">               ##</font>
<font color="#06989A">    ##                                                        ##</font>
<font color="#06989A">    ##        Please store these credentials securely.        ##</font>
<font color="#06989A">    ##    You will need them to connect to the webservice.    ##</font>
<font color="#06989A">    ############################################################</font>

<font color="#34E2E2"><b>MSF web service username</b></font>: anitya
<font color="#34E2E2"><b>MSF web service password</b></font>: Slytherin!@!389
<font color="#34E2E2"><b>MSF web service user API token</b></font>: 23c4404e5a1bbb0031fd7b967481be4004fba9b34df5dc123c7a626f46ffbfd9609ccc0979912cee
</pre>



#### hydra

安装一些模块的依赖
```bash
sudo apt-get install libssl-dev libssh-dev libidn11-dev libpcre3-dev \
                 libgtk2.0-dev libmysqlclient-dev libpq-dev libsvn-dev \
                 firebird-dev libmemcached-dev libgpg-error-dev \
                 libgcrypt11-dev libgcrypt20-dev
```

下载源码，编译安装
```bash
./configure
make
sudo make install
```

#### 安装PostgreSQL

```bash
sudo apt-get install postgresql-10 
```

##### 用户配置

通过如下命令登陆postgreSQL：

```
sudo -u postgres psql
```

进入PostgreSQL命令行视图，（可以看到以`postgres=#`开头的提示符），然后执行如下命令将postgres用户的密码修改为"password"：

```
ALTER USER postgres WITH PASSWORD 'password';
```

执行 `\q` 命令推出PostgreSQL命令行。

可以在命令行界面以超级用户权限设置postgre用户的密码：
先清除密码：

```
sudo passwd -d postgres
```

在设置密码：

```
sudo -u postgres passwd
```

按照提示，输入两次新密码即可。

在postgresql中创建msf用户和msf数据库
```bash

postgres=# create user msf with password 'msf';
CREATE ROLE
postgres=# \l
postgres=# create database msf owner 'msf';
CREATE DATABASE
postgres-# \q
```
在msfconsole中设置一下
```bash
msf5 > db_connect msf:msf@localhost/msf
[-] Connection already established. Only one connection is allowed at a time.
[-] Run db_disconnect first if you wish to connect to a different data service.

Current connection information:
[*] Connected to remote_data_service: (https://localhost:5443). Connection type: http. Connection name: local-https-data-service.
msf5 > db_disconnect 
Successfully disconnected from the data service: remote_data_service: (https://localhost:5443).
msf5 > db_connect msf:msf@localhost/msf
[-] Connection already established. Only one connection is allowed at a time.
[-] Run db_disconnect first if you wish to connect to a different data service.

Current connection information:
[*] Connected to msf. Connection type: postgresql.
msf5 > db_status 
[*] Connected to msf. Connection type: postgresql.
```

新版msf5直接
```bash
msfdb init
```
就行了


### 使sudo命令继承环境变量

使用`visudo`命令修改`/etc/sudoers`文件
```bash
sudo visudo
```

`env_reset`改成`!env_reset`
再把`secure_path`注释掉
```bash
Defaults        !env_reset
Defaults        mail_badpass
#Defaults       secure_path=
```

### 使用Python3开启简单http服务器

```bash
sudo python -m http.server 88
```

### BeEF

需要Ruby作为依赖
```bash
sudo apt-get install ruby ruby-dev
git clone https://github.com/beefproject/beef
cd beef
sudo ./install
```
安装到升级gem时遇到Timeout的问题
```bash
sudo gem install rubygems-update  # might need to be admin/root

sudo update_rubygems
```

### 安装 cisco packet tracer 

官网下载

https://linuxhint.com/install_packet_tracer_ubuntu_1804/

### 安装socat

```bash
sudo apt install socat
```


### pencil

原型图设计

官网deb包下载

### 安装GNS3 交换机模拟软件

```bash
sudo add-apt-repository ppa:gns3/ppa
sudo apt-get update
sudo apt-get install gns3-gui
```

这个好像也把Wireshark一起装上了

### m0nowall 防火墙

[官方网站](https://m0n0.ch/wall/index.php)

### Resilio Sync

官网下载deb包

安装

```
#启动服务
sudo systemctl enable resilio-sync
```
Linux下只有Web GUI
[Web GUI](http://localhost:8888/gui/)
因为安全的原因会以最小权限运行
[官方指南](https://help.resilio.com/hc/en-us/articles/206178924-Installing-Sync-package-on-Linux)

### PicGo

[Github](https://github.com/Molunerfinn/PicGo)
Github下载
直接安装即可

### Tusk 印象笔记的Linux客户端

看起来是基于Web

直接搜索下载即可

没有图标比较难受

### tree

看文件目录的工具

```bash
sudo apt intstall tree
```