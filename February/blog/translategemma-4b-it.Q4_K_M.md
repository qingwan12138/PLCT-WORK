# **在 OpenEuler RISC-V架构的qemu虚拟机上构建 TranslateGemma-4b-it.Q4_K_M模型**
本文档详细说明了如何在运行RISC-V架构  OpenEuler 系统的 qemu 虚拟机上从源代码构建 TranslateGemma-4b-it.Q4_K_M 。
## 准备
系统版本：`RISC-V 架构 OpenEuler-24.03 LTS SP2`

镜像下载：[OpenEuler-24.03](https://www.openeuler.org/zh/download/#openEuler%2024.03%20LTS%20SP2)

模型：`TranslateGemma-4b-it.Q4_K_M`

## 安装依赖
```bash
$ sudo dnf update
$ sudo yum install -y git make cmake gcc-c++ wget
```

## 下载并编译`llama.cpp`
### 下载`llama.cpp`
```bash
$ cd root/Desktop
$ git clone https://github.com/ggml-org/llama.cpp
$ cd llama.cpp
$ mkdir build && cd build
```
### 编译`llama.cpp`
```bash
$ cmake .. -DGGML_RVV=OFF
$ cmake --build . --config Release -j4
# 其中核心数以实际为准
```
![alt text](image/编译百分百.png)

## 模型下载
### 从huggingface下载TranslateGemma-4b-it.Q4_K_M模型

```bash
$ cd root/Desktop
$ wget  https://huggingface.co/mradermacher/translategemma-4b-it-GGUF/resolve/main/translategemma-4b-it.Q4_K_M.gguf  translategemma-4b-it.Q4_K_M.gguf
```

## 模型运行
```bash
# 编译完成后，可执行文件在 build/bin/ 目录下
$ ./bin/llama-cli -m root/Desktop/translategemma-4b-it.Q4_K_M.gguf
```
![模型成功加载](image/模型成功加载.png)