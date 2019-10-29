# k8s中搭建jenkins服务

> 安装完kubernets后，不可避免的要在集群中安装一些日常所需要的软件和服务。其中对于运维来说，jenkins是经常使用的一个工具，这里，介绍一下如何在k8s中安装jenkins工具。这也是为将来为jinkins+k8s的ci/cd流程做一个基础的架构环境。

### Step 1

```text
k8s-node2#docker pull jenkins/jenkins
```

### Step 2

```text
# more jenkins.yaml 
#-----Deployment----------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jenkins
  labels: 
    app: jenkins
spec:
  replicas: 1                #副本数为1
  selector:
    matchLabels:
      app: jenkins
  template:
    metadata:
      labels:
        app: jenkins
    spec:
      containers:
      - name: jenkins
        image: docker.io/jenkins/jenkins:latest
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 8080
      volumes:
      - name: data
        hostPath:
         path: /Users/enbowang/docker_data
---

#------service---------------
apiVersion: v1
kind: Service
metadata:
  name: jenkins
  labels:
    name: jenkins
spec:
  type: NodePort
  ports:
  - name: jenkins
    port: 8080 
    targetPort: 8080
    nodePort: 30009         #开启nodeport
  - name: jenkins-agent
    port: 50000 
    targetPort: 50000
    nodePort: 30010
  selector:
    app: jenkins
```

### Step 3

```text
k8s-node1# kubectl create -f ./jenkins.yaml
```

创建完成之后，可用看到k8s中已有jenkins相关的pod，以及service

```text
# kubectl get pods 
NAME                       READY     STATUS    RESTARTS   AGE
jenkins-59cd98fc55-74qlv   1/1       Running   0          2h

# kubectl get service
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                          AGE
jenkins      NodePort    10.104.15.194   <none>        8080:30009/TCP,50000:30010/TCP   2h
```

登陆jenkins，由于我们用的是nodeport模式，在每个k8s nodes上都会开放jenkins的访问端口30009，这里随便选择一台登陆即可

