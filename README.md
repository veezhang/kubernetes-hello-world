# kubernetes hello world

## kubernetes 简介
* kubernetes 架构
![kubernetes 架构](/resources/architecture.jpg)
* kubernetes Master
![kubernetes Master](/resources/master.jpg)
    * Master 组件提供的集群控制。Master 组件对集群做出全局性决策(例如：调度)，以及检测和响应集群事件(副本控制器的replicas字段不满足时,启动新的副本)。
    * Master 组件：
        * API服务器
            * kube-apiserver对外暴露了Kubernetes API。它是的 Kubernetes 前端控制层。
        * etcd
            * etcd 用于 Kubernetes 的后端存储。所有集群数据都存储在此处，始终为您的 Kubernetes 集群的 etcd 数据提供备份计划。
        * kube-controller-manager
            * kube-controller-manager运行控制器，它们是处理集群中常规任务的后台线程。逻辑上，每个控制器是一个单独的进程，但为了降低复杂性，它们都被编译成独立的可执行文件，并在单个进程中运行。
            这些控制器包括:
                * 节点控制器: 当节点移除时，负责注意和响应。
                * 副本控制器: 负责维护系统中每个副本控制器对象正确数量的 Pod。
                * 端点控制器: 填充 端点(Endpoints) 对象(即连接 Services & Pods)。
                * 服务帐户和令牌控制器: 为新的命名空间创建默认帐户和 API 访问令牌.
        * cloud-controller-manager
            * cloud-controller-manager 是用于与底层云提供商交互的控制器。仅运行云提供商特定的控制器循环。
                * 节点控制器: 用于检查云提供商以确定节点是否在云中停止响应后被删除
                * 路由控制器: 用于在底层云基础架构中设置路由
                * 服务控制器: 用于创建，更新和删除云提供商负载平衡器
                * 数据卷控制器: 用于创建，附加和装载卷，并与云提供商进行交互以协调卷
        * kube-scheduler
            * kube-scheduler监视没有分配节点的新创建的 Pod，选择一个节点供他们运行。
* kubernetes Node
![kubernetes Node](/resources/node.jpg)
    * Node 组件在每个节点上运行，维护运行的 Pod 并提供 Kubernetes 运行时环境。
    * Node 组件：
        * kubelet
            *kubelet是主要的节点代理,它监测已分配给其节点的 Pod(通过 apiserver 或通过本地配置文件)，提供如下功能:
                * 挂载 Pod 所需要的数据卷(Volume)。
                * 下载 Pod 的 secrets。
                * 通过 Docker 运行(或通过 rkt)运行 Pod 的容器。
                * 周期性的对容器生命周期进行探测。
                * 如果需要，通过创建 镜像 Pod（Mirror Pod） 将 Pod 的状态报告回系统的其余部分。
                * 将节点的状态报告回系统的其余部分。
        * kube-proxy
            * kube-proxy通过维护主机上的网络规则并执行连接转发，实现了Kubernetes服务抽象。
        * CRI(Container Runtime Interface)
            * 使得kubernetes更好的扩展以及整合容器运行时
                * Docker
                * Containerd
                * CRI-O
        * CNI(Container Network Interface)
            * 使得kubernetes网络互通
                * calico
                * canal
                * cilium
                * flannel
                * kube-router
                * weave

* kubernetes 资源对象
    * Pod
        * Pod 是一组紧密关联的容器集合，它们共享 PID、IPC、Network 和 UTS namespace，是Kubernetes 调度的基本单位。Pod 的设计理念是支持多个容器在一个 Pod 中共享网络和文件系统，可以通过进程间通信和文件共享这种简单高效的方式组合完成服务。
    * Label
        * Label 是识别 Kubernetes 对象的标签，以 key/value 的方式附加到对象上，可以使用 Label Selector 来选择一组相同 label 的对象。
    * Namespace
        * Namespace 是对一组资源和对象的抽象集合，比如可以用来将系统内部的对象划分为不同的项目组或用户组。
    * ReplicationController
        * 它的主要作用是确保Pod以你指定的副本数运行，即如果有容器异常退出，会自动创建新的 Pod 来替代；而异常多出来的容器也会自动回收。可以说，通过 ReplicationController，Kubernetes实现了集群的高可用性。
    * Deployment
        * ReplicaSet 是 ReplicationController 的代替物，因此用法基本相同，唯一的区别在于 ReplicaSet 支持集合式的 selector。虽然也 ReplicaSet 可以独立使用，但建议使用 Deployment 来自动管理 ReplicaSet，这样就无需担心跟其他机制的不兼容问题（比如ReplicaSet 不支持 rolling-update 但 Deployment 支持），并且 Deployment 还支持版本记录、回滚、暂停升级等高级特性。
    * Deployment
        * Deployment 确保任意时间都有指定数量的 Pod“副本”在运行。还支持回滚和滚动升级。
    * StatefulSet
        * StatefulSet 是为了解决有状态服务的问题。
            * 稳定的持久化存储，即 Pod 重新调度后还是能访问到相同的持久化数据，基于 PVC 来实现
            * 稳定的网络标志，即 Pod 重新调度后其 PodName 和 HostName 不变，基于 Headless Service（即没有Cluster IP的Service）来实现
            * 有序部署，有序扩展，即 Pod 是有顺序的，在部署或者扩展的时候要依据定义的顺序依次依次进行（即从0到N-1，在下一个Pod运行之前所有之前的 Pod 必须都是 Running 和 Ready 状态），基于 init containers来实现
            * 有序收缩，有序删除（即从N-1到0）
    * DaemonSet
        * DaemonSet 保证在每个Node上都运行一个容器副本，常用来部署一些集群的日志、监控或者其他系统管理应用。典型的应用包括：
            * 日志收集，比如 fluentd，logstash 等
            * 系统监控，比如 Prometheus Node Exporter，collectd，New Relic agent，Ganglia gmond 等
            * 系统程序，比如 kube-proxy, kube-dns, glusterd, ceph 等
    * Service
        * Service 是应用服务的抽象，通过 labels 为应用提供负载均衡和服务发现。匹配 labels 的Pod IP 和端口列表组成 endpoints，由 kube-proxy 负责将服务 IP 负载均衡到这些endpoints 上。每个 Service 都会自动分配一个 cluster IP（仅在集群内部可访问的虚拟地址）和 DNS 名，其他容器可以通过该地址或 DNS 来访问服务，而不需要了解后端容器的运行。Service有四种类型：
            * ClusterIP：默认类型，自动分配一个仅cluster内部可以访问的虚拟IP
            * NodePort：在ClusterIP基础上为Service在每台机器上绑定一个端口，这样就可以通过 <NodeIP>:NodePort 来访问该服务
            * LoadBalancer：在NodePort的基础上，借助cloud provider创建一个外部的负载均衡器，并将请求转发到 <NodeIP>:NodePort
            * ExternalName：将服务通过DNS CNAME记录方式转发到指定的域名（通过 spec.externlName 设定）。需要kube-dns版本在1.7以上。
    * Endpoints
        * Endpoints是实现实际服务的端点集合。
    * Job
        * Job 负责批量处理短暂的一次性任务 (short lived one-off tasks)，即仅执行一次的任务，它保证批处理任务的一个或多个Pod成功结束。
    * CronJob
        * CronJob 即定时任务，就类似于 Linux 系统的 crontab，在指定的时间周期运行指定的任务。
    * Horizontal Pod Autoscaler（HPA）
        * Horizontal Pod Autoscaling 可以根据 CPU 使用率或应用自定义 metrics 自动扩展 Pod 数量（支持replication controller、deployment和replica set），从而合理的扩展性能与使用资源。
    * PersistentVolume（PV） 持久卷，简称PV
        * GCEPersistentDisk
        * AWSElasticBlockStore
        * AzureFile
        * AzureDisk
        * CSI
        * FC (Fibre Channel)
        * Flexvolume
        * Flocker
        * NFS
        * iSCSI
        * RBD (Ceph Block Device)
        * CephFS
        * Cinder (OpenStack block storage)
        * Glusterfs
        * VsphereVolume
        * Quobyte Volumes
        * HostPath (Single node testing only – local storage is not supported in any way and WILL NOT WORK in a multi-node cluster)
        * Portworx Volumes
        * ScaleIO Volumes
        * StorageOS
    * PersistentVolumeClaim（PVC） 持久卷申请，简称PVC
    * StorageClass 存储类，自动生成pv,pvc

## install microk8s (单机版本 kubernetes)   
```shell
# 开启转发
sudo bash -c "echo net.ipv4.ip_forward=1 >> /etc/sysctl.conf"
sudo sysctl -p
sudo iptables -P FORWARD ACCEPT

# 安装microk8s （修改了中国区不能拉取镜像导致启动不了的问题）
# sudo snap install --classic --channel=1.14/stable microk8s #能够访问国外镜像可以使用这个安装, --channel=1.14/stable可以不要这个参数安装最新版
sudo snap install --classic --dangerous ./snaps/microk8s_v1.14.1_amd64.snap

# 等待microk8s启动 microk8s is running
microk8s.status

# 启动dns, storage
microk8s.enable dns storage

# 设置配置
microk8s.kubectl config view --raw > $HOME/.kube/config
echo 'alias kubectl=microk8s.kubectl' >> ~/.bashrc
alias kubectl=microk8s.kubectl

# 等待都启动 Running， READY列前后数字相等
kubectl get pod --all-namespaces
```

### 按照如下例子操作
* [01-pod](/manifests/01-pod)
* [02-deployment](/manifests/02-deployment)
* [03-service](/manifests/03-service)
* [04-statefulset](/manifests/04-statefulset)
* [99-final](/manifests/99-final)

### Q&A

### 至此，本次介绍完成！ 谢谢！