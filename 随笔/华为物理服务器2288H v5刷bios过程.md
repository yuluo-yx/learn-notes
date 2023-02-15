# 华为物理服务器2288H v5刷bios过程

## 利用ftp将bios的升级包上传到服务器的`/tmp`路径下

![image-20220322182747805](E:\Typora笔记\java framework\mybatisplus\images\image-20220322182747805.png)

## 利用远程工具连接服务器

```shell
# 执行命令
ipmcset -t maintenance -d upgradebios -v /tmp/biosimage.hpm

# 等待执行完成即可
```

## 重启系统即可

