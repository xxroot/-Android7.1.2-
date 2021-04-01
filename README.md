
1.安装所需工具
# apt-get install -y openjdk-8-jdk openjdk-8-jre git flex bison gperf build-essential 
libncurses5-dev:i386 git flex bison gperf build-essential libncurses5-dev:i386 libx11-
dev:i386 libreadline6-dev:i386 libgl1-mesa-dev g++-multilib tofrodos python-markdown 
libxml2-utils xsltproc zlib1g-dev:i386 dpkg-dev libsdl1.2-dev libesd0-dev git-core gnupg 
flex bison gperf build-essential zip curl zlib1g-dev gcc-multilib g++-multilib libc6-dev-
i386 lib32ncurses5-dev x11proto-core-dev libx11-dev lib32z-dev ccache libgl1-mesa-dev 
libxml2-utils xsltproc unzip m4

<1>.配置JDK
# sudo apt-get install openjdk-8-jdk
# sudo vim /etc/profile
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64
export JRE_HOME=${JAVA_HOME}/jre 
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib 
export PATH=${JAVA_HOME}/bin:$PATH

<2>.安装需要工具
# sudo apt-get install m4 g++-multilib gcc-multilib lib32ncurses5-dev lib32readline6-dev 
lib32z1-dev flex curl bison

<3>.查看并开启jack-server
# ./prebuilts/sdk/tools/jack-admin list-server
# ./prebuilts/sdk/tools/jack-admin start -server
# ./prebuilts/sdk/tools/jack-admin stop -server

2.使用清华源下载源码
# curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo -o repo
# chmod +x repo
# sudo mv ./repo /usr/local/bin
# sudo vim /usr/local/bin/repo //把REPO_URL='改为清华源'
REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/'

<1>.下载源码的初始化包（加速源码下载时间看网速而定)
可下载某一天初始化包
# wget https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-20180901.tar //33G
也可下载最新的初始化包
# wget https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar //58G
# tar -xvf aosp-latest.tar
# cd ~/aosp
# cd .repo/manifests
# git branch -a //查看分支
# git tag //查看打的tag

<2>.下载pixel手机驱动(必须下载，否则手机不能开机),选择NZH54D
Android版本列表：https://source.android.google.cn/setup/start/build-numbers?hl=zh-cn#source-code-tags-and-builds
驱动地址:https://developers.google.com/android/drivers
驱动别弄错了，不然pixel起不来：Pixel binaries for Android 7.1.2 (NZH54D)
细分版本	 分支 版本 支持的设备
NZH54D	 android-7.1.2_r33  Nougat	Pixel XL、Pixel

注意：
NZH54D：对应Google和高通的vend.img和外设驱动程序
android-7.1.2_r33：要切换的android分支，必须严格匹配,否则设备起不来
Pixel XL、Pixel：支持的手机设备

<3>.解压驱动分别为两个.sh文件,然后拷贝到~/asop
# tar zxvf google_devices-sailfish-nzh54d-cc9ac173.tgz
# tar zxvf qcom-sailfish-nzh54d-56bb51e3.tgz
# cp extract-google_devices-sailfish.sh ~/asop
# cp extract-qcom-sailfish.sh ~/asop
# ./extract-google_devices-sailfish.sh
# ./extract-qcom-sailfish.sh
最后输入 “I ACCEPT”， Enter同意即可
Pixel内部代号：sailfish(旗鱼)
Pixel XL内部代号：marlin(金枪鱼)
<4>.根据上面选的版本(NZH54D)来切换代码分支(android-7.1.2_r33)
# cd ~/aosp
# repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest -b android-7.1.2_r33	
然后运行同步,获取完整的源码(大约需要1天时间,反正我这边3天都没成功).
# repo sync -j8

3.编译
# source build/envsetup.sh
# lunch aosp_sailfish-userdebug
接下里调整一个Java参数，要不然会出现Java OOM错误
# export JACK_SERVER_VM_ARGUMENTS="-Dfile.encoding=UTF-8 -XX:+TieredCompilation -Xmx8192m"
# make -j8

4.刷机
<1>.全刷
# cd out/target/product/sailfish
# adb reboot bootloader
# fastboot -w flushall
注意：此命令会在当前文件夹中查找全部img文件，将这些img文件烧写到全部相应的分区中，并又一次启动手机。
# fastboot reboot
# fastboot reboot-bootloder //restart bootloader

//清空分区
# fastboot erase boot
# fastboot erase system
# fastboot erase data
# fastboot erase cache
上面的命令也可以简化成一条命令
fastboot erase system -w

<1>.单刷
最重要刷boot.img、system.img、userdata.img、vendor.img这四个固件.
# adb reboot bootloader 
# fastboot flash boot boot.img
# fastboot flash system system.img
# fastboot flash userdata userdata.img
# fastboot flash vendor vendor.img 
# fastboot flash recovery recovery.img //没有编出来，可选
# fastboot flash cache cache.img //没有编出来，可选
# fastboot flash persist persist.img //没有编出来，可选
# fastboot reboot

