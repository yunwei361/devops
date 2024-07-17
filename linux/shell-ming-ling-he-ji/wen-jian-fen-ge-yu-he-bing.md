# 文件分割与合并

部分环境由于各种限制，无法上传超过指定大小的文件，于是就需要将整个大文件分割成数个较小的文件，进行上传后再合并，就可以解决文件上传大小的限制

```bash
# 文件分割
split -b 5M -d -a 2 FileZilla_win64-setup.zip FileZilla_win64-setup.zip.
 
# -b： 选项后跟期望切割后的单个文件的大小
# -d： 使用数字作为后缀
# -a： 配合选项-d，指定后缀长度
# -l： 选项后行数（此参数可用于文本文件按行数分割）。如按分割后100行存储为一个文件进行分割：split -l 100 -d -a 2 result.log result.log.
 
 
# 文件合并
cat FileZilla_win64-setup.zip.* > FileZilla_win64-setup_ok.zip
```

<figure><img src="../../.gitbook/assets/image (16).png" alt=""><figcaption></figcaption></figure>
