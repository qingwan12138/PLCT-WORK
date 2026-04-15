# 虚拟环境

Ruyi 包管理器提供了 `venv` 命令，用于组合工具链、模拟器、sysroot 等组件，快速创建一个用于交叉编译的独立虚拟环境。该虚拟环境可以为不同 RISC-V 开发板或平台准备匹配的编译配置，减少手动配置复杂度。

## 主要作用
- 将工具链、模拟器、目标 sysroot、交叉编译配置等整合到一个目录中。
- 生成可激活的编译环境，支持 `PATH`、`PS1<vnev-name>` 和构建工具链的自动设置。
- 使不同项目和不同目标平台的编译环境彼此隔离。
- 提供 `toolchain.cmake`等交叉编译辅助文件。

## 常见命令格式

```bash
ruyi venv -t <toolchain> <profile> <venv-dir>
```

参数说明：

- `-t <toolchain>`：指定要使用的 Ruyi 工具链包，如 `gnu-upstream`、`gnu-plct`、`llvm-upstream` 等。
- `<profile>`：选择预置的虚拟环境配置，如 `generic`、`milkv-duo`、`xiangshan-nanhu`、`sipeed-lpi4a` 等。
- `<venv-dir>`：指定虚拟环境目标目录。

使用 `-h` 可以查看具体帮助：

```bash
ruyi venv -h
```

## 示例

- 使用 GNU 上游工具链创建 `generic` 虚拟环境：

```bash
ruyi venv -t gnu-upstream generic ./generic-venv
```

- 使用 PLCT 工具链创建 Milkv Duo 虚拟环境：

```bash
ruyi venv -t gnu-plct milkv-duo ./milkv-venv
```

- 使用 PLCT 工具链创建香山南湖虚拟环境：

```bash
ruyi venv -t gnu-plct xiangshan-nanhu ./nanhu-venv
```

- 使用 LLVM 工具链并从 GNU 工具链导入 sysroot：

```bash
ruyi venv -t llvm-upstream --sysroot-from gnu-plct generic ./llvm-venv
```

- 指定版本工具链创建荔枝派 4A 虚拟环境：

```bash
ruyi venv -t "gnu-plct-xthead(==0.20231212.0)" sipeed-lpi4a ./sipeed-venv
```
虚拟环境是用于集成工具链、模拟器等工具的，创建编译环境之前请确保对应的 Ruyi 软件包已经安装。

### 工具链与预置配置组合

Ruyi 包管理在建立虚拟环境之前会检查该环境是否存在冲突，但是并不保证建立成功的环境一定可用。灵活使用该功能需要您对这些工具链有一定的了解，一般情况下则可以直接参考下面的表格。

这里列出了经过测试确认可用的配置组合：

|   工具链(toolchain)   |   sysroot   |  预置配置(profile)  |  构建目标 |
| :--------------------: | :----------: | :-----------------: | :---: |
|        gnu-plct        |     自带     |       generic       | riscv64 架构的 Linux 操作系统 |
|        gnu-plct        |     自带     |      milkv-duo      | 使用 glibc 的 MilkV Duo 系列开发板镜像 |
|        gnu-plct        |     自带     |   xiangshan-nanhu   | 香山南湖 |
|      gnu-upstream      |     自带     |       generic       | riscv64 架构的 Linux 操作系统 |
|    gnu-plct-xthead    |     自带     |    sipeed-lpi4a    | Thead C910 |
| gnu-plct-rv64ilp32-elf |      无      | baremetal-rv64ilp32 | rv64ilp32 裸机 |
|     llvm-plct         |   gnu-plct   |       generic       | riscv64 架构的 Linux 操作系统 |
|     llvm-upstream     | gnu-upstream |       generic       | riscv64 架构的 Linux 操作系统 |

|   厂商发布的二进制工具链(toolchain)   |   sysroot   |  预置配置(profile)  |  构建目标 |
| :--------------------: | :----------: | :-----------------: | :---: |
| gnu-milkv-milkv-duo-elf-bin  |     无     |   generic   | Milkv Duo 系列开发板裸机（需要额外传参） |
| gnu-milkv-milkv-duo-bin      |     自带   |   generic   | 使用 glibc 的 Milkv Duo 系列开发板镜像（需要额外传参） |
| gnu-milkv-milkv-duo-musl-bin |     自带   |   generic   | 使用 musl 的 Milkv Duo 系列开发板镜像（需要额外传参） |

编译工具链的更多相关信息请参考[RuyiSDK 编译工具](../Other/GNU-type)。


## 虚拟环境入门
下面以 gnu-upstream 为例详细描述虚拟环境集成，路径、 ``PS1`` 等环境相关的信息请根据实际理解和修改。

建立一个干净的目录用于运行用例：
```bash input="1-2"
$ mkdir hello-ruyi
$ cd hello-ruyi
```

建立虚拟环境：

```bash input="1"
$ ruyi venv -t gnu-upstream -e qemu-user-riscv-upstream generic ./myhone-venv
info: Creating a Ruyi virtual environment at myhone-venv...
info: The virtual environment is now created.

You may activate it by sourcing the appropriate activation script in the
bin directory, and deactivate by invoking \'ruyi-deactivate\'.

A fresh sysroot/prefix is also provisioned in the virtual environment.
It is available at the following path:

    /home/myhone/hello-ruyi/myhone-venv/sysroot

The virtual environment also comes with ready-made CMake toolchain file
and Meson cross file. Check the virtual environment root for those;
comments in the files contain usage instructions.
```

该虚拟环境集成了 gnu-upstream 工具链和 qemu-user-riscv-upstream 模拟器，使用目标 riscv64 Linux 操作系统的 generic 配置，将编译环境建立在 ``./myhome-venv`` 目录下（亦可用绝对路径）。命令的输出给出了激活编译环境的方法，退出编译环境的命令， sysroot 目录的绝对路径以及一些其他信息。

## 激活与退出

创建完成后，进入虚拟环境目录并激活环境：

```bash
source ./<venv-dir>/bin/ruyi-activate
```

激活后，`PATH`、`PS1` 等环境变量会被调整，您可以直接调用交叉编译工具链命令。

退出虚拟环境：

```bash
ruyi-deactivate
```


在虚拟环境目录可以看到编译环境相关的文件：

```bash input="1"
$ ls ./myhone-venv/
bin                                        ruyi-venv.toml
binfmt.conf                                sysroot
meson-cross.ini                            sysroot.riscv64-unknown-linux-gnu
meson-cross.riscv64-unknown-linux-gnu.ini  toolchain.cmake
ruyi-cache.v1.toml                         toolchain.riscv64-unknown-linux-gnu.cmake
```
其中：
- `bin/`：`toolchain`工具链、`ruyi-activate`、`ruyi-deactivate`、`ruyi-qemu` 等。
- `binfmt.conf` 是可用于 systemd-binfmt 的示例配置。
- `meson-cross.ini`：Meson 交叉编译配置文件。
- `toolchain.cmake`：Cmake 交叉编译配置文件。
- `sysroot`：目标系统的 sysroot。
- `ruyi-venv.toml`：虚拟环境元数据。

在 ``bin`` 目录下可以查看到可用的工具链二进制：

```bash input="1"
$ ls ./myhone-venv/bin/
riscv64-unknown-linux-gnu-addr2line  riscv64-unknown-linux-gnu-gcc-ranlib     riscv64-unknown-linux-gnu-nm
riscv64-unknown-linux-gnu-ar         riscv64-unknown-linux-gnu-gcov           riscv64-unknown-linux-gnu-objcopy
riscv64-unknown-linux-gnu-as         riscv64-unknown-linux-gnu-gcov-dump      riscv64-unknown-linux-gnu-objdump
riscv64-unknown-linux-gnu-c++        riscv64-unknown-linux-gnu-gcov-tool      riscv64-unknown-linux-gnu-ranlib
riscv64-unknown-linux-gnu-cc         riscv64-unknown-linux-gnu-gdb            riscv64-unknown-linux-gnu-readelf
riscv64-unknown-linux-gnu-c++filt    riscv64-unknown-linux-gnu-gdb-add-index  riscv64-unknown-linux-gnu-size
riscv64-unknown-linux-gnu-cpp        riscv64-unknown-linux-gnu-gfortran       riscv64-unknown-linux-gnu-strings
riscv64-unknown-linux-gnu-elfedit    riscv64-unknown-linux-gnu-gprof          riscv64-unknown-linux-gnu-strip
riscv64-unknown-linux-gnu-g++        riscv64-unknown-linux-gnu-ld             ruyi-activate
riscv64-unknown-linux-gnu-gcc        riscv64-unknown-linux-gnu-ld.bfd         ruyi-qemu
riscv64-unknown-linux-gnu-gcc-ar     riscv64-unknown-linux-gnu-ldd
riscv64-unknown-linux-gnu-gcc-nm     riscv64-unknown-linux-gnu-lto-dump
```

其中包含了全部的工具链命令，被重命名为 ``ruyi-qemu`` 的 QEMU 用户态模拟器，以及用于进入虚拟环境的 ``ruyi-activate`` 脚本。其中工具链命令实际上是指向 ruyi 的软链接。

``source`` ruyi-activate 脚本即可激活构建环境， ``PS1`` 将被修改：

```bash input="1"
$ source ./myhone-venv/bin/ruyi-activate
«Ruyi myhone-venv» $
```

同时 ``PATH`` 也被修改，现在可以直接调用工具链了：

```bash input="1,3"
«Ruyi myhone-venv» $ whereis riscv64-unknown-linux-gnu-gcc
riscv64-unknown-linux-gnu-gcc: /home/myhone/hello-ruyi/myhone-venv/bin/riscv64-unknown-linux-gnu-gcc
«Ruyi myhone-venv» $ riscv64-unknown-linux-gnu-gcc --version
riscv64-unknown-linux-gnu-gcc (RuyiSDK 20260201 Upstream-Sources ) 15.2.0
Copyright (C) 2025 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

在 Ruyi 虚拟环境下，除了会自动向工具链传入配置的架构选项外，工具链和模拟器的使用并没有太大的区别。


## 依赖与注意事项

- `venv` 仅负责创建编译环境，不保证所选组合一定可用。若出现问题，请检查工具链包支持的 `flavor(s)` 以及预置配置对目标平台的兼容性。
- 若需要运行交叉编译后的二进制，可在虚拟环境中同时加入相应的 QEMU 用户态模拟器，例如 `qemu-user-riscv-upstream`。

更多工具链和配置组合，请参考对应的 Ruyi 软件包说明和支持矩阵。
