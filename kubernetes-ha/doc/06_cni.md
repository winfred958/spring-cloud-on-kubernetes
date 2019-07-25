# 1. CNI
```
kubadmin不涉及网络插件CNI的初始化
因此kubeadm初步安装的机器不具备网络功能
任何pod自带的的coreDNS都无法正常工作
```
# 2. CNI 插件 calico
## 2.1 calico 安装说明
```
网络插件安装需要指定特殊参数
例如 calico 需要指定 --pod-network-cidr
kubeadm init --config=xxx.yaml --pod-network-cidr=192.168.0.0/16
```
```
修改kubnetes服务启动参数, 并重启
	1. 配置master node上, kube-apiserver的启动参数: --allow-privileged=true, (因为calico-node需要以特权形式运行在各node上)
	2. 配置各Node上kubelet服务的启动参数: --network-plugin=cni, (使用CNI网络插件)
```

```
创建Calico服务, 包括 calico-node, calico-policy-controller, 需要创建资源如下
	1. 创建ConfigMap calico-config, 包含calico所需配置参数
	2. 创建Secret calico-etcd-secrets, 用于使用TLS方式连接etcd
	3. 每个Node上都运行calico/node容器, 部署为DaemonSet
	4. 在每个Node上都安装Calico CNI二进制文件和网络篇日志参数(由install-cli完成)
	5. 部署一个名为calico/kube-policy-controller的Deployment,以对接集群中为Pod设置的Network Policy
```

## 2.1 calico 安装步骤
  * [calico 官方 Quickstart](https://docs.projectcalico.org/v3.8/getting-started/kubernetes/)
  * [Installing Calico for policy and networking (recommended)](https://docs.projectcalico.org/v3.8/getting-started/kubernetes/installation/calico)