# April  2026 
## Issues
## eclipse
### [Issue 154](https://github.com/ruyisdk/ruyisdk-eclipse-plugins/issues/154)
- eclipse中版本检测中升级ruyi包管理器，显示由于不支持utf8报错

### [Issue 153](https://github.com/ruyisdk/ruyisdk-eclipse-plugins/issues/153)
- Ruyi-venv名称可以默认设置为ruyi-venv-profile属性

### [Issue 152](https://github.com/ruyisdk/ruyisdk-eclipse-plugins/issues/152)
- Ruyi-venv中的vnev path需要手动点击右侧列表，显示项目路径

### [Issue 151](https://github.com/ruyisdk/ruyisdk-eclipse-plugins/issues/151)
- Ruyi Venv 没有显示虚拟环境名称，具体名称只会在sysroot路径列表出现

### [Issue 83](https://github.com/ruyisdk/ruyisdk-eclipse-plugins/issues/83)
- name和id字节可以正常的顺序或者逆序排列，但是针对variants字节的顺序和逆序排列方式还是乱的

反馈Issue[Profiles 列表解析中文 #109](https://github.com/ruyisdk/ruyisdk-eclipse-plugins/issues/109)，[虚拟环境删除后项目目录更新 #99](https://github.com/ruyisdk/ruyisdk-eclipse-plugins/issues/99)等

## vscode
###[Issue 144](https://github.com/ruyisdk/ruyisdk-vscode-extension/issues/144)
- Ruyi-venv创建过程中配置sysroot选项中输入非法字符仍可正常配置sysroot

## PR
eclipse v0.1.3报告[PR 7](https://github.com/ruyisdk-test/ruyisdk-eclipse-plugins-test/pull/7)
### 对ruyisdk-eclipse-plugins-0.1.3的测试用例报告
#### 主要内容
修复内容
- 创建虚拟环境时 profile 列表的排序
- 创建虚拟环境时 不再因中文系统报错
- 虚拟环境删除后项目目录更新

目前存在的bug
- 开发板选择框variants字节顺序排序意义不明
- 包在下载完成前还是关闭，并重新打开，导致下载失败
- 创建虚拟环境时选择的路径不在项目目录下无法使用
- 在白色主题下，Unread Only 勾选不明显 

vscode v0.1.3-beta[PR 9](https://github.com/ruyisdk-test/ruyisdk-vscode-extension-test/pull/9)
### 对ruyisdk-vscode-extension-0.1.3-beta的测试用例报告
#### 主要内容
修复内容
- 修复ruyi版本更新冲突
- 修复了enter默认状态下无法配置sysroot

目前存在的bug
- build project 按钮会调用宿主机工具链，编译架构为宿主机架构（v0.1.3-beta并未合并修复的pr，后续测试）
- sysroot配置中输入非法字符仍可正常配置