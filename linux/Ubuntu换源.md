# Ubuntu换源

## ubuntu版本代号

- 22.04：jammy；
- 20.04：focal；
- 18.04：bionic；
- 16.04：xenial；
- 14.04：trusty。

## 直接换

### 20.4 阿里

```markdown
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

# deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

### 20.4 清华

```markdown
# 默认注释了源码镜像以提高 apt update 速度，如有需要可自行取消注释
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-security main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
```

### 20.4 z中科大

```markdown
# 默认注释了源码仓库，如有需要可自行取消注释
deb https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-security main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-updates main restricted universe multiverse

deb https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-backports main restricted universe multiverse

# 预发布软件源，不建议启用
# deb https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multiverse
# deb-src https://mirrors.ustc.edu.cn/ubuntu/ focal-proposed main restricted universe multivers
```



## 换源shell脚本

### 1 

```shell
#!/bin/bash

function change_sources() {
  echo "#======= change sources.list start =======#"
  echo "Input Your Ubuntu Version："
  read UBUNTU_VERSION
  echo "Your Ubuntu Version is ${UBUNTU_VERSION}"
  if [ ${UBUNTU_VERSION} = "12.04" ] || [ ${UBUNTU_VERSION} = "14.04" ] || [ ${UBUNTU_VERSION} = "16.04" ] || [ ${UBUNTU_VERSION} = "18.04" ] || [ ${UBUNTU_VERSION} = "20.10" ] || [ ${UBUNTU_VERSION} = "21.04" ]; then
    cp /etc/apt/sources.list /etc/apt/sources.list.bak
    cp ./apt/${UBUNTU_VERSION}/sources.list /etc/apt/sources.list
    apt-get update && apt-get upgrade
  fi
  echo '#======= change sources.list end =======#'
}

change_sources

```

```shell
git clone https://gitee.com/taoge2021/env-config.git
cd env-config/linux/ubuntu/sources
chmod u+x change-sources.sh
./change-source.sh
```

### 2

```shell
#!/bin/bash

#1.确定系统版本代号
Codename=$(cat /etc/os-release | grep VERSION_CODENAME |awk -F'=' '{print $2}')
echo "检测到您的Ubuntu系统版本为：$Codename"

#2.选择镜像源
echo "**********************************"
echo "请选择镜像源："
echo "
    1.阿里      
    2.清华     
    3.网易
    4.中科大
    "
echo "**********************************"
read -s -n1 sourceChoice

if [ $sourceChoice -ne 1 ] && [ $sourceChoice -ne 2 ] && [ $sourceChoice -ne 3 ] && [ $sourceChoice -ne 4 ];then
    echo
    echo '输入有误，Good Bye.'
    exit
fi

case $sourceChoice in
	1)
		choose='aliyun'
	;;
	2)
		choose='tsinghua'
	;;
	3)
		choose='163'
	;;
	4)
		choose='ustc'
	;;
esac

case $choose in
	aliyun)
		sourceweb='http://mirrors.aliyun.com'
	;;
	tsinghua)
		sourceweb='https://mirrors.tuna.tsinghua.edu.cn'
	;;
	163)
		sourceweb='http://mirrors.163.com'
	;;
	ustc)
		sourceweb='http://mirrors.ustc.edu.cn'
	;;
esac

# 3.备份换源
echo "备份sources.list..."
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
echo "设置新的镜像源..."
echo "\
deb $sourceweb/ubuntu/ $Codename main restricted universe multiverse
deb $sourceweb/ubuntu/ $Codename-security main restricted universe multiverse
deb $sourceweb/ubuntu/ $Codename-updates main restricted universe multiverse
deb $sourceweb/ubuntu/ $Codename-proposed main restricted universe multiverse
deb $sourceweb/ubuntu/ $Codename-backports main restricted universe multiverse
deb-src $sourceweb/ubuntu/ $Codename main restricted universe multiverse
deb-src $sourceweb/ubuntu/ $Codename-security main restricted universe multiverse
deb-src $sourceweb/ubuntu/ $Codename-updates main restricted universe multiverse
deb-src $sourceweb/ubuntu/ $Codename-proposed main restricted universe multiverse
deb-src $sourceweb/ubuntu/ $Codename-backports main restricted universe multiverse">/etc/apt/sources.list
echo "更新源..."
sudo apt-get update
```

## docker容器换源

使用正则替换

```shell
sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
sed -i s@/security.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
apt clean
apt-get update -y
```

