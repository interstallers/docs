# TKEStack安装部署指南



##  产品架构

### 总体架构

TKEStack产品架构如下图所示：
![](https://github.com/tkestack/tke/blob/master/docs/images/TKEStackHighLevelArchitecture@2x.png?raw=true)



### 架构说明

TKEStack采用了Kubernetes on Kubernetes架构设计理念，即用一个Kubernetes集群作为控制台管理Kubernetes业务集群。



### 模块说明

●    Installer: 运行tke-installer容器，提供Web UI指导用户在Global集群部署TKEStacl控制台；

●    Global Cluster: 运行的TKEStack控制台的Kubernetes集群；

●    Cluster: 运行业务的Kubernetes集群，可以通过TKEStack控制台创建或导入；

●    Dashboard: 运行TKEStack控制台web服务；

●    Auth: 权限认证组件，提供用户鉴权、权限对接相关功能；

●    Gateway: 实现集群后台统一入口、统一鉴权相关的功能；

●    Platform: 集群管理组件，提供global集群管理多个业务集群相关功能；

●    Business: 业务管理组件，提供平台业务管理相关功能的后台服务；

●    Network Controller：网络服务组件，支撑Galaxy网络功能；

●    Monitor: 监控服务组件，提供监控采集、上报、告警相关服务；

●    Notify: 通知功能组件，提供消息通知相关的功能；

●    Registry: 镜像服务组件，提供平台镜像仓库服务；





##  部署环境要求

### 硬件要求

#### **● 最小化部署配置：**

​		**Installer 节点：**CPU：2核，内存：4G ，系统盘：50G，1台

​		**Global 集群：**CPU：8核，内存：16G ，系统盘：100G，1台

​		**业务集群：**

​				Master & etcd 节点：CPU：4核，内存：8G ，系统盘：100G，1台

​				Node 节点：CPU：8核，内存：16G ，系统盘：100G，3台

#### **● 推荐配置：**

​		**Installer 节点：**CPU：2核，内存：4G ，系统盘：50G，1台

​		**Global 集群：**CPU：16核，内存：32G ，系统盘：100G，3台

​		**业务集群：**

​				Master & etcd 节点：CPU：8核，内存：16G ，系统盘：50G，数据盘：100G（/var/lib/docker），3台

​				Node 节点：CPU：16核，内存：32G ，系统盘：100G，数据盘：100G（/var/lib/docker），>3台（基于业务量添加）



### 软件要求

**●   操作系统：**centos 7.2+(推荐centos 7.5)、ubuntu14.04+（推荐ubuntu16.04）、tlinux2.2(操作系统请选择标准安装版)

**●    端口：**所有节点防火墙必须放通放通SSH（默认22）、80、8080、443、6443 端口。确保 Installer 节点及其容器、Global 集群节点及其容器、业务集群节点及其容器之间能够 ssh 互联。



##  安装步骤

### 1.安装tke-installer

#### 在线环境：

登录到您的Linux主机，然后通过以下命令安装tke-installer：

```
version=v1.1.0 && wget https://tke-release-1251707795.cos.ap-guangzhou.myqcloud.com/tke-installer-x86_64-$version.run{,.sha256} && sha256sum --check --status tke-installer-x86_64-$version.run.sha256 && chmod +x tke-installer-x86_64-$version.run && ./tke-installer-x86_64-$version.run
```

注意：此命令可以在[TKEStack版本详情](https://github.com/tkestack/tke/releases)中找到，您可以按需选择版本进行安装，我们建议您安装最新版本。

注意：tke-installer约为3GB，包含安装所需的所有资源。



#### 离线环境：

1.在一台可以联网的主机上通过以下命令下载tke-installer安装包：

```
 version=v1.1.0 && wget https://tke-release-1251707795.cos.ap-guangzhou.myqcloud.com/tke-installer-x86_64-$version.run{,.sha256}
```

2.将安装包使用 rz 或 scp 工具传入离线目标主机。

3.在离线目标主机通过以下命令安装tke-installer：

```
 version=v1.1.0 && sha256sum --check --status tke-installer-x86_64-$version.run.sha256 && chmod +x tke-installer-x86_64-$version.run && ./tke-installer-x86_64-$version.run
```



### 2.安装TKEStack控制台

1.**基本设置**：

配置TKEStack控制台的账户信息及APIServer高可用类型。

注：高可用设置为”TKE提供“，会为您默认在master节点额外安装Keepalived完成VIP配置。高可用设置设置为”使用已有“需要您手动对接配置好的VIP实例。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-1.png?raw=true)



2.**集群设置**：

填写Global集群的信息，包括网卡、节点GPU类型、容器网络、master节点信息。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-2.png?raw=true)

高级设置处可以自定义Global集群的Docker、kube-apiserver、kube-controller-manager、kube-scheduler、kubelet运行参数。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-3-2.png?raw=true)



3.**认证设置**：

填写TKEStack控制台的认证信息，可以选择使用TKE提供的认证方式或对接OIDC认证授权系统。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-3-1.png?raw=true)



4.**镜像仓库设置**：

配置TKEStack的镜像仓库，它是集群初始化的源镜像仓库，支持本地和远程仓库。

注：如果选择第三方仓库TKE将不会安装镜像仓库服务，使用您提供的镜像仓库作为默认镜像仓库服务。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-4.png?raw=true)



5.**业务设置**：

确认是否开启业务模块，建议默认开启。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-5.png?raw=true)



6.**监控设置**：

选择监控的存储类型，可选TKE提供的influxDB，或者选择对接外部influxDB、ES。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-6.png?raw=true)



7.**控制台设置**：

确认是否开启集群控制台，确认开启则填写控制台的已备案域名并填写服务器证书。

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-7.png?raw=true)



8.**配置预览**：

确认配置是否正确

![](https://github.com/interstallers/docs/blob/master/Images/Installration/step-8.png?raw=true)