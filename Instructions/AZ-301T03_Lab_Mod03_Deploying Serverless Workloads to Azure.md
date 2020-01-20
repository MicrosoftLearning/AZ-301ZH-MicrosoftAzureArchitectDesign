---
lab:
    title: '将无服务器工作负载部署到 Azure'
    module: '模块 3：在 Azure 中编写无服务器应用程序'
---

# 在 Azure 中编写无服务器应用程序

# 实验室答案关键点：将无服务器工作负载部署到 Azure

## 开始之前

1. 确保使用以下凭证登录到 Windows 10 实验室虚拟机：

    - 用户名称：**Admin**

    - 密码：**Pa55w.rd**

1. 查看位于 Windows 10 桌面底部的任务栏。任务栏中包含你将在实验中使用的常见应用程序的图标：

    - Microsoft Edge

    - 文件资源管理器

    - [Visual Studio Code](https://code.visualstudio.com/)

    - [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

    - Windows 版 Bash on Ubuntu

    - Windows PowerShell

    > **注**：你还可以在 **开始菜单** 中找到这些应用程序的快捷方式。

## 练习 1：创建 Web 应用

#### 任务 1：打开 Azure 门户

1. 在任务栏上，单击 **Microsoft Edge** 图标。

1. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>)。

1. 如果出现提示，请使用你将在本实验室中使用的 Azure 订阅中具有所有者角色的用户帐户进行身份验证。

#### 任务 2：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标以打开新 shell 实例。

    > **注**：该 **Cloud Shell** 图标是由 *大于* 和 *下划线* 字符组合而成的符号。

1. 如果这是你第一次使用你的订阅打开 **Cloud Shell** ，你将看到首次使用时的 **Cloud Shell** 配置向导。出现提示时，在 **欢迎使用 Azure Cloud Shell** 窗格中，单击 **Bash (Linux)**。

    > **注**：如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你在本课程的实验室中使用了现有订阅。如果是，请直接进入下一个任务。 

1. 在 **你没有安装存储空间** 窗格中，单击 **显示高级设置**，然后执行以下任务：

    - 将 **订阅** 下拉列表条目保留其默认值。

    - 在 **Cloud Shell 区域** 下拉列表中，选择与你计划在本实验室中部署资源的位置匹配或靠近的 Azure 区域

    - 在 **资源组** 部分中，确保已选中 **新建** 选项，然后在下面的文本框中输入 **AADesignLab0901-RG**。

    - 在 **存储帐户** 部分中，确保已选中 **新建** 选项，然后在下面的文本框中输入由 3 到 24 个字符和数字组成的唯一名称。 

    - 在 **文件共享** 部分中，确保已选中 **新建** 选项，然后在下面的文本框中输入 **cloudshell**。

    - 选择 **创建存储** 按钮。

1. 等待 **Cloud Shell** 完成首次设置过程，然后继续下一个任务。

#### 任务 3：创建应用服务计划

1. 在门户网站底部的 **Cloud Shell** 在命令提示符中，输入以下命令并按 **Enter** 键以创建一个变量，该值指定了你将在本练习中使用的资源组名称：

```sh
    RESOURCE_GROUP_APP='AADesignLab0502-RG'
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键创建一个变量，该值指定了将用于部署的 Azure 区域（在提示时输入区域名称）：

```sh
    read -p 'Region: ' LOCATION
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建资源组：

```sh
    az group create --name $RESOURCE_GROUP_APP --location $LOCATION
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建新的应用服务计划：

```sh
    az appservice plan create --is-linux --name "AADesignLab0502-$LOCATION" --resource-group $RESOURCE_GROUP_APP --location $LOCATION --sku B2
```

    > **注意**：如果命令失败并显示消息 *“Linux 辅助角色在资源组 AADesignLab0502-RG 中不可用”。使用以下链接来了解详细信息：https://go.microsoft.com/fwlink/?linkid=831180"*，删除资源组，将 **“位置”** 设置为 **“eastus”**，然后再次执行前面两个步骤。
    
#### 任务 4：创建 Web 应用程序实例

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以查看基于 Linux 的应用服务 Web 应用程序实例的可能运行时列表： 

```sh
    az webapp list-runtimes --linux --output tsv
``` 

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建一个新变量，该值是随机生成的字符串，你将用它作为新 Web 应用程序的名称：

```sh
    WEBAPPNAME1=webapp05021$RANDOM$RANDOM
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以使用唯一名称创建新 Web 应用程序：

```sh
    az webapp create --name $WEBAPPNAME1 --plan AADesignLab0502-$LOCATION --resource-group $RESOURCE_GROUP_APP --runtime "DOTNETCORE|2.1"
```

    > **注**：如果命令因 Web 应用程序名称重复而失败，请重新运行最后两个步骤，直到命令成功完成

1. 在继续执行下一个任务之前，请等待部署完成。

#### 任务 5：查看部署结果。

1. 在 Azure 门户的中心菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡中，单击 **AADesignLab0502-RG**。

1. 在 **AADesignLab0502-RG** 边栏选项卡中，单击代表你先前在本练习中创建的 Azure Web 应用程序的条目。

1. 在 Web 应用程序边栏选项卡中，单击顶部的 **浏览** 按钮。

1. 查看 Azure 应用服务生成的默认页面。

1. 关闭新的浏览器选项卡并返回显示 Azure 门户的浏览器选项卡。

> **回顾**：在本练习中，你创建了一个基于 Linux 的应用服务计划，其中包含一个空白 Web 应用程序。 


## 练习 2：部署 Web 应用程序代码

#### 任务 1：使用 Azure 资源管理器模板和 GitHub 通过 Web 应用程序扩展部署代码

1. 在门户顶部，单击 **Cloud Shell** 图标以打开新 shell 实例。

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建一个变量，该值指定了你将在本练习中使用的资源组名称：

```sh
    RESOURCE_GROUP_APP='AADesignLab0502-RG'
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键创建一个变量，该值指定了将用于部署的 Azure 区域：

```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab0502-RG'].location" --output tsv)
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建一个新变量，该值是随机生成的字符串，你将用它作为新 Web 应用程序的名称：

```sh
    WEBAPPNAME2=webapp05022$RANDOM$RANDOM
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以使用唯一名称创建新 Web 应用程序：

```sh
    az webapp create --name $WEBAPPNAME2 --plan AADesignLab0502-$LOCATION --resource-group $RESOURCE_GROUP_APP --runtime "NODE|9.4"
```

    > **注**：如果命令因 Web 应用程序名称重复而失败，请重新运行最后两个步骤，直到命令成功完成


1. 在 **Cloud Shell** 窗格中，单击 **上传/下载文件** 图标，然后在下拉菜单中单击 **上传**。 

1. 在 **打开** 对话框中，导航到 **\\allfiles\\AZ-301T03\\Module_03\\Labfiles\\Starter\\** 文件夹，选择 **github.json** 文件，然后单击 **打开**。该文件中包含以下 Azure 资源管理器模板：

```json
    {
        "$schema": "http://schemas.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webAppName": {
                "type": "string"
            },
            "repositoryUrl": {
                "type": "string"
            },
            "branch": {
                "type": "string",
                "defaultValue": "master"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('webAppName')]",
                "location": "[resourceGroup().location]",
                "properties": {},
                "resources": [
                    {
                        "apiVersion": "2015-08-01",
                        "name": "web",
                        "type": "sourcecontrols",
                        "dependsOn": [
                            "[resourceId('Microsoft.Web/Sites', parameters('webAppName'))]"
                        ],
                        "properties": {
                            "RepoUrl": "[parameters('repositoryUrl')]",
                            "branch": "[parameters('branch')]",
                            "IsManualIntegration": true
                        }
                    }
                ]
            }
        ]
    }
```

1. 在 **Cloud Shell** 窗格中，单击 **上传/下载文件** 图标，然后在下拉菜单中单击 **上传**。 

1. 在 **打开** 对话框中，导航到 **\\allfiles\\AZ-301T03\\Module_03\\Labfiles\\Starter\\** 文件夹，选择 **parameters.json** 文件，然后单击 **打开**。该文件中包含你先前上传的 Azure 资源管理器模板的以下参数：

```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "webAppName": {
          "value": "$WEBAPPNAME2"
        },
        "repositoryUrl": {		
          "value": "$REPOSITORY_URL"
        },
        "branch": {
          "value": "master"
        }
      }
    }
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建一个变量，该值指定了托管 Web 应用程序代码的 GitHub 存储库名称：

```sh
    REPOSITORY_URL='https://github.com/Azure-Samples/nodejs-docs-hello-world'
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键创建一个变量，该值指定了托管 Web 应用程序代码的 GitHub 存储库名称，并考虑了 URL 可能包含的任何特殊字符：

```sh
    REPOSITORY_URL_REGEX="$(echo $REPOSITORY_URL | sed -e 's/\\/\\\\/g; s/\//\\\//g; s/&/\\\&/g')"
```

    > **注**：这是必要的操作，因为你将使用 **SED** 实用程序将此字符串插入 Azure 资源管理器模板参数文件。或者，你只需打开文件并直接在文件中输入 URL 字符串即可。

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以使用参数文件中的 **$WEBAPPNAME2** 变量值替换 **webAppName** 参数值的占位符：

```sh
    sed -i.bak1 's/"$WEBAPPNAME2"/"'"$WEBAPPNAME2"'"/' ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以使用参数文件中的 **$REPOSITORY_URL** 变量值替换 **repositoryUrl** 参数值的占位符：

```sh
    sed -i.bak2 's/"$REPOSITORY_URL"/"'"$REPOSITORY_URL_REGEX"'"/' ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以验证是否已成功替换参数文件中的占位符：

```sh
    cat ~/parameters.json
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以通过使用本地 Azure 资源管理器模板和本地参数文件来部署在 GitHub 中驻留的 Web 应用程序代码：

```sh
    az group deployment create --resource-group $RESOURCE_GROUP_APP --template-file github.json --parameters @parameters.json
```

1. 在继续执行下一个任务之前，请等待部署完成。

    > **注**：部署大约需要一分钟。

#### 任务 2：查看部署结果。

1. 在 Azure 门户的中心菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡中，单击 **AADesignLab0502-RG**。

1. 在 **AADesignLab0502-RG** 边栏选项卡中，单击代表你在上一任务中创建的 Azure Web 应用程序的条目。

1. 在 Web 应用程序边栏选项卡中，单击顶部的 **浏览** 按钮。

1. 查看从 GitHub 部署的示例 Node.js Web 应用程序。

1. 关闭新的浏览器选项卡并返回显示 Azure 门户的浏览器选项卡。

#### 任务 3：使用 Docker Hub 容器映像部署代码

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建一个变量，该值指定了你将在此任务中使用的资源组名称：

```sh
    RESOURCE_GROUP_CONTAINER='AADesignLab0502-RG'
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键创建一个变量，该值指定了将用于部署的 Azure 区域：

```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab0502-RG'].location" --output tsv)
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以创建一个新变量，该值是随机生成的字符串，你将用它作为新 Web 应用程序的名称：

```sh
    WEBAPPNAME3=webapp05023$RANDOM$RANDOM
```

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以使用唯一名称创建新 Web 应用程序：

```sh
    az webapp create --name $WEBAPPNAME3 --plan AADesignLab0502-$LOCATION --resource-group $RESOURCE_GROUP_CONTAINER --deployment-container-image ghost
```

    > **注**：如果命令因 Web 应用程序名称重复而失败，请重新运行最后两个步骤，直到命令成功完成

1. 在继续执行下一个任务之前，请等待部署完成。

    > **注**：部署时间应该不超过一分钟。

1. 关闭 **Cloud Shell** 窗格。


#### 任务 4：查看部署结果。

1. 在 Azure 门户的中心菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡中，单击 **AADesignLab0502-RG**。

1. 在 **AADesignLab0502-RG** 边栏选项卡中，单击代表你在上一任务中创建的 Azure Web 应用程序的条目。

1. 在 Web 应用程序边栏选项卡中，单击顶部的 **浏览** 按钮。

    > **注**：如果未显示该应用程序，请切换到 Web 应用程序边栏选项卡，然后单击位于顶部的 **重新开始** 按钮，然后再次单击 **浏览**。 

1. 查看从 Docker Hub 部署的博客应用程序。 

1. 关闭新的浏览器选项卡并返回显示 Azure 门户的浏览器选项卡。

> **回顾**：在本练习中，你使用 Azure 资源管理器模板和 Docker Hub 映像将代码部署到应用服务 Web 应用。


## 练习 3：部署函数应用

#### 任务 1：使用 Azure 资源管理器模板通过代码部署函数应用

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **新建** 边栏选项卡顶部的 **搜索市场** 文本框中，输入 **模板部署**，然后按 **Enter** 键。

1. 在 **所有** 边栏选项卡中的搜索结果部分，单击 **模板部署**。

1. 在 **模板部署** 边栏选项卡中，单击 **创建** 按钮。

1. 在 **自定义部署** 边栏选项卡中，单击 **在编辑器中构建自己的模板** 链接。

1. 在 **编辑模板** 边栏选项卡中，单击 **快速启动模板** 链接。

1. 在 **加载快速启动模板** 窗格中的 **选择一个模板** 下拉列表中，选择 **201-function-app-dedicated-github-deploy** 模板。

1. 单击 **确定** 按钮。

1. 回到 **编辑模板** 边栏选项卡，单击 **保存** 按钮以保留模板。

1. 回到 **自定义部署** 边栏选项卡，执行以下任务：

    - 将 **订阅** 下拉列表条目保留其默认值。

    - 在 **资源组** 部分中，确保已选中 **新建** 选择选项，然后在下面的文本框中输入 **AADesignLab0503-RG**。

    - 在 **应用名称** 在文本框中，输入新函数应用的唯一名称。

    - 在 **SKU** 下拉列表，选择 **基本** 选项。

    - 保留 **辅助角色大小** 下拉列表的默认值。

    - 保留 **存储帐户类型** 下拉列表的默认值。

    - 保留 **存储库 URL** 字段的默认值。

    - 保留 **分支** 文本框的默认值。

    - 保留 **位置** 文本框的默认值。

    - 在 **条款和条件** 部分，选中 **我同意上述条款和条件** 复选框。

    - 单击 **购买** 按钮。

1. 在继续执行下一个任务之前，请等待部署完成。

    > **注**：部署大约需要一分钟。


#### 任务 2：查看部署结果。

1. 在 Azure 门户的中心菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡中，单击 **AADesignLab0503-RG**。

1. 在 **AADesignLab0503-RG** 边栏选项卡，单击代表你在上一任务中创建的函数应用的条目。

1. 在函数应用边栏选项卡上，找到 **URL** 条目并单击下面的超链接，以在新浏览器选项卡中查看函数应用登录页面。

1. 关闭新的浏览器选项卡并返回显示 Azure 门户的浏览器选项卡。

> **回顾**：在本练习中，你使用 Azure 资源管理器模板部署了函数应用和代码。


## 练习 4：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标以打开 Cloud Shell 窗格。

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以列出你在本实验中创建的所有资源组：

```sh
    az group list --query "[?starts_with(name,'AADesignLab05')]".name --output tsv
```

1. 验证输出是否仅包含你在本实验室中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符中输入以下命令并按 **Enter** 键以删除你在本实验室中创建的资源组

```sh
    az group list --query "[?starts_with(name,'AADesignLab05')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

1. 关闭门户网站的底部的 **Cloud Shell** 提示符。


> **回顾**：在本练习中，你删除了本实验室中使用的资源。
