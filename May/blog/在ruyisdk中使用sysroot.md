# 本文详细介绍了如何在RuyiSDK中使用sysroot

## sysroot
### 本文将以OpenRuyi操作系统为例
```bash
#下载OpenRuyi rootfs
$ wget https://releases.openruyi.cn/creek/2026.04/rva23/openRuyi-2026.04-rootfs-oci.tar.zst
```

### 安装 ruyi-qemu (user 模式)
```bash
$ ruyi install qemu-user-riscv-upstream
# info: package qemu-user-riscv-upstream-11.0.0-ruyi.20260421 installed to /home/cjh/.local/share/ruyi/binaries/x86_64/qemu-user-riscv-upstream-11.0.0-ruyi.20260421
```

### 配置 Linux 机制 binfmt_misc
#### 确认 ruyi-qemu 可执行文件的位置
```bash
$ ls /home/cjh/.local/share/ruyi/binaries/x86_64/qemu-user-riscv-upstream-11.0.0-ruyi.20260421/bin/qemu-riscv64 
```

#### 建立配置目录
```bash
$ mkdir -p /home/cjh/.local/share/ruyi/binaries/x86_64/qemu-user-riscv-upstream-11.0.0-ruyi.20260421/etc/binfmt.d/
```
#### 写入配置文件
```bash
$ nano /home/cjh/.local/share/ruyi/binaries/x86_64/qemu-user-riscv-upstream-11.0.0-ruyi.20260421/etc/binfmt.d/qemu-riscv64.conf
$ :ruyi-qemu-riscv64:M::\x7f\x45\x4c\x46\x02\x01\x01\x00\x00\x00\x00\x00\x00\x00\x00\x00\x02\x00\xf3\x00:\xff\xff\xff\xff\xff\xff\xff\x00\xff\xff\xff\xff\xff\xff\xff\xff\xfe\xff\xff\xff:/home/cjh/.local/share/ruyi/binaries/x86_64/qemu-user-riscv-upstream-11.0.0-ruyi.20260421/bin/qemu-riscv64:POCF
```
#### 将其部署到系统
```bash
$ sudo cp /home/cjh/.local/share/ruyi/binaries/x86_64/qemu-user-riscv-upstream-11.0.0-ruyi.20260421/etc/binfmt.d/qemu-riscv64.conf /etc/binfmt.d/qemu-riscv64.conf
$ sudo systemctl restart systemd-binfmt
```
#### 检查状态
```bash
$ cat /proc/sys/fs/binfmt_misc/ruyi-qemu-riscv64
enabled
interpreter /home/cjh/.local/share/ruyi/binaries/x86_64/qemu-user-riscv-upstream-11.0.0-ruyi.20260421/bin/qemu-riscv64
flags: POCF
offset 0
magic 7f454c460201010000000000000000000200f300
mask ffffffffffffff00fffffffffffffffffeffffff
```
### 进入openRuyi rootfs
```bash
$ sudo chroot ~/openRuyi-2026.04-rootfs-oci /bin/bash
```

## Ruyi-venv中调用OpenRuyi rootfs
### Ruyi相关命令
```bash
# ruyi venv -h
cjh@cjh:~/桌面$ ruyi venv -h
用法：ruyi venv [-h] [--name NAME] [--toolchain TOOLCHAIN] [--emulator EMULATOR]
             [--with-sysroot] [--without-sysroot]
             [--copy-sysroot-from-pkg COPY_SYSROOT_FROM_PKG]
             [--copy-sysroot-from-dir COPY_SYSROOT_FROM_DIR]
             [--symlink-sysroot-from-dir SYMLINK_SYSROOT_FROM_DIR]
             [--extra-commands-from EXTRA_COMMANDS_FROM]
             profile dest

位置参数：
  profile               环境使用的配置文件
  dest                  新虚拟环境的路径

选项：
  -h, --help            显示该帮助信息并退出
  --name, -n NAME       自定义虚拟环境的名称
  --toolchain, -t TOOLCHAIN
                        要使用的工具链软件包的指示表达式（atoms）
  --emulator, -e EMULATOR
                        要使用的模拟器软件包的指示表达式（atom）
  --with-sysroot        在新虚拟环境内准备一个全新的 sysroot（默认）
  --without-sysroot     不在新虚拟环境内准备任何 sysroot
  --copy-sysroot-from-pkg, --sysroot-from COPY_SYSROOT_FROM_PKG
                        要使用的 sysroot 软件包的指示表达式（atom），如工具链软件包也内置了 sysroot 则优先于它
  --copy-sysroot-from-dir COPY_SYSROOT_FROM_DIR
                        Copy the sysroot from the given directory into the
                        virtual environment
  --symlink-sysroot-from-dir SYMLINK_SYSROOT_FROM_DIR
                        Symlink the virtual environment's sysroot to the given
                        existing directory
  --extra-commands-from EXTRA_COMMANDS_FROM
                        要向新虚拟环境添加额外命令，这些命令的提供者软件包的指示表达式（atoms）
```

### 通过软连接的方式创建 ruyi-venv(--symlink-sysroot-from-dir)
该命令不复制文件，此时虚拟环境中的 sysroot 目录实际上是一个软链接，指向你指定的物理目录，但如果你删除了原始目录，虚拟环境就坏了，并且在虚拟环境里误删文件会破坏原始数据。
```bash
$ ruyi venv -t gnu-plct --symlink-sysroot-from-dir /home/cjh/openRuyi-2026.04-rootfs-oci generic  ./my_venv
```

### 补充相关依赖
```bash
$ sudo chroot ~/openRuyi-2026.04-rootfs-oci /bin/bash
$ dnf update
$ dnf install 
```

