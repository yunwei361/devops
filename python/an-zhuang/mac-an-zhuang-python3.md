# Mac 安装 Python3

### 卸载旧版 Python3

{% hint style="danger" %}
此方法将删除安装的所有版本 Python3
{% endhint %}

#### 删除 Python3 框架

```
cd /Library/Frameworks/
rm -rf ./Python.framework/
```

#### 删除 Python3 应用目录

```
cd /Applications
sudo rm -rf Python\ 3.10/
```

#### 删除 /usr/local/bin 目录下指向的 Python3 的相关链接：

```
cd /usr/local/bin/
ls -lh /usr/local/bin | grep python3
sudo rm -rf ./python3*
```

#### 删除 python3 的环境路径





### 安装新版 Python3

#### 在官网下载安装包双击一直下一步

[https://www.python.org/downloads/macos/](https://www.python.org/downloads/macos/)

#### 添加环境路径（zsh）

```
vim ~/.zshrc
PATH=/Library/Frameworks/Python.framework/Versions/3.10/bin:$PATH
export PATH

exec zsh
```
