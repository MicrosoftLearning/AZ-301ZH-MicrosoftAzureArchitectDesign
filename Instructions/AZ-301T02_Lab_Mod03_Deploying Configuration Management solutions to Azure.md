---
lab:
    title: '部署配置管理解决方案至 Azure'
    module: '模块 3：监控和自动化 Azure 解决方案'
---

# 监控和自动化 Azure 解决方案

# 实验室答案密钥：部署配置管理解决方案至 Azure

## 在我们开始之前

1. 确保你已使用以下凭据登录到 Windows 10 实验室虚拟机：

    - 用户名称：**管理员**

    - 密码：**Pa55w.rd**

1. 查看位于 Windows 10 桌面底部的任务栏。任务栏包含你将在实验中使用的常见应用程序的图标：

    - Microsoft Edge

    - 文件资源管理器

    - [Visual Studio 代码](https://code.visualstudio.com/)

    - [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

    - Windows Ubuntu 上的 Bash

    - Windows PowerShell

    > **注意**：你还可以在 **开始菜单** 中找到这些应用程序的快捷方式。

## 练习 1：部署计算资源

#### 任务 1：打开 Azure 门户

1. 在任务栏上，单击 **Microsoft Edge** 图标。

1. 在打开的浏览器窗口中，导航至 **Azure 门户** (<https://portal.azure.com>)。

1. 出现提示时，请使用你将在本实验中使用的 Azure 订阅中具有所有者角色的用户帐户进行身份验证。

#### 任务 2：打开 Cloud Shell

1. 单击门户顶部的 **Cloud Shell** 图标，打开新的 Shell 实例。

    > **注意**：**Cloud Shell** 图标是由 *大于号* 和 *下划线* 字符组合而成的。

1. 如果这是你第一次使用你的订阅打开 **Cloud Shell**，将看到一个首次使用 **Cloud Shell** 的配置向导。出现提示后，在 **欢迎使用 Azure Cloud Shell** 窗格中，单击 **Bash (Linux)**。

    > **注意**：如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你正在使用本课程实验室的现有订阅。如果是，请直接进入下一个任务。 

1. 在 **你没有安装任何存储** 窗格中，单击 **显示高级设置**，执行以下任务：

    - 将 **订阅** 下拉列表项设置为其默认值。

    - 在 **Cloud Shell 区域** 的下拉列表中，选择匹配 Azure 区域或靠近你打算在本实验中部署资源的位置。

    - 在 **资源组** 部分，选择 **创建新的** 选项然后在文本框中键入 **AADesignLab1201-RG**。

    - 在 **存储帐户** 部分，确保已选择 **创建新的** 选项，然后在下面的文本框中键入由 3 到 24 个字符和数字组成的唯一名称。 

    - 在 **文件共享** 部分，确保已选择 **创建新的** 选项，然后在下面的文本框中键入 **cloudshell**。

    - 单击 **创建储存** 按钮。

1. 在继续下一个任务之前，请等待 **Cloud Shell** 完成其首次设置过程。

#### 任务 3：部署 Linux VM

1. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 Clould Shell 实例。

1. 在 **Cloud Shell** 边栏选项卡中，单击 **上传/下载文件** 图标，然后在下拉菜单中单击 **上传**。 

1. 在 **打开** 对话框中，导航至 **\\allfiles\\AZ-301T02\\Module_03\\LabFiles\\Starter\\** 文件夹，选择 **linux-template.json** 文件，然后单击 **打开**。该文件包含以下模板：

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "userName": {
                "type": "string",
                "defaultValue": "Student"
            },
            "password": {
                "type": "securestring"
            }
        },
        "variables": {
            "vmName": "[concat('lvm', uniqueString(resourceGroup().id))]",
            "nicName": "[concat('nic', uniqueString(resourceGroup().id))]",
            "publicIPAddressName": "[concat('pip', uniqueString(resourceGroup().id))]",
            "virtualNetworkName": "[concat('vnt', uniqueString(resourceGroup().id))]",
            "subnetName": "Linux",
            "imageReference": {
                "publisher": "suse",
                "offer": "opensuse-leap",
                "sku": "42.3",
                "version": "latest"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-06-01",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[variables('publicIPAddressName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "publicIPAllocationMethod": "Dynamic"
                }
            },
            {
                "apiVersion": "2017-06-01",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[variables('virtualNetworkName')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "10.0.0.0/16"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnetName')]",
                            "properties": {
                                "addressPrefix": "10.0.0.0/24"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2017-10-01",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[variables('nicName')]",
                "location": "[resourceGroup().location]",
                "dependsOn": [
                    "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                    "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[concat(resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName')), '/subnets/', variables('subnetName'))]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2017-03-30",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[variables('vmName')]",
                "location": "[resourceGroup().location]",
                "dependsOn": [
                    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "Standard_A1_v2"
                    },
                    "osProfile": {
                        "computerName": "[variables('vmName')]",
                        "adminUsername": "[parameters('username')]",
                        "adminPassword": "[parameters('password')]"
                    },
                    "storageProfile": {
                        "imageReference": "[variables('imageReference')]",
                        "osDisk": {
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('nicName'))]"
                            }
                        ]
                    }
                }
            }
        ]
    }
    ```

1. 在 **Cloud Shell** 命令提示符，键入以下命令并按 **Enter 键** 创建一个变量，该变量的值指定将包含 hub 虚拟网络的资源组的名称：

    ```sh
    RESOURCE_GROUP='AADesignLab1202-RG'
    ```

1. 在 **Cloud Shell** 命令提示符，键入以下命令并按 **Enter 键** 创建一个变量，该值指定将用于部署的 Azure 区域（将占位符`<Azure region>`替换为你希望在本实验中部署资源的 Azure 区域的名称）：

    ```sh
    LOCATION='<Azure region>'
    ```

1. 在 **Cloud Shell** 命令提示符，键入以下命令并按 **Enter 键** 创建新资源组：

    ```sh
    az group create --name $RESOURCE_GROUP --location $LOCATION
    ```

1. 在 **Cloud Shell** 命令提示符，键入以下命令并按 **Enter 键** 使用指定的参数文件部署 Azure 资源管理器模板：

    ```sh
    az group deployment create --resource-group $RESOURCE_GROUP --template-file ~/linux-template.json --parameters password=Pa55w.rd1234
    ```

1. 在继续执行下一个任务之前，无需等待部署完成。

#### 任务 4：部署 Azure 自动化帐户

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在顶部的 **新建** 边栏选项卡的 **搜索应用市场** 文本框中，输入 **自动化** 并按 ** Enter 键**。

1. 在 **全部** 边栏选项卡的搜索结果中，单击 **自动化**。

1. 在 **自动化** 边栏选项卡中，单击 **创建**。

1. 在 **添加自动化帐户** 边栏选项卡中，执行以下任务：

    - 在 **名称** 文本框中，输入 **LinuxAutomation**。
    
    - 将 **订阅** 下拉列表项设置为其默认值。

    - 在 **资源组** 部分，选择 **创建新的** 选项，然后在文本框中键入 **AADesignLab1203-RG**。

    - 在 **位置** 下拉列表中，选择 Azure 区域匹配或靠近与上一任务中部署 Azure VM 的位置。

    - 在 **创建 Azure 运行方式帐户** 部分，确保 **是** 选项已被选中。

    - 单击 **创建** 按钮。

1. 在继续执行下一个任务之前，请等待配置完成。 

> **回顾**：在本练习中，你使用 Azure 资源管理器模板创建了一个 Linux Vm，并从 Azure 门户配置了 Azure 自动化帐户。


## 练习 2：配置 Azure 自动化 DSC

#### 任务 1：导入 Linux PowerShell DSC 模块

1. 在 Azure 门户的中心菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡中，单击 **AADesignLab1203-RG**。

1. 在 **AADesignLab1203-RG** 边栏选项卡中，单击新创建的 Azure 自动化帐户。

1. 在 **LinuxAutomation** 边栏选项卡中的 **共享资源** 左侧部分，单击 **模块库**。

1. 在 **LinuxAutomation - 模块库** 边栏选项卡中，执行以下任务：

    - 在 **搜索** 文本框中，输入 **nx** 并按下 **Enter 键**。

    - 在搜索结果中，单击 **nx** 模块。

1. 在 **nx** 边栏选项卡顶部，单击 **导入** 按钮。

1. 在 **导入** 边栏选项卡中，单击 **确认** 按钮。

1. 在继续执行下一个任务之前，请等待导入过程完成。在 **nx 模块** 边栏选项卡中将出现一个状态消息，告知模块已成功导入。

    > **注意**：这个过程大约需要 2 分钟。

#### 任务 2：创建 Linux DSC 配置

1. 导航回到 **LinuxAutomation** 边栏选项卡。

1. 返回 **LinuxAutomation** 边栏选项卡，在 **配置管理** 部分，单击 **状态配置 (DSC)**。

1. 在 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡，单击 **配置** 链接。

1. 在 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡，单击窗格顶部的 **+ 添加** 按钮。

1. 在 **导入** 边栏选项卡，执行以下任务：

    - 在 **配置文件** 字段旁，单击带有文件夹图标的蓝色按钮。

    - 在 **选择要上载的文件** 对话框中，导航至 **\\allfiles\\AZ-301T02\\Module_02\\LabFiles\\Starter\\** 文件夹。

    - 选择 **lampserver.ps1** 文件。

    - 单击 **打开** 按钮，关闭对话框并返回到 **导入** 边栏选项卡。

    - 在 **名称** 文本框中，接受默认条目 **lampserver**。

    - 在 **描述** 文本框中，输入 **LAMP Server configuration using PHP and MySQL**。

    - 单击 **确认** 按钮。

1. 返回 **DSC 配置** 窗格，单击 **刷新** 然后单击新创建的 **lampserver** 配置。

1. 在 **lampserver 配置** 边栏选项卡中，单击边栏选项卡顶部的 **编译** 按钮。在确认对话框中，单击 **是** 继续编译配置。

1. 等待编译任务完成。要确定编译任务的状态，请查看 **状态** 列中 **编译工作** 部分的 **lampserver 配置** 边栏选项卡。

    > **注意**：你可能需要关闭并重新打开边栏选项卡以查看最新的编译状态。此边栏选项卡不会自动刷新。

#### 任务 3：访问 Linux VM

1. 导航回到 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡。

1. 返回 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡，单击 **节点** 链接。

1. 在 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡，单击窗格顶部的 **+ 添加** 按钮。

1. 在 **虚拟机** 边栏选项卡，单击表示你在上一个练习中部署的 Linux 虚拟机的条目。

1. 在虚拟机边栏选项卡中，单击 **+ Connect**。

1. 在 **注册** 边栏选项卡中，执行以下任务：

    - 关闭 **注册码** 设置，保留默认值。

    - 在 **节点配置名称** 的下拉列表中，选择 **lampserver.localhost** 条目。

    - 所有其他设置保留为默认值。

    - 单击 **确认** 按钮。

1. 等待连接过程完成，然后再继续下一步。

1. 导航回到 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡。

1. 在 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡，选择你在上一个练习中部署的虚拟机的 **节点** 部分。

1. 在虚拟机边栏选项卡，单击 **分配节点配置**。

1. 在“分配节点配置”边栏选项卡上，选择节点配置 **lampserver.host** 然后单击 **确认** 按钮。

1. 返回 **LinuxAutomation - 状态配置 (DSC)** 边栏选项卡，单击 **刷新** 按钮。

1. 在 DSC 节点列表中，验证 Linux 虚拟机是否具有 **兼容** 状态。

> **回顾**：在本练习中，你创建了 PowerShell DSC 配置并将配置应用于 Linux 虚拟机。


## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标以打开 Cloud Shell 窗格。

1. 在门户网站底部的 **Cloud Shell** 命令提示符下，键入以下命令并按 **Enter 键** 列出你在本实验中创建的所有资源组：

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab12')]".name --output tsv
    ```

1. 验证输出是否仅包含你在本实验中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符下，键入以下命令并按 **Enter 键** 删除你在本实验中创建的资源组

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab12')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

1. 关闭在门户网站的底部的 **Cloud Shell** 提示。


> **回顾**：在本练习中，你删除了本实验中使用的资源。
