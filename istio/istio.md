# istio

## 安装

参考官网：https://istio.io/latest/zh/docs/setup/getting-started/

### 下载

转到 [Istio 发布](https://github.com/istio/istio/releases/tag/1.16.2) 页面，下载针对您操作系统的安装文件， 或用自动化工具下载并提取最新版本（Linux 或 macOS）

1. `curl -L https://istio.io/downloadIstio | sh -`

转到 Istio 包目录。例如，如果包是 `istio-1.16.2`：

2. `cd istio-1.16.2`

将 `istioctl` 客户端添加到路径（Linux 或 macOS）

3. `export PATH=$PWD/bin:$PATH`

> 如果curl下载失败可以去`https://github.com/istio/istio/releases`下载之后上传到服务器解压使用
>
> `tar -zxvf file-name`

### 安装

1. `istioctl install --set profile=demo -y`

给命名空间添加标签，指示 Istio 在部署应用的时候，自动注入 Envoy 边车代理：

2. `kubectl label namespace default istio-injection=enabled`