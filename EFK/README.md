# kubernetes1.6.0安装EFK

注意：这里用的是阿里云镜像仓库，建议pull下镜像后推送到本地镜像仓库并修改yaml文件中镜像地址，否则集群中每台机器都需要pull镜像。镜像地址自行在文件中搜索。



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