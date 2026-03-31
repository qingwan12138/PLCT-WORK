# **在x86架构的ubuntu 24.04.03 LTS上通过ruyisdk虚拟环境构建运行 nxengine-evo引擎并运行cavestoryen**
本文档详细说明了如何在运行x86架构的 ubuntu 虚拟机上通过ruyisdk虚拟环境从源代码编译和运行 nxengine-evo引擎并运行cavestoryen。
## 获取并编译 nxengine-evo
```bash
$ git clone --recursive https://github.com/nxengine/nxengine-evo.git
```

## 在 x86 Ubuntu 上构建 openEuler RISC-V Sysroot(同teeworlds) 
### 安装必要的模拟器和包管理工具
```bash
$ sudo apt update
$ sudo apt install -y qemu-user-static dnf git cmake
```
### 创建并初始化 openEuler 的目录结构
```bash
$ mkdir -p ~/oe-sysroot/usr/bin
$ sudo cp /usr/bin/qemu-riscv64-static ~/oe-sysroot/usr/bin/
```

### 使用 dnf 填充 openEuler 系统
```bash
$ sudo dnf --installroot=$HOME/oe-sysroot \
           --forcearch=riscv64 \
           --releasever=24.03 \
            --repofrompath=oe-base,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/OS/riscv64/ \
            --repofrompath=oe-update,https://mirrors.huaweicloud.com/openeuler/openEuler-24.03-LTS/update/riscv64/ \
            --disablerepo=* --enablerepo=oe-base,oe-update \
            --nogpgcheck \
            --setopt=install_weak_deps=False \
            install -y bash coreutils dnf openEuler-release
```

### 进入 openEuler 环境安装依赖库
```bash
$ sudo chroot ~/oe-sysroot /bin/bash
$ dnf install -y SDL2-devel freetype-devel libpng-devel wavpack-devel mesa-dri-drivers mesa-libGL-devel libX11-devel zlib-devel openssl-devel libXext-devel libXcursor-devel libXinerama-devel libXi-devel  --nogpgcheck --releasever=24.03
```

![alt text](image/依赖需求.png)
### 其中sdl2全家桶需手动编译

在依赖库目录下编写riscv64.toolchain文件(为了方便补充缺少的依赖手动编译riscv文件，我在依赖库目录下也创建了虚拟环境)
```bash
$ set(CMAKE_SYSTEM_NAME Linux)
$ set(CMAKE_SYSTEM_PROCESSOR riscv64)

$ set(CMAKE_C_COMPILER /home/cjh/桌面/依赖库/ruyi-venv-sipeed-lpi4a/bin/riscv64-plctxthead-linux-gnu-gcc)
$ set(CMAKE_CXX_COMPILER /home/cjh/桌面/依赖库/ruyi-venv-sipeed-lpi4a/bin/riscv64-plctxthead-linux-gnu-g++)

$ set(CMAKE_SYSROOT /home/cjh/oe-sysroot)
$ set(CMAKE_FIND_ROOT_PATH /home/cjh/oe-sysroot)

$ set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
$ set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
$ set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
$ set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)
$ add_definitions(-DANGELSCRIPT_EXPORT -DAS_RISCV64)
```
依赖补充流程类似如下

```bash
$ cd ~/桌面/依赖库
$ wget https://github.com/libsdl-org/SDL_mixer/releases/download/release-2.6.3/SDL2_mixer-2.6.3.tar.gz
$ tar -zxvf SDL2_mixer-2.6.3.tar.gz
$ cd SDL2_mixer-2.6.3
```
配置交叉编译
```bash
$ mkdir build && cd build
$ cmake .. \
  -DCMAKE_TOOLCHAIN_FILE=/home/cjh/桌面/依赖库/riscv-toolchain.cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/home/cjh/oe-sysroot/usr
```

编译并安装到 Sysroot
```bash
$ make DESTDIR=/home/cjh/oe-sysroot install
```


## 运用RuyiSDK 虚拟环境交叉编译
### 安装并激活 Ruyi 虚拟环境
这一步可以根据你不同开发板的开发环境安装不同的虚拟环境进行编译，本文采取的是licheepi4a
具体构建过程可参考(官网)(https://ruyisdk.org/docs/Package-Manager/intergration)
### 编译
```bash
$ cd ~/桌面/nxengine-evo/build
$ cmake .. \
  -DCMAKE_TOOLCHAIN_FILE=../riscv-toolchain.cmake \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/home/cjh/oe-sysroot/usr
$ make
```
### 编译完成
```bash
$ file ./nxengine-evo
```
![alt text](image/file属性.png)

##  游戏运行
### 游戏本体下载
从[cavestoryen.zip](https://www.cavestory.org/downloads/cavestoryen.zip) 提取资源,将文件夹中的所有内容复制到该文件夹，并将Doukutsu.exe放入项目目录,然后运行nxextract从.exe文件中获取数据。
```bash
$ ./build/nxextract
```
### 游戏加载
```bash
$ LD_LIBRARY_PATH=~/oe-sysroot/usr/lib:~/oe-sysroot/usr/lib64 \
  SDL_VIDEODRIVER=x11 \
  SDL_RENDER_DRIVER=software \
  SDL_AUDIODRIVER=dummy \
$ ruyi-qemu -cpu rv64 -L ~/oe-sysroot ./nxengine-evo
```
![alt text](image/loading.png)

![alt text](image/启动页面.png)

![alt text](image/对话.png)
