# win10安装Kubernetes

## 1. Docker配置镜像下载源

	{
	  "registry-mirrors": [
		"https://dockerhub.azk8s.cn",
		"https://registry.docker-cn.com"
	  ],
	  "insecure-registries": [],
	  "debug": true,
	  "experimental": true
	}

## 2. 国内需要下载k8s-for-docker-desktop

	git clone https://github.com/AliyunContainerService/k8s-for-docker-desktop.git

## 3. 执行安装脚本

	cd k8s-for-docker-desktop && .\load_images.ps1

Docker内打开Preferences并点击Kubernetes选项卡,勾选Enable Kubernete，勾选Deploy Docker Stacks to Kubernetes by default。

**可能遇到的问题：**

.\load_images.ps1 : 无法加载文件 D:\k8s-for-docker-desktop\load_images.ps1，因为在此系统上禁止运行脚本。有关详细信息，请参阅 https:/go.microsoft.com/fwlink/?LinkID=135170 中的 about_Execution_Policies。
上面报错的原因是系统禁止运行脚本，通过一下命令并输入Y开启此功能：

powershell执行 set-executionpolicy remotesigned 选择Y



## 6. 安装dashboard

- 部署 Kubernetes dashboard
	
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0-rc5/aio/deploy/recommended.yaml
    或
    kubectl create -f D:\develop\k8s-for-docker-desktop-master\kubernetes-dashboard.yaml  

- 检查 kubernetes-dashboard 应用状态

  kubectl get pod -n kubernetes-dashboard

- 开启 API Server 访问代理

	kubectl proxy

- 通过如下 URL 访问 Kubernetes dashboard
	
  http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/

## 7. 配置控制台访问令牌

- powershell执行

        $TOKEN=((kubectl -n kube-system describe secret default | Select-String "token:") -split " +")[1]
        kubectl config set-credentials docker-for-desktop --token="${TOKEN}"
        echo $TOKEN
	
- 登录dashboard的时候，选择Token，输入上文控制台输出的内容



​		