### Kubernets学习问题总结

#### 一.第一章：Kubernetes入门问题汇总

##### 1. kubenetes无法创建pod/创建RC时无法自动创建pod的问题

![](E:\桌面文档\K8S Mkdocs\Kubernetes-Learn\1524369792(1).jpg)

创建pod:

​	kubectl create -f nginx.yaml

此时有如下报错：

​	Error from server: error when creating "nginx.yaml": Pod "nginx" is forbidden: no API token found for service account default/default, retry after the token is automatically created and added to the service account

解决办法是编辑/etc/kubernetes/apiserver 去除 KUBE_ADMISSION_CONTROL中的SecurityContextDeny,ServiceAccount，并重启kube-apiserver.service服务：

​	vim /etc/kubernetes/apiserver

​	KUBE_ADMISSION_CONTROL="--admission_control=NamespaceLifecycle,NamespaceExists,LimitRanger,ResourceQuota"

​	systemctl restart kube-apiserver.service

之后重新创建pod:

​	kubectl create -f nginx.yaml pods/nginx

#####2.kubernetes无法拉取镜像 

报错一：image pull failed for registry.access.redhat.com/rhel7/pod-infrastructure:latest, this may be because there are no credentials on this request.  details: (open /etc/docker/certs.d/registry.access.redhat.com/redhat-ca.crt: no such file or directory)

解决方案：

yum install *rhsm* -y

修改 vim /etc/docker/daemon.json文件

{
"registry-mirrors":["https://registry.docker-cn.com"]
} 

重启docker服务：

​	systemctl restart docker.service

