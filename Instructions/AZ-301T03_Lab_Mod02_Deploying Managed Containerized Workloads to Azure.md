---
lab:
    title: '将托管的容器化工作负载部署到 Azure'
    module: '模块 2：在 Azure 中创建托管服务器应用程序'
---

# 在 Azure 中创建托管服务器应用程序

# 实验室答案关键点： 将托管的容器化工作负载部署到 Azure

## 开始之前

1. 确保使用以下凭证登录到 Windows 10 实验室虚拟机：

    - 用户名： **Admin**

    - 密码： **Pa55w.rd**

1. 查看位于 Windows 10 桌面底部的任务栏。 任务栏中包含你将在实验中使用的常见应用程序的图标：

    - Microsoft Edge

    - 文件资源管理器

    - [Visual Studio Code](https://code.visualstudio.com/)

    - [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

    - Windows 版 Bash on Ubuntu

    - Windows PowerShell

    > **注**: 你还可以在 **开始菜单** 中找到这些应用程序的快捷方式。

## 练习 1： 创建 Azure Kubernetes 服务 (AKS) 群集

#### 任务1： 打开 Azure 门户

1. 在任务栏上，单击 **Microsoft Edge** 图标。

1. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>).

1. 如果出现提示，请使用你将在本实验室中使用的 Azure 订阅中具有所有者角色的用户帐户进行身份验证。

#### 任务 2： 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标以打开新的 shell 实例。

    > **注**: **Cloud Shell** 图标是由 *大于* 和 *下划线* 字符组合而成的符号。

1. 如果这是你第一次使用订阅打开 **Cloud Shell**，你将看到首次使用时的 **Cloud Shell** 配置向导。 出现提示时，在 **欢迎使用 Azure Cloud Shell** 窗格中，单击 **Bash (Linux)**。

    > **注**: 如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你在本课程的实验室中使用了现有订阅。 如果是，请直接进入下一个任务。

1. 在**你没有安装存储**窗格中，单击**显示高级设置**，然后执行以下任务：

    - 保留**订阅**下拉列表条目的默认值。

    - 在 **Cloud Shell 区域**下拉列表中，选择与你计划在本实验室中部署资源的位置匹配或靠近的 Azure 区域。

    - 在 **资源组** 部分中，确保选中 **新建** 选项，然后在文本框中输入 **AADesignLab0401-RG**。

    - 在 **存储帐户** 部分中，确保选中 **新建** 选项，然后在下面的文本框中输入由 3 到 24 个字符和数字组成的唯一名称。 

    - 在 **文件共享** 部分中，确保选中 **新建** 选项，然后在下面的文本框中输入 **cloudshell**。

    - 单击 **创建存储** 按钮。

1. 等待 **Cloud Shell** 完成首次设置过程，然后继续下一个任务。

#### 任务 3： 使用 Cloud Shell 创建 AKS 群集

1. 在门户底部的 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了将在此任务中使用的资源组名称：

```sh
    RESOURCE_GROUP='AADesignLab0402-RG'
```

1. 在 **“Cloud Shell”** 命令提示符，键入以下命令并按 **Enter** 键创建一个变量，该值指定将用于部署的 Azure 区域（将占位符 `<Azure region>` 替换为你希望在本实验中部署资源的 Azure 区域的名称。`az account list-locations` 将列出订阅的所有可用位置。）：

```sh
    LOCATION='<Azure region>'
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建新资源组：

```sh
    az group create --name $RESOURCE_GROUP --location $LOCATION
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建新的 AKS 群集：

```sh
    az aks create --resource-group $RESOURCE_GROUP --name aad0402-akscluster --node-count 1 --node-vm-size Standard_DS1_v2 --generate-ssh-keys
```

    > **注**: 如果你收到有关 VM 大小（此值由 `--node-vm-size` 参数表示）可用性的错误消息，请查看该消息并尝试其他建议的 VM 大小。

    > **注**: 或者，在 **Cloud Shell** 上的 **PowerShell**中，你可以通过运行以下命令并查看**限制**列中的值来确定给定区域中订阅可用的 VM 大小（确保使用目标区域名称替换 `region` 占位符）：

```pwsh
    Get-AzComputeResourceSku | where {$_.Locations -icontains "region"} | Where-Object {($_.ResourceType -ilike "virtualMachines")}
```

    > **注**: 对于你订阅中不可用的 VM 大小，**限制**列将包含值 **NotAvailableForSubscription**。

    > **注**: 自 2019 年 2 月 21 日起，VM 大小 **Standard_DS2_V2** 可在 **westeurope** 中找到。


1. 在继续执行下一个任务之前，请等待部署完成。

    > **注**: 此操作可能需要耗时 10 分钟。


#### 任务 4： 连接到 AKS 群集。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以检索用于访问 AKS 群集的凭证：

```sh
    az aks get-credentials --resource-group $RESOURCE_GROUP --name aad0402-akscluster 
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证与 AKS 群集的连接：

```
    kubectl get nodes
```

1. 在 **Cloud Shell** 命令提示符中，查看输出并验证节点是否在报告 **就绪** 状态。 重新运行该命令，直到显示正确的状态。
  
> **结果**: 完成本练习后，你应该已成功部署新的 AKS 群集。


## 练习 2： 管理 AKS 群集及其容器化工作负载。

#### 任务 1： 将容器化应用程序部署到 AKS 群集

1. 在 Microsoft Edge 窗口中打开 Azure 门户，在 **Cloud Shell** 提示符中，输入以下命令并按 **Enter** 键以从 Docker Hub 部署 **nginx** 映像：

```
    kubectl run aad0402-akscluster --image=nginx --replicas=1 --port=80
```

    > **注**: 输入部署名称时，请确保使用小写字母。 你还将收到一条通知，指出此命令已弃用，将在后续版本中删除，但已成功创建群集。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证是否已创建 Kubernetes pod：

```
    kubectl get pods
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以标识部署状态：

```
    kubectl get deployment
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以使 pod 可通过 Internet 使用：

```
    kubectl expose deployment aad0402-akscluster --port=80 --type=LoadBalancer
```

    > **注**: 输入部署名称时，请确保使用小写字母。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以确定是否已配置公共 IP 地址：

```
    kubectl get service --watch
```

1. 等待 **“aad0402-akscluster”** 条目的 **“EXTERNAL-IP”** 列中的值从 **“\<pending\>”** 变为公共 IP 地址，然后按 Ctrl-C 组合键。记录 **“aad0402-akscluster”** 的 **“EXTERNAL-IP”** 列中的公共 IP 地址。 

1. 启动 Microsoft Edge 并浏览到你在上一步中获得的 IP 地址。 验证 Microsoft Edge 是否显示包含 **Welcome to nginx!** 消息的网页。


#### 任务 2： 扩展容器化应用程序和 AKS 群集节点

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以扩展部署：

```
    kubectl scale --replicas=2 deployment/aad0402-akscluster
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证扩展部署的结果：

```
    kubectl get pods
```

    > **注**: 查看命令的输出并验证 pod 的数量是否增加到 2。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以缩小群集节点数：

```sh
    az aks scale --resource-group $RESOURCE_GROUP --name aad0402-akscluster --node-count 2
```

1. 等待附加节点配置完成。

    > **注**: 此操作可能需要耗时 10 分钟。 如果失败，请重新运行 `az aks scale` 命令。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证扩展群集的结果：

```
    kubectl get nodes
```

    > **注**: 查看命令的输出并验证节点数是否增加到 2。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以扩展部署：

```
    kubectl scale --replicas=10 deployment/aad0402-akscluster
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证扩展部署的结果：

```
    kubectl get pods
```

    > **注**: 查看命令的输出并验证 pod 的数量是否增加到 10。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以查看跨群集节点的 pod 分布：

```
    kubectl get pod -o=custom-columns=NODE:.spec.nodeName,POD:.metadata.name
```

    > **注**: 查看命令的输出并验证 pod 是否分布在两个节点上。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以删除部署：

```
    kubectl delete deployment aad0402-akscluster 
```

## 练习3： 在 AKS 群集中自动扩展 pod

#### 任务 1： 使用 **yaml** 文件部署 Kubernetes pod。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以下载容器化应用程序样本：

```
    git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以导航到下载的应用程序所在的位置：

```sh
    cd azure-voting-app-redis
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以列出应用程序 **yaml** 文件的内容：

```sh
    cat azure-vote-all-in-one-redis.yaml
```

1. 查看命令的输出并验证 pod 定义中是否包含以下格式的请求和限制：

```yaml
    resources:
      requests:
         cpu: 250m
      limits:
         cpu: 500m
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以部署基于 **yaml** 文件的应用程序：

```
    kubectl apply -f azure-vote-all-in-one-redis.yaml
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证是否已创建 Kubernetes pod：

```
    kubectl get pods
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以确定是否已设置容器化应用程序的公共 IP 地址：

```
    kubectl get service azure-vote-front --watch
```

1. 等待 **“azure-vote-front”** 条目的 **“EXTERNAL-IP”** 列中的值从 **“\<pending\>”** 变为公共 IP 地址，然后按 Ctrl-C 组合键。记录 **“azure-vote-front”** 的 **“EXTERNAL-IP”** 列中的公共 IP 地址。 

1. 启动 Microsoft Edge 并浏览到你在上一步中获得的 IP 地址。 验证 Microsoft Edge 是否显示包含 **Azure Voting App** 消息的网页。

#### 任务 2： 自动扩展 Kubernetes pod。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以下载容器化应用程序样本：

```
    git clone https://github.com/kubernetes-incubator/metrics-server.git
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以安装 **Metrics Server**：

```
    kubectl create -f ~/metrics-server/deploy/1.8+/
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以配置 **azure-vote-front**部署的自动扩展：

```
    kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以查看自动扩展的状态：

```
    kubectl get hpa
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以查看 pod：

```
    kubectl get pods
```

    > **注**: 验证副本的数量是否已增加到 3。 否则请等待一分钟，然后重新运行前两个步骤。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以删除部署：

```
    kubectl delete deployment azure-vote-front 
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以删除部署：

```
    kubectl delete deployment azure-vote-back 
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证你在先前步骤中运行的命令是否已成功完成：

```
    kubectl get pods
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以删除 AKS 群集：

```
    az aks delete --resource-group AADesignLab0402-RG --name aad0402-akscluster --yes --no-wait
```

1. 关闭 **Cloud Shell** 窗格。

> **回顾**: 在本练习中，你实现了对 AKS 群集中 pod 的自动扩展


## 练习 4： 使用 AKS 实现 DevOps

#### 任务 1： 使用 AKS 部署 DevOps

> **注意**：此解决方案基于 https://docs.microsoft.com/zh-cn/azure/architecture/example-scenario/apps/devops-with-aks 中描述的 DevOps with Containers 解决方案。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以生成 SSH 密钥对，该密钥对将用于在访问运行 Jenkins 实例和 Grafana 控制台的 Linux VM 时进行身份验证：

```
    ssh-keygen -t rsa -b 2048
```

    - 当提示输入要保存密钥的文件时，按 **Enter** 键以接受默认值 **(~/.ssh/id_rsa)**.

    - 提示输入密码时，按 **Enter** 键两次。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了新生成的密钥对的公钥：

```sh
    PUBLIC_KEY=$(cat ~/.ssh/id_rsa.pub)
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了新生成的密钥对的公钥，并考虑了公钥可能包含的任何特殊字符：

```sh
    PUBLIC_KEY_REGEX="$(echo $PUBLIC_KEY | sed -e 's/\\/\\\\/g; s/\//\\\//g; s/&/\\\&/g')"
```

    > **注**: 这是必要的操作，因为你将使用 **sed** 实用程序将此字符串插入 Azure 资源管理器模板参数文件。 或者，你只需打开文件并直接在文件中输入公钥字符串即可。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了将用于部署的资源组名称：

```sh
    RESOURCE_GROUP='AADesignLab0403-RG'
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了将用于部署的 Azure 区域：

```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab0402-RG'].location" --output tsv)
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建新资源组：

```sh
    az group create --name $RESOURCE_GROUP --location $LOCATION
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建 Azure Active Directory 服务主体，从而对示例解决方案中的服务和资源进行身份验证：

```sh
    SERVICE_PRINCIPAL=$(az ad sp create-for-rbac --name AADesignLab0403-SP --output json)
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以检索新建服务主体的 **appId** 属性：

```sh
    APP_ID=$(echo $SERVICE_PRINCIPAL | jq -r .appId)
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以检索新的服务主体的 **password** 属性：

```sh
    PASSWORD=$(echo $SERVICE_PRINCIPAL | jq -r .password)
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建用于部署示例解决方案的参数文件，并在 vi 界面中将其打开：

```sh
    vi ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符的 vi 编辑器界面中，添加示例参数文件的内容 (**\\allfiles\\AZ-301T03\\Module_02\\Labfiles\\Starter\\parameters.json**):

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "spClientId": {
          "value": "$APP_ID"
        },
        "spClientSecret": {		
          "value": "$PASSWORD"
        },
        "linuxAdminUsername": {
          "value": "Student"
        },
        "linuxAdminPassword": {		
          "value": "Pa55w.rd1234"
        },
        "linuxSSHPublicKey": {		
          "value": "$PUBLIC_KEY_REGEX"
        }
      }
    }
```

1. 在 **Cloud Shell** 命令提示符的 vi 编辑器界面中，若要保存更改并关闭文件，按 **Esc**，再按 **:**，输入 **wq!**，然后按 **Enter** 键。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以将 **appId** 属性的占位符替换为参数文件中的 **$APP_ID** 变量值：

```sh
    sed -i.bak1 's/"$APP_ID"/"'"$APP_ID"'"/' ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以将 **password** 属性的占位符替换为参数文件中的 **$PASSWORD** 变量值：

```sh
    sed -i.bak2 's/"$PASSWORD"/"'"$PASSWORD"'"/' ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以将 **sshPublicKey** 参数的占位符替换为参数文件中的 **$PUBLIC_KEY_REGEX** 变量值：

```sh
    sed -i.bak3 's/"$PUBLIC_KEY_REGEX"/"'"$PUBLIC_KEY_REGEX"'"/' ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以验证是否已在参数文件中成功替换占位符：

```sh
    cat ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以使用驻留在 GitHub 存储库中的 Azure 资源管理器模板部署示例解决方案：

```sh
    az group deployment create --resource-group $RESOURCE_GROUP --template-uri https://raw.githubusercontent.com/MicrosoftLearning/AZ-301-MicrosoftAzureArchitectDesign/master/allfiles/AZ-301T03/Module_02/LabFiles/Starter/azuredeploy.json --parameters ~/parameters.json
```

1. 在继续执行下一个任务之前，请等待部署完成。

    > **注**: 部署可能需要耗时 15 分钟。


#### 任务 2： 使用 AKS 架构审核 DevOps

1. 在 Azure 门户的中心菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡上，单击代表 **AADesignLab0403-RG** 资源组的条目。

1. 在 **AADesignLab0403-RG** 资源组边栏选项卡上，查看资源列表，并将其与 https://docs.microsoft.com/zh-cn/azure/architecture/example-scenario/apps/devops-with-aks 上提供的信息进行比较

> **回顾**：在本练习中，你部署了具有 AKS 体系结构的 DevOps。


## 练习 5： 删除实验室资源

#### 任务1： 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标以打开 Cloud Shell 窗格。

1. 在门户底部的 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以列出你在本实验中创建的所有资源组：

```sh
    az group list --query "[?starts_with(name,'AADesignLab04')]".name --output tsv
```

1. 验证输出是否仅包含你在本实验室中创建的资源组。 这些组将在下一个任务中删除。

#### 任务 2： 删除资源组

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以删除在本实验中创建的资源组

```sh
    az group list --query "[?starts_with(name,'AADesignLab04')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

1. 关闭门户底部的 **Cloud Shell** 提示符。


> **回顾**: 在本练习中，你删除了本实验室中使用的资源。
