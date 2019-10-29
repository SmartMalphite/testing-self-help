# Mac OS下安装Docker&k8s

### Step1 安装 docker 

[https://hub.docker.com/editions/community/docker-ce-desktop-mac](https://hub.docker.com/editions/community/docker-ce-desktop-mac) 下载最新版的docker安装包下载成功后安装，打开主界面，启用k8s![](//note.youdao.com/src/1C85B9F17DF04E39B2A84BCBF17470FC) 

![](../../.gitbook/assets/image%20%288%29.png)

稍等一段时间后右下角显示Kubernets is running 即安装并启动成功

### Step2 安装Dashboard 

Dashboard的github仓库地址 ：[https://github.com/kubernetes/dashboard](https://github.com/kubernetes/dashboard) · 

```text
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
kubectl proxy

#打开dashboard
http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/#!/login
```

![](../../.gitbook/assets/image%20%285%29.png)

### Step3 生成token

需要创建一个 admin 用户并授予 admin 角色绑定，使用下面的 yaml 文件创建 admin 用户并赋予他管理员权限，然后可以通过 token 登陆 dashbaord，该文件见 [admin-role.yaml](https://github.com/rootsongjc/kubernetes-handbook/tree/master/manifests/dashboard-1.7.1/admin-role.yaml)

```text
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: admin
  annotations:
    rbac.authorization.kubernetes.io/autoupdate: "true"
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
subjects:
- kind: ServiceAccount
  name: admin
  namespace: kube-system
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin
  namespace: kube-system
  labels:
    kubernetes.io/cluster-service: "true"
    addonmanager.kubernetes.io/mode: Reconcile
```

然后执行下面的命令创建 serviceaccount 和角色绑定，对于其他命名空间的其他用户只要修改上述 yaml 中的 name 和 namespace 字段即可：

```text
kubectl create -f admin-role.yaml
```

创建完成后获取 secret 和 token 的值：

```text
# 获取admin-token的secret名字
$ kubectl -n kube-system get secret|grep admin-token
admin-token-nwphb                          kubernetes.io/service-account-token   3         6m

# 获取token的值
$ kubectl -n kube-system describe secret admin-token-nwphb
Name:		admin-token-nwphb
Namespace:	kube-system
Labels:		<none>
Annotations:	kubernetes.io/service-account.name=admin
		kubernetes.io/service-account.uid=f37bd044-bfb3-11e7-87c0-f4e9d49f8ed0

Type:	kubernetes.io/service-account-token

Data
====
namespace:	11 bytes
token:		非常长的字符串
ca.crt:		1310 bytes
```

在 dashboard 登录页面上使用上面输出中的那个非常长的字符串进行 base64 解码后作为 token 登录

