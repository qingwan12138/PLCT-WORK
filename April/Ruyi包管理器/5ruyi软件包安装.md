# ruyi软件包安装
ruyi软件包主要由toolchain(工具链)、emulator(模拟器)、board-image(系统镜像)、analyzer(分析工具)和extra(其他)五个部分组成，这些软件包可以使用 install 命令安装。
## 工具链(toolchain)安装
```bash
$ ruyi install gnu-upstream
$ ruyi install toolchain/gnu-upstream
$ ruyi install 'gnu-upstream(0.20231118.0)'
$ ruyi install 'gnu-upstream(>=0.20231118.0)'
```
上述通过指定软件包名安装的方式默认会安装标记为 latest 版本的 gnu-upstream 包，如果想安装某个历史版本的 gnu-upstream，则可以通过指定版本来安装,表达式支持`<`、 `>`、`==`、`<=`、`>=`、`!=` 运算符，并且`ruyi install`命令支持同时安装多个包。
如果错误删除了已安装软件包的文件，则可以重新安装这个软件包来恢复：
```bash
$ ruyi install --reinstall gnu-upstream
```
包管理器下载的包存放在 ~/.cache/ruyi/distfiles/ 下，在 XDG_CACHE_HOME 被指定时为 $XDG_CACHE_HOME/ruyi/distfiles/。这些包通常以已压缩的格式存在，需要调用系统工具解包。在系统中缺少对应的工具时将打印相应的警告。

install 命令默认情况下只会安装和本机架构相同的二进制包，解包后的二进制包存放在 ~/.local/share/ruyi/binaries/$(uname -m)/ 目录下，在 XDG_DATA_HOME 被指定时为 $XDG_DATA_HOME/ruyi/binaries/$(uname -m)/。

由于系统镜像也是二进制文件，也可以使用 install 命令执行下载和解包。不过通常情况下这些包配合 Ruyi 的镜像刷写功能使用。解包后的镜像文件存放在 ~/.local/share/ruyi/blobs/ 目录下，在 XDG_DATA_HOME 被指定时为 $XDG_DATA_HOME/ruyi/blobs/。
## 源码包(source)安装
源码包在 ruyi list中表示为`source/源码包`,可以使用 extract 命令下载一个源码包并解包。默认情况下,extract 会将所请求的软件包内容解压到以软件包名、版本命名的独立目录下:
```bash
$ ruyi extract ruyisdk-demo
$ cd ruyisdk-demo-0.20231114.0
$ ls
README.md  rvv-autovec
```
如果您需要直接解压到当前工作目录(先前版本的默认行为),可以使用`--extract-without-subdir` 选项
```bash
$ ruyi extract --extract-without-subdir ruyisdk-demo
$ ls
README.md  rvv-autovec
```
如果您需要将内容解压到其他目录,可以使用`--dest-dir` 或 `-d`选项指定目标目录:
```bash
$ ruyi extract -d /path/to/destination ruyisdk-demo
```
如果您只需要下载源码包但不解压,可以使用 `--fetch-only` 或 `-f` 选项:
```bash
$ ruyi extract -f ruyisdk-demo
```
`extract` 命令支持和 `install` 相同的版本表达方式。
## 卸载软件包
您可以使用 ruyi uninstall/remove/rm 命令卸载已安装的软件包:
```bash
$ ruyi uninstall gnu-upstream
$ ruyi remove gnu-upstream
$ ruyi rm gnu-upstream
```
如果您需要删除所有已经下载和安装的软件包，可以使用如下命令:
```bash
$ ruyi self clean --distfiles --installed-pkgs
```
注意，如果某个工具链软件包被删除，已经建立的依赖该包的虚拟环境将失效，且激活该编译环境时并不会有相关警告。

