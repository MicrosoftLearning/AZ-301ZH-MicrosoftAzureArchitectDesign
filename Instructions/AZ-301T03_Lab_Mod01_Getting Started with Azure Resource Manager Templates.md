---
lab:
    title: 'Azure 资源管理器模板和 Azure 构建基块入门'
    module: '模块 1：使用 Azure 资源管理器部署资源'
---

# 使用 Azure 资源管理器部署资源
# 实验室答案关键点： Azure 资源管理器模板和 Azure 构建基块入门

## 在开始之前

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

    > **注**: 你还可以在**开始菜单**中找到这些应用程序的快捷方式。


## 练习 1： 使用 Azure 门户中的 Azure 资源管理器模板部署核心 Azure 资源

#### 任务 1： 打开 Azure 门户

1. 在任务栏上，单击**Microsoft Edge**图标。

1. 在打开的浏览器窗口中，导航到**Azure 门户** (<https://portal.azure.com>).

1. 如果出现提示，请使用你将在本实验室中使用的 Azure 订阅中具有所有者角色的用户帐户进行身份验证。

#### 任务 2： 使用 Azure 资源管理器模板从 Azure 门户部署 Azure 虚拟网络

1. 在 Azure门户的左上角，单击**创建资源**。

1. 在**新建**边栏选项卡顶部的**搜索市场**文本框中，输入**模板部署**，然后按 **Enter** 键。

1. 在**所有**边栏选项卡中的搜索结果部分，单击**模板部署**。

1. 在**模板部署**边栏选项卡上，单击**创建**按钮。

1. 在**自定义部署**边栏选项卡上，单击**在编辑器中构建自己的模板**链接。

1. 在**编辑模板**边栏选项卡上，单击**加载文件**。

1. 在**选择要上传的文件**对话框中，导航到**\\allfiles\\AZ-301T03\\Module_01\\Labfiles\\Starter\\**文件夹，选择 **vnet-simple-template.json** 文件，然后单击**打开**。 这会将以下内容加载到模板编辑器窗格中：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vnetNamePrefix": {
                "type": "string",
                "defaultValue": "vnet-",
                "metadata": {
                    "description": "Name prefix of the vnet"
                 }
            },
            "vnetIPPrefix": {
                "type": "string",
                "defaultValue": "10.2.0.0/16",
                "metadata": {
                    "description": "IP address prefix of the vnet"
                }
            },
            "subnetNamePrefix": {
                "type": "string",
                "defaultValue": "subnet-",
                "metadata": {
                    "description": "Name prefix of the subnets"
                }
            },
            "subnetIPPrefix": {
                "type": "string",
                "defaultValue": "10.2.0.0/24",
                "metadata": {
                    "description": "IP address prefix of the first subnet"
                }
            }
        },
        "variables": {
            "vnetName": "[concat(parameters('vnetNamePrefix'), resourceGroup().name)]",
            "subnetNameSuffix": "0"
        },
        "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[variables('vnetName')]",
            "type": "Microsoft.Network/virtualNetworks",
            "location": "[resourceGroup().location]",
            "scale": null,
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[parameters('vnetIPPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[concat(parameters('subnetNamePrefix'), variables('subnetNameSuffix'))]",
                        "properties": {
                        "addressPrefix": "[parameters('subnetIPPrefix')]"
                        }
                    }
                ],
                "virtualNetworkPeerings": [],
                "enableDdosProtection": false,
                "enableVmProtection": false
            },
            "dependsOn": []
            }
        ]
    }
    ```

1. 单击**保存**按钮以保留模板。

1. 返回**自定义部署**边栏选项卡，执行以下任务：

    - 保留**订阅**下拉列表条目的默认值。

    - 在**资源组**部分中，选择**新建**选项，然后在文本框中输入 **AADesignLab0201-RG**。

    - 在**位置**下拉列表中，选择要在本实验室中部署资源的 Azure 区域。

    - 保留 **vnetNamePrefix** 文本框的默认值。

    - 保留 **vnetIPPrefix** 文本框的默认值。

    - 保留 **subnetNamePrefix** 文本框的默认值。

    - 保留 **subnetIPPrefix** 文本框的默认值。

    - 在**条款和条件**部分，选中**我同意上述条款和条件**复选框。

    - 单击**购买**按钮。

1. 在继续执行下一个任务之前，请等待部署完成。


#### 任务 3： 查看部署元数据

1. 在 Azure门户的中心菜单中，单击**资源组**。

1. 在**资源组**边栏选项卡上，单击代表你在上一个任务中部署模板的资源组的条目。

1. 激活**概览**选项后，在资源组边栏选项卡上，单击**部署**链接。

1. 在打开的边栏选项卡上，单击最新部署以在新边栏选项卡中查看其元数据。

1. 在部署边栏选项卡中，请遵守**操作详情**部分中显示的信息。

> **回顾**: 在本练习中，你使用 Azure 门户中的Azure 资源管理器模板部署了 Azure 虚拟网络



## 练习 2： 使用 Azure Cloud Shell 中的 Azure 构建基块部署核心 Azure 资源

#### 任务 1： 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标以打开新的 shell 实例。

    > **注**: **Cloud Shell** 图标是由*大于*和*下划线*字符组合而成的符号。

1. 如果这是你第一次使用订阅打开 **Cloud Shell**，你将看到首次使用时的 **Cloud Shell** 配置向导。 出现提示时，在**欢迎使用 Azure Cloud Shell**窗格中，单击**Bash (Linux)**。

    > **注**: 如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你在本课程的实验室中使用了现有订阅。 如果是，请直接进入下一个任务。

1. 在**你没有安装存储**窗格中，单击**显示高级设置**，然后执行以下任务：

    - 保留**订阅**下拉列表条目的默认值。

    - 在 **Cloud Shell 区域**下拉列表中，选择与本实验室中部署资源的位置匹配或靠近的 Azure 区域

    - 资源组：确保选中**新建**选项，然后在文本框中输入 **AADesignLab0202-RG**。

    - 在**存储帐户**部分中，确保选中**新建**选项，然后在下面的文本框中输入由 3 到 24 个字符和数字组成的唯一名称。

    - 在**文件共享**部分中，确保选中**新建**选项，然后在下面的文本框中输入 **cloudshell**。

    - 单击**创建存储**按钮。

1. 等待 **Cloud Shell** 完成其首次设置过程，然后继续执行下一个任务。


#### 任务 2： 在 Azure Cloud Shell 中安装 Azure 构建基块 npm 包

1. 在门户底部的 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建本地目录来安装 Azure 构建基块 npm 包：

    ```sh
    mkdir ~/.npm-global
    ```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以更新 npm 配置来包含新的本地目录：

    ```sh
    npm config set prefix '~/.npm-global'
    ```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以打开 ~./bashrc 配置文件进行编辑：

    ```sh
    vi ~/.bashrc
    ```

1. 在 **Cloud Shell** 命令提示符的 vi 编辑器界面中，向下滚动到文件的底部（或输入 **G**），向右滚动到最后一行的最右侧字符（或输入**$**），输入**a** 进入 **插入**模式，按 **Enter** 键开始新行，然后输入以下内容将新创建的目录添加到系统路径：

    ```sh
    export PATH="$HOME/.npm-global/bin:$PATH"
    ```

1. 在 **Cloud Shell** 命令提示符的 vi 编辑器界面中，若要保存更改并关闭文件，按 **Esc**，再按 **:**，输入 **wq!**，然后按 **Enter** 键。

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以安装 Azure 构建基块 npm 包：

    ```sh
    npm install -g @mspnp/azure-building-blocks
    ```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以退出 shell：

    ```sh
    exit
    ```

1. 在 **Cloud Shell 超时**窗格中，单击**重新连接**。

    > **注**: 安装构建基块 npm 包后，你需要重新启动 Cloud Shell 才能生效。


#### 任务 3： 使用 Azure 构建基块从 Cloud Shell 部署 Azure 虚拟网络

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以下载包含 Azure 构建基块模板的 GitHub 存储库：

    ```sh
    git clone https://github.com/mspnp/template-building-blocks.git
    ```

1.  在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以查看将用于此部署的 Azure 构建基块参数文件的内容：

    ```sh
    cat ./template-building-blocks/scenarios/vnet/vnet-simple.json 
    ```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个值，该值指定了 Azure 订阅的名称：

    ```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" | tr -d '"')
    ```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了你先前在本练习中创建的资源组名称：

    ```sh
    RESOURCE_GROUP='AADesignLab0202-RG'
    ```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了将用于部署的 Azure 区域：

    ```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab0201-RG'].location" --output tsv)
    ```

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以使用 Azure 构建基块部署虚拟网络：

    ```sh
    azbb -g $RESOURCE_GROUP -s $SUBSCRIPTION_ID -l $LOCATION -p ./template-building-blocks/scenarios/vnet/vnet-simple.json --deploy
    ```

1. 在继续执行下一个任务之前，请等待部署完成。


#### 任务 4： 查看部署元数据

1. 在门户的左侧，单击**资源组**链接。

1. 在**资源组**边栏选项卡上，单击代表你先前在本练习中创建的资源组的条目。

1. 激活**概览**选项后，在资源组边栏选项卡上，单击**部署**链接。

1. 在打开的边栏选项卡上，单击最新部署以在新边栏选项卡中查看其元数据。

1. 在部署边栏选项卡中，请遵守**操作详情**部分中显示的信息。

1. 关闭 **Cloud Shell** 窗格。

> **回顾**: 在本练习中，你使用 Azure 门户中的Azure 资源管理器模板部署了 Azure 虚拟网络


## 练习 3： 删除实验室资源

#### 任务1： 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标以打开 Cloud Shell 窗格。

1. 在门户底部的 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以列出你在本实验中创建的所有资源组：

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab02')]".name --output tsv
    ```

1. 验证输出是否仅包含你在本实验室中创建的资源组。 这些组将在下一个任务中删除。

#### 任务 2： 删除资源组

1. 在 **Cloud Shell** 命令提示符中，输入以下命令并按 **Enter** 键以删除在本实验中创建的资源组

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab02')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

1. 关闭门户底部的 **Cloud Shell** 提示符。


> **回顾**: 在本练习中，你删除了本实验室中使用的资源。
