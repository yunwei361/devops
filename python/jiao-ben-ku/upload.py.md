# upload.py

### upload.py

{% hint style="info" %}
此脚本用于上传文件到阿里云OSS中，使用测试环境为 CentOS 7&#x20;
{% endhint %}

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
from __future__ import print_function
import os, sys
import oss2
from oss2.credentials import EnvironmentVariableCredentialsProvider

# 使用环境变量中获取的RAM用户的访问密钥配置访问凭证。
auth = oss2.ProviderAuth(EnvironmentVariableCredentialsProvider())
# 地域 + Bucket名称
bucket = oss2.Bucket(auth, 'https://oss-cn-chengdu.aliyuncs.com', 'yunwei361')

def percentage(consumed_bytes, total_bytes):
    """进度条回调函数，计算当前完成的百分比

    :param consumed_bytes: 已经上传/下载的数据量
    :param total_bytes: 总数据量
    """
    if total_bytes:
        rate = int(100 * (float(consumed_bytes) / float(total_bytes)))
        print('\rupload: {0}% '.format(rate), end='')
        sys.stdout.flush()

# 待上传文件的本地路径
upload_file_path = sys.argv[1]
# 待上传文件的文件名
upload_file_name = os.path.basename(upload_file_path)

# 填写Object完整路径和本地文件的完整路径。Object完整路径中不能包含Bucket名称。
with open(upload_file_path, 'rb') as fileobj:
    bucket.put_object('upload/' + upload_file_name, fileobj, progress_callback=percentage)

# 文件上传后，修改OSS里的文件权限为公共读
bucket.put_object_acl('upload/' + upload_file_name, oss2.OBJECT_ACL_PUBLIC_READ)

# 打印下载地址
print("\nhttps://yunwei361.oss-cn-chengdu.aliyuncs.com/upload/" + upload_file_name)
```

```bash
chmod +x ./upload.py
mv ./upload.py /usr/bin/upload

# 使用示例：
upload /tmp/test_file
```

#### 前置设置

```bash
# 安装依赖
yum install python3-devel -y
pip3 install oss2

# 验证安装是否成功
python3
> import oss2
> oss2.__version__                         

# 配置OSS KEY
cat >> /etc/profile << EOF
export OSS_ACCESS_KEY_ID=uTAI5t99wtrTut7Y8DEAsZ5L
export OSS_ACCESS_KEY_SECRET=M61eU2JVin6vUvAUzeWjapDzD2s3Ez
EOF
source /etc/profile

# 进入阿里云OSS后台，创建相关Bucket
```
