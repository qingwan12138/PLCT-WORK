# 虚拟环境

Ruyi 包管理器提供了 `venv` 命令，用于组合工具链、模拟器、sysroot 等组件，快速创建一个用于交叉编译的独立虚拟环境。该虚拟环境可以为不同 RISC-V 开发板或平台准备匹配的编译配置，减少手动配置复杂度。

## 主要作用

- 将工具链、模拟器、目标 sysroot、交叉编译配置等整合到一个目录中。
- 生成可激活的编译环境，支持 `PATH`、`PS1` 和构建工具链的自动设置。
- 使不同项目和不同目标平台的编译环境彼此隔离。
- 提供 `toolchain.cmake`、`meson-cross.ini` 等交叉编译辅助文件。

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

## 虚拟环境目录结构

虚拟环境目录通常包含：

- `bin/`：工具链命令、`ruyi-activate`、`ruyi-deactivate`、`ruyi-qemu` 等。
- `sysroot/`：目标系统的 sysroot。
- `toolchain.cmake`：CMake 交叉编译工具链文件。
- `meson-cross.ini`：Meson 交叉编译配置文件。
- `ruyi-venv.toml`：虚拟环境元数据。

## 依赖与注意事项

- 在创建虚拟环境前，请确保所需的工具链包和模拟器包已通过 `ruyi install` 安装。
- `venv` 仅负责创建编译环境，不保证所选组合一定可用。若出现问题，请检查工具链包支持的 `flavor(s)` 以及预置配置对目标平台的兼容性。
- 若需要运行交叉编译后的二进制，可在虚拟环境中同时加入相应的 QEMU 用户态模拟器，例如 `qemu-user-riscv-upstream`。

## 典型组合

下面是一些常见可用组合：

- `gnu-plct + generic`：riscv64 架构 Linux 操作系统。
- `gnu-plct + milkv-duo`：使用 glibc 的 Milkv Duo 系列开发板镜像。
- `gnu-plct + xiangshan-nanhu`：香山南湖开发板。
- `gnu-upstream + generic`：riscv64 架构 Linux 操作系统。
- `gnu-plct-xthead + sipeed-lpi4a`：平头哥 C910 及荔枝派 4A。
- `gnu-plct-rv64ilp32-elf + baremetal-rv64ilp32`：rv64ilp32 裸机。

更多工具链和配置组合，请参考对应的 Ruyi 软件包说明和支持矩阵。
