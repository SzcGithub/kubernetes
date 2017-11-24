# kubernetes1.6.0安装EFK

注意1：这里用的是阿里云镜像仓库，建议pull下镜像后推送到本地镜像仓库并修改yaml文件中镜像地址，否则集群中每台机器都需要pull镜像。镜像地址自行在文件中搜索。

注意2：安装EFK前需要先安装DNS，否则会出现elasticsearch-logging:9200无法连接的状态。



步骤1：给nodes设置标签

kubectl get nodes  #查看node的名称

kubectl label nodes <node的名称> beta.kubernetes.io/fluentd-ds-ready=true  #设置标签

步骤2：进入yaml文件所在目录执行命令

kubectl create -f .

步骤3：查看pods状态，是否running状态。

kubectl get pods --namespace= kube-system

步骤4：创建代理访问kibana

nohup kubectl proxy --address='主机IP' --port=8086 --accep-hosts='^*$' &

步骤5：浏览器访问kibana

http://主机IP:8086/api/v1/proxy/namespaces/kube-system/services/kibana-logging/app/kibana



## 遇到的问题

问题1：浏览器访问kibana后create按钮灰色

解决办法：修改docker日志，所有的docker日志都journal到系统日志 /var/log/messages下了. 因为经常有人说docker日志太多导致container容器增长比较快，所以都通过系统的journal进行统一处理。

修改/etc/sysconfig/docker配置文件，把原来的journal改回到当前json.log方式

~~~
#OPTIONS='--selinux-enabled --log-driver=journald --signature-verification=false  --insecure-registry 192.168.30.11:5000'
OPTIONS='--selinux-enabled --log-driver=json-file --signature-verification=false  --insecure-registry 192.168.30.11:5000'

~~~

