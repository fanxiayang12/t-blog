# 命令
## 参考
[官方文档](https://kubernetes.io/docs/reference/kubectl/overview/)
# K8s 安装过程

## 准备工作
```
yum install -y curl
curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

# yum install -y wget
# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

yum clean all
yum makecache
```

## 关闭防火墙
```
systemctl stop firewalld
systemctl disable firewalld
```

## 关闭selinux
```
sed -i 's/enforcing/disabled/' /etc/selinux/config

# setenforce 0
# reboot
```

## 关闭swap分区
```
sed -ri 's/.*swap.*/#&/' /etc/fstab
# swapoff -a
```

## 主机名

### Master-163 上执行
```
hostnamectl set-hostname k8s-master-163
```

### Node-164 上执行
```
hostnamectl set-hostname k8s-node-164
```

### Node-165 上执行
```
hostnamectl set-hostname k8s-node-165
```

### 每个节点上执行
```
cat >> /etc/hosts << EOF
10.10.10.163 k8s-master-163
10.10.10.164 k8s-node-164
10.10.10.165 k8s-node-165
EOF
```

## 将桥接的IPv4流量传递到iptables的链
```
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
vm.swappiness = 0
EOF
```
```
# 加载br_netfilter模块
modprobe br_netfilter
# 查看是否加载
lsmod | grep br_netfilter
# 生效
sysctl --system 
```

## 时间同步
```
yum install ntpdate -y
ntpdate time.windows.com
```

## 开启ipvs
### •	在每个节点安装ipset和ipvsadm
```
yum -y install ipset ipvsadm
```
### •	在所有节点执行如下脚本
```
cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack_ipv4
EOF
```
### •	授权、运行、检查是否加载：
```
chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack_ipv4
```
### •	检查是否加载
```
lsmod | grep -e ipvs -e nf_conntrack_ipv4
```

## 所有节点安装Docker/kubeadm/kubelet/kubectl
### •	安装Docker
```
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo

yum -y install docker-ce-20.10.7
# yum -y install docker-ce
systemctl enable docker && systemctl start docker
docker version
```
### •	设置Docker镜像加速器
```
sudo mkdir -p /etc/docker

sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "exec-opts": ["native.cgroupdriver=systemd"],	
  "registry-mirrors": ["https://b9pmyelo.mirror.aliyuncs.com"]
}
EOF

sudo systemctl daemon-reload
sudo systemctl restart docker
```
### 添加阿里云的YUM软件源
```
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```

## 安装kubeadm、kubelet和kubectl
```
yum install -y kubelet-1.21.2 kubeadm-1.21.2 kubectl-1.21.2
# yum install -y kubelet kubeadm kubectl
```
```
vi /etc/sysconfig/kubelet
# 修改
KUBELET_EXTRA_ARGS="--cgroup-driver=systemd"
```

## 部署k8s的Master节点
### kubeadm init 
```
kubeadm init \
  --apiserver-advertise-address=10.10.10.163 \
  --image-repository registry.aliyuncs.com/google_containers \
  --kubernetes-version v1.21.2 \
  --service-cidr=10.96.0.0/12 \
  --pod-network-cidr=10.244.0.0/16
```
### kube config
```
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config
```
## 添加k8s的Node节点
### 在Master上执行
```
kubeadm token create --print-join-command
# 生成一个永不过期的token
# kubeadm token create --ttl 0
```
### 在所有节点执行
```
# 就是Master上一个命令执行的结果
kubeadm join 10.10.10.163:6443 --token XXXXXXXXXX
```
## 部署CNI网络插件
### •	在Master节点部署CNI网络插件
```
kubectl get nodes
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```
### •	查看部署CNI网络插件进度
```
kubectl get pods -n kube-system
kubectl get nodes
kubectl get cs
kubectl cluster-info
```


