# 在Ubuntu18.04上编译
#一键安装依赖
```makefile
sudo apt install -y bc bison build-essential ccache curl flex g++-multilib gcc-multilib git gnupg gperf lib32ncurses5-dev lib32readline-dev lib32z1-dev liblz4-tool libncurses5-dev libsdl1.2-dev libssl-dev libwxgtk3.0-dev libxml2 libxml2-utils lzop pngcrush rsync schedtool squashfs-tools xsltproc zip zlib1g-dev imagemagick libbz2-dev libssl-dev lzma ncftp bash-completion m4 openjdk-8-jdk python clang
```


#设置 git 用户名和邮箱
```makefile
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
（双引号中的 “you@example.com” 填写您的 github 所使用的邮箱，双引号中的 “Your Name” 填写您的 github 用户名，不要把双引号去掉。）
```
#生成 ssh 并添加到 github
```makefile
https://help.github.com/articles/connecting-to-github-with-ssh/
（如果您不将 ssh 添加到 github 的话，您可能会遭遇下载受限，从而导致接下来的同步源码失败。）
```

#下载魔趣版的repo（比较好用）
```makefile
mkdir ~/bin
PATH=~/bin:$PATH
curl https://raw.githubusercontent.com/MoKee/git-repo/stable/repo > ~/bin/repo
chmod a+x ~/bin/repo
```

#新建源码目录
```makefile
mkdir xxx
cd xxx
```

#设置repo
```makefile
repo init -u git://github.com/DotOS/manifest.git -b dot-p --depth=1
```

#设置编译机型的 device tree、kernel 和私有文件
```makefile
把dot_dipper.xml添加到xxx/.repo/local_manifests即可（这是隐藏目录）
```

#使用repo同步源码
```makefile
repo sync -c -j8 --force-sync --no-clone-bundle --no-tags  （-j8表示使用8个线程）
```
#删除会惹来麻烦的dependencies
```makefile
sudo rm -rf device/xiaomi/sdm845-common/lineage.dependencies
```

#设置 ccache 提高编译效率（可选）
在源码路径使用:
```makefile
echo export USE_CCACHE=1 >> ~/.bashrc
prebuilts/misc/linux-x86/ccache/ccache -M 50G
```

#解决 jack 内存不足导致编译失败的问题、flex 报错问题
```makefile
echo 'export JACK_SERVER_VM_ARGUMENTS="-Dfile.encoding=UTF-8 -XX:+TieredCompilation -Xmx4096m"' >> ~/.bashrc
echo "export LANG=C" >> ~/.bashrc
source ~/.bashrc
```

#源码去×（可选）
```makefile
sed -i 's@www.google.com/generate_204@connect.rom.miui.com/generate_204@g' frameworks/base/services/core/java/com/android/server/connectivity/NetworkMonitor.java
sed -i 's@www.google.com/gen_204@connect.rom.miui.com/generate_204@g' frameworks/base/services/core/java/com/android/server/connectivity/NetworkMonitor.java
sed -i 's@connectivitycheck.gstatic.com/generate_204@connect.rom.miui.com/generate_204@g' frameworks/base/services/core/java/com/android/server/connectivity/NetworkMonitor.java
sed -i 's@play.googleapis.com/generate_204@connect.rom.miui.com/generate_204@g' frameworks/base/services/core/java/com/android/server/connectivity/NetworkMonitor.java
```

#设置编译变量
```makefile
. ./build/envsetup.sh
```

#开始编译
```makefile
brunch dot_dipper-userdebug -j2(-j2同样表示使用两个线程）
```

#编译完成后，会在 源码目录/out/target/product/您的机型 文件夹生成对应的刷机包、镜像和一些编译出来的文件。
假如在编译过程中遇到报错，尝试 Google 对应的报错内容，不建议使用国内搜索引擎。
每次重新编译，可执行清理步骤
```makefile
make clean
```

本文基于https://www.htcp.net/4595.html 再次修改
