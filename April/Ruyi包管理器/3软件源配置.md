# Ruyi 包管理器配置
## 刷新本地软件包缓存
获取远程软件源的内容并刷新本地软件包缓存，默认使用托管在 GitHub 上的镜像：
```bash
$ ruyi update
```
软件包缓存将存放在用户目录中，通常为 `~/.cache/ruyi/packages-index/` ；在 `XDG_CACHE_HOME` 环境变量被设置时，目录为 `$XDG_CACHE_HOME/ruyi/packages-index/`。
## `ruyi update`拉取失败
由于目前软件包索引信息托管于 GitHub 仓库，若出现仓库访问不稳定的情况，可以使用 `ruyi config` 子命令来配置使用[备用仓库](https://mirror.iscas.ac.cn/git/ruyisdk/packages-index.git)。
```bash
# 查看当前仓库配置
$ ruyi config get repo.remote
# 将远程仓库切换到中国科学院镜像
$ ruyi config set repo.remote https://mirror.iscas.ac.cn/git/ruyisdk/packages-index.git
# （可选）修改默认分支，通常为 main
$ ruyi config set repo.branch main
# （可选）修改本地缓存目录
$ ruyi config set repo.local /your/custom/cache/path
# 配置修改完成后，重新刷新本地软件包缓存
$ ruyi update
```