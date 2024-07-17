# 上传文件至阿里云 OSS（实现文件中转站）

> 需求：工作中经常需要传输文件，某些场景只支持https链接进行下载，故需要一个下载速度快、文件链接为https、长期稳定的文件中转站。
>
> &#x20;
>
> 实现：经过分析，阿里云 OSS 正好满足；
>
> 此脚本实现上传文件到阿里云OSS（使用阿里云ossutil工具进行上传），并获取文件下载地址；
>
> &#x20;
>
> 使用前需先安装并配置 ossutil 工具
>
> ossutil 参考文档：[https://help.aliyun.com/zh/oss/developer-reference/ossutil-1/](https://help.aliyun.com/zh/oss/developer-reference/ossutil-1/)

#### upload.sh（使用前需先安装配置ossutil，见后方步骤）

```sh
#!/bin/bash

COLOR_MAGENTA="\E[0;35m"
COLOR_RED="\E[0;31m"
COLOR_RESET="\E[0m"

OSS_BUCKET="yunwei361"
OSS_ENDPOINT="oss-cn-chengdu.aliyuncs.com"

OSS_DIR="ws"
LOCAL_FILE_PATH="$1"
OSS_OBJECT_PATH="${OSS_DIR}/$(basename $1)"

# check args
if [ "$#" -ne 1 ]; then
  echo -e "${COLOR_RED}Useage: upload /path/filename${COLOR_RESET}"
  exit 1
fi

if [ ! -f "$1" ]; then
  echo -e "${COLOR_RED}Error: Only support upload a regular file!${COLOR_RESET}"
  exit 2
fi


# use ossutil upload file & set acl public-read
ossutil cp "$LOCAL_FILE_PATH" "oss://$OSS_BUCKET/$OSS_OBJECT_PATH" --force --acl public-read


# check succeed or failed
if [ $? -eq 0 ]; then
  echo -e "\nDownload URL:\n${COLOR_MAGENTA}https://${OSS_BUCKET}.${OSS_ENDPOINT}/${OSS_OBJECT_PATH}${COLOR_RESET}\n"
else
  echo -e "\n${COLOR_RED}:( Upload failed !${COLOR_RESET}\n"
fi
```

赋可执行权限

```bash
cp upload.sh /usr/bin/upload
chmod +x /usr/bin/upload

```

#### install-ossutil.sh（安装 ossutil, 此脚本只适用于x86机器）

```sh
#!/bin/bash

set -e

work_path=/usr/local/ossutil

if [ ! -e $work_path ]; then
  mkdir -p $work_path
fi

version=$(curl -s https://gosspublic.alicdn.com/ossutil/version.txt | awk -F 'v' '{print $NF}')
ossutil_zip=ossutil-v${version}-linux-amd64.zip


download_link=https://gosspublic.alicdn.com/ossutil/${version}/$ossutil_zip

# download
curl -sSL -o $work_path/$ossutil_zip $download_link

# unzip
unzip -q $work_path/$ossutil_zip -d $work_path

# make symbolic link
ln -sf $work_path/ossutil-v${version}-linux-amd64/ossutil /usr/bin/ossutil
```

#### 配置 ossutil

```bash
# https://help.aliyun.com/zh/oss/developer-reference/configure-ossutil
# -e --endpoint
# -i --access-key-id
# -k --access-key-secret

ossutil config \
  -e oss-cn-chengdu.aliyuncs.com \
  -i uTAI5t99wtrTut7Y8DEAsZ5L \
  -k M61eU2JVin6vUvAUzeWjapDzD2s3Ez
```
