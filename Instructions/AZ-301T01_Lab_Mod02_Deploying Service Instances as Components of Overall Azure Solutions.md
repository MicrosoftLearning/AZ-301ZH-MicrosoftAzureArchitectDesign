---
lab:
    title: '将服务实例部署为整体 Azure 解决方案的组件'
    module: '模块 2：集成 Azure 平台上提供的 SaaS 服务'
---

# 集成 Azure 平台上提供的 SaaS 服务

# 实验室答案密钥：将服务实例部署为整体 Azure 解决方案的组件

## 在开始之前，

1. 请确保使用以下凭证登录到 Windows 10 实验室虚拟机：

    - 用户名称：**Admin**

    - 密码：**Pa55w.rd**

1. 查看位于 Windows 10 桌面底部的任务栏。任务栏包含你在实验中常用的应用程序图标：

    - Microsoft Edge

    - 文件资源管理器

    - [Visual Studio Code](https://code.visualstudio.com/)

    - [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

    - Bash on Ubuntu on Windows

    - Windows PowerShell

    > **注意**：你还可以在 **开始菜单** 中找到这些应用程序的快捷方式。

## 练习 1：使用 ARM 模板部署函数应用程序和认知服务

#### 任务 1：打开 Azure 门户

1. 单击任务栏中的 **Microsoft Edge** 图标。

1. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>)。

1. 出现提示时，使用你将在本实验室中使用的 Azure 订阅中具有所有者角色的用户帐户进行身份验证。

#### 任务 2：使用 Azure 资源管理器模板部署认知服务

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **模板部署** 并按 **Enter 键**。

1. 在 **“所有内容”** 边栏选项卡的搜索结果中，单击 **“模板部署(使用自定义模板部署)”**。

1. 在 **“模板部署(使用自定义模板部署)”** 边栏选项卡中，单击 **“创建”** 按钮。

1. 在 **自定义部署** 边栏选项卡上，单击 **在编辑器中构建自己的模板** 链接。

1. 在 **编辑模板** 边栏选项卡上，单击 **加载文件**。

1. 在 **选择需要上传的文件** 对话框中，导航到 **\\allfiles\\AZ-301T01\\Module_02\\LabFiles\\Starter\\** 文件夹，选择 **cognitive-template.json** 文件，然后单击 **打开**。这样就可以将以下内容加载到模板编辑器窗格中：

```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "variables": {
            "serviceName": "[concat('cgnt', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2017-04-18",
                "type": "Microsoft.CognitiveServices/accounts",
                "name": "[variables('serviceName')]",
                "kind": "TextAnalytics",
                "location": "[resourceGroup().location]",
                "sku": {
                    "name": "S1"
                },
                "properties": {}
            }
        ],
        "outputs": {
            "cognitiveEndpointUrl": {
                "type": "string",
                "value": "[reference(variables('serviceName')).endpoint]"
            },
            "cognitiveEndpointKey": {
                "type": "string",
                "value": "[listKeys(variables('serviceName'), '2017-04-18').key1]"
            }
        }
    }
```

1. 单击 **保存** 按钮保存模板。

1. 返回至 **自定义部署** 边栏选项卡，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，确保选中 **创建新的** 选项，然后在文本框中输入 **AADesignLab1001-RG**。

    - 在 **位置** 下拉列表中，选择你想要在本实验室中部署资源的 Azure 区域。

    - 在 **条款和条件** 部分，选择 **我同意上述条款和条件** 复选框。

    - 单击 **购买** 按钮。

1. 请等待部署完成，再继续下一步。 

1. 在 Azure 门户的主菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡上，单击 **AADesignLab1001-RG**。

1. 在 **AADesignLab1001-RG** 边栏选项卡上，找到边栏选项卡顶部的 **部署** 标题，然后单击下方的 **部署** 标签，该标签指示成功部署的数量。

1. 在“部署”边栏选项卡上，单击最新部署的名称。

1. 在 **“Microsoft.Template-概述”** 边栏选项卡上，单击 **“输出”**。 

1. 在 **“Microsoft.Template - 输出”** 边栏选项卡上，识别 **“cognitiveEndpointUrl”** 和 **“cognitiveEndpointKey”** 输出的值。将这些值记录下来，稍后在实验中使用。

#### 任务 3：部署功能应用程序

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **功能应用程序** 并按 **Enter 键**。

1. 在 **全部** 边栏选项卡上，在搜索结果中，单击 **功能应用程序**。

1. 在 **功能应用程序** 边栏选项卡上，单击 **创建** 按钮。

1. 在下一个 **功能应用程序** 边栏选项卡上，执行以下任务：

    - 在 **应用程序名称** 文本框中输入全球唯一名称。
    
    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下拉列表中选择 **AADesignLab1001-RG**。

    - 在 **OS** 部分，确保选中 **Windows** 按钮。

    - 在 **主机计划** 下拉列表中，确保选中 **消耗计划** 条目。

    - 在 **位置** 下拉列表中，选择你在上一个任务中部署了认知服务实例的 Azure 区域。
    
    - 在 **“运行时堆栈”** 下拉列表中，确保选中 **“NET Core”** 条目。 

    - 在 **存储** 部分，确保选中 **创建新的** 选项，然后接受存储帐户名称的默认值。 

    - 在 **“Application Insights”** 部分，将选项设置为 **“禁用”**。

    - 单击 **创建** 按钮。

1. 请等待功能应用程序配置完成，然后再继续下一步。 

1. 在 Azure 门户的主菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡上，单击 **AADesignLab1001-RG**。

1. 在 **AADesignLab1001-RG** 边栏选项卡上，在资源列表中，单击新配置的功能应用程序。

1. 在“功能应用程序”边栏选项卡上，单击边栏选项卡顶部的 **平台功能** 选项卡。

1. 在 **“平台功能”** 选项卡上，单击 **“常规设置”** 部分的 **“配置”** 链接。

1. 在 **“应用程序设置”** 选项卡上，找到 **“应用程序设置”** 部分。单击 **“+ 新建应用程序设置”** 链接并执行以下任务：

    - 在 **“名称”** 文本框中，输入 **“EndpointUrl”**。

    - 在 **“值”** 文本框中，输入先前识别的 **“cognitiveEndpointUrl”** 的值，然后输入 **“text/analytics/v2.0”**。

1. 在 **应用程序设置** 部分，再次单击 **添加新的设置** 并执行以下任务：

    - 在 **输入名称** 文本框中输入 **EndpointKey**。

    - 在 **“输入值”** 文本框中，输入你先前识别的 **“cognitiveEndpointKey”** 的值。

1. 单击 **应用程序设置** 选项卡顶部的 **保存** 按钮。

1. 导航回到 **“函数应用”** 边栏选项卡，然后在 **“平台功能”** 选项卡中，单击 **“代码部署”** 部分的 **“部署中心”** 链接。

1. 在出现的 **“部署中心”** 边栏选项卡上，单击 **“外部”**，然后单击 **“继续”**。

1. 单击 **“应用服务生成服务”** 并单击 **“继续”**。 

1. 显示 **代码** 部分后，执行以下任务

    - 在 **“存储库”** 文本框中键入 **“https://github.com/azure-labs/cognitive-services-function”**。

    - 在 **分支** 文本框中输入 **master**。
    
        **注意**：分支字段需区分大小写。

    - 将 **“存储库类型”** 的值设置为 **“Git”**。
    
    - 将 **“专用存储库”** 的值设置为 **“否”**。

    - 单击 **继续** 按钮。

1. 单击 **完成** 并等待部署完成，然后再继续执行下一个任务。 

    > **注意**：通过监控 **部署** 选项卡，你将能够确定第一个部署是否已经完成。此选项卡会自动更新。

#### 任务 4：使用认知服务测试功能应用程序

1. 返回“功能应用程序”边栏选项卡，单击 **功能**，展开功能列表。

    > **注意**：你可能需要双击 **功能** 才能刷新功能列表。

1. 从功能列表中选择 **DetermineLanguage** 功能。

1. 在打开的 **run.csx** 窗格中，单击窗格右侧的 **测试**。

1. 在 **测试** 窗格中，执行以下任务：

    - 在 **请求正文** 文本框中输入以下内容：

```json
    {
        "text": "I stuffed a shirt or two into my old carpet-bag, tucked it under my arm, and started for Cape Horn and the Pacific."
    }
```

    - 单击 **运行** 按钮。

    - 查看 **输出** 部分的输出结果。输出结果应将语言标识为 **en** （英语）。

> **回顾**：在本练习中，你创建了一个使用 Azure 认知服务的功能应用程序。


## 练习 2：创建使用功能应用程序的逻辑应用程序

#### 任务 1：创建一个逻辑应用程序

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **逻辑应用程序** 并按 **Enter 键**。

1. 在 **全部** 边栏选项卡上，在搜索结果中，单击**逻辑应用程序**。

1. 在 **逻辑应用程序** 边栏选项卡上，单击 **创建** 按钮。

1. 在 **创建逻辑应用程序** 边栏选项卡上，执行以下任务：

    - 在 **名称** 文本框中输入值 **CognitiveWorkflow**。
    
    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下拉列表中选择 **AADesignLab1001-RG**。

    - 在 **位置** 下拉列表中，选择你在本实验室上一个练习中选择的同一 Azure 区域。 

    - 在 **日志分析** 部分，确保选中 **关闭** 按钮。

    - 单击 **创建** 按钮。

1. 请等待配置完成，再执行下一个任务。 

#### 任务 2：配置逻辑应用步骤

1. 在 Azure 门户的主菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡上，单击 **AADesignLab1001-RG**。

1. 在 **AADesignLab1001-RG** 边栏选项卡上，单击表示你在上一个任务中创建的逻辑应用程序的条目。

1. 在 **逻辑应用设计器** 边栏选项卡上，向下滚动并单击 **模板** 部分的 **空白逻辑应用** 图块。

1. 在 **逻辑应用程序设计器** 边栏选项卡上，单击窗格顶部的 **代码检视** 按钮。

1. 在 **逻辑应用程序设计器** 边栏选项卡上，查看空白的逻辑应用程序 JSON 模板：

```json
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {},
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {},
            "triggers": {}
        }
    }
```

1. 使用包含 HTTP 触发器的以下模板替换默认 JSON 模板 (****\\allfiles\\AZ-301T01\\Module_02\\LabFiles\\Starter\\logic-app.json**)：

```json
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {},
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {},
            "triggers": {
                "manual": {
                    "inputs": {
                        "method": "POST",
                        "schema": {
                            "properties": {
                                "text": {
                                    "type": "string"
                                }
                            },
                            "type": "object"
                        }
                    },
                    "kind": "Http",
                    "type": "Request"
                }
            }
        }
    }
```

1. 在 **逻辑应用程序设计器** 边栏选项卡上，单击 **设计器** 按钮。

    > **注意**：此时，你应该在设计器中看到一个单步操作。这就是开始工作流程的“触发器”步骤。

1. 单击设计器中的 **+ 新建步骤** 按钮。

1. 在 **选择操作** 部分，执行以下任务：

    - 在搜索文本框中输入 **Azure Functions**。

    - 在搜索结果中，选择名为 **选择 Azure Functions** 的操作。

    - 在下一组搜索结果中，选择你在本实验室上一个练习中创建的 Azure Functions 实例。

    - 在最后一组搜索结果中，选择将用于该操作的 **DetermineLanguage** 函数。

1. 在 **DetermineLanguage** 步骤中，执行以下任务：

    - 单击 **显示高级选项** 链接，显示所有选项。

    - 在 **请求正文** 文本框中输入 **@triggerBody()**。

    - 在 **方法** 下拉列表中，选择 **粘贴** 选项。

1. 单击设计器中的 **+ 新建步骤** 按钮。单击 **添加操作** 按钮，打开用于创建操作的对话框。

1. 在显示的 **选择操作** 对话框中，执行以下任务：

    - 在搜索文本框中输入 **Azure Functions**。

    - 在搜索结果中，选择名为 **选择 Azure Functions ** 的操作。

    - 在下一组搜索结果中，选择你在本实验室上一个练习中创建的 Azure Functions 实例。

    - 在最后一组搜索结果中，选择将用于该操作的 **DetermineKeyPhrases** 函数。

1. 在 **DetermineKeyPhrases** 步骤中，执行以下任务：

    - 单击 **显示高级选项** 链接，显示所有选项。

    - 在 **请求正文** 文本框中输入值 **@body('DetermineLanguage')**。

    - 在 **方法** 下拉列表中，选择 **粘贴** 选项。

1. 单击设计器中的 **+ 新建步骤** 按钮。 

1. 在显示的 **选择操作** 对话框中，执行以下任务：

    - 在搜索文本框中输入 **响应**。

    - 在搜索结果中，选择名为 **响应请求** 的 **行动**。

1. 在 **响应** 步骤中，执行以下任务：

    - 在 **状态代码** 文本框中，确保指定值为 **200**。

    - 在 **本文** 文本框中输入 **@body('DetermineKeyPhrases')**。

1. 在 **逻辑应用程序设计器** 边栏选项卡的顶部，单击 **保存** 按钮保存工作流程。

1. 滚动到 **逻辑应用程序设计器** 区域的顶部，然后单击 **收到 HTTP 请求时** 步骤。

1. 复制 **HTTP POST URL** 文本框的值。此 URL 稍后将在本实验室中使用。

#### 任务 2：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

    > **注意**：**Cloud Shell** 图标是由 *大于* 和 *下划线* 字符组合构成的一个符号。

1. 如果这是你第一次使用订阅打开 **Cloud Shell**，你将看到 **Cloud Shell** 首次使用的配置向导。出现提示时，在 **欢迎使用 Azure Cloud Shell** 窗格，单击 **Bash (Linux)**。

    > **注意**：如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你在本课程的实验室中使用现有订阅。如果是，请直接进入下一个任务。 

1. 在 **尚未安装存储资源** 窗格，单击 **显示高级设置**，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **Cloud Shell 区域** 下拉列表中，选择与你在本实验室中部署资源的位置匹配或接近的 Azure 区域

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下拉列表中选择 **AADesignLab1001-RG**。

    - 在 **存储帐户** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入由 3 到 24 个字符和数字组成的唯一名称。 

    - 在 **文件共享** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入 **cloudshell**。

    - 单击 **创建存储资源** 按钮。

1. 等待 **Cloud Shell** 完成其首次设置过程，然后进入下一个任务。

#### 任务 3：使用 Python 验证逻辑应用程序

1. 在门户底部的 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，打开交互式 **python** 终端：

```
    python
```

1. 在门户底部的 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，导入 **请求** 库：

```python
    import requests
```

1. 在门户底部的 **Cloud Shell** 命令提示符处，输入以下命令（将占位符`<logic app POST Url>`替换为本实验室中先前记录的url值）并按 **Enter 键**，创建一个包含你逻辑应用程序 url 值的变量：

```python
    url = "<logic app POST Url>"
```

1. 在门户底部的 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，发送 HTTP POST 请求以触发逻辑应用程序工作流程：

```python
    response = requests.post(url, json={'text': 'Circumambulate the city of a dreamy Sabbath afternoon.Go from Corlears Hook to Coenties Slip, and from thence, by Whitehall, northward.'})
```

1. 在门户底部的 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，显示逻辑应用程序工作流程的输出结果：

```python
    print(response.status_code, response.reason, response.text)
```

1.  关闭 **Cloud Shell** 窗格。

> **回顾**：在本练习中，你创建了一个利用在本实验室上一个练习中创建的功能应用程序的逻辑应用程序。

## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，列出你在本实验室中创建的所有资源组：

```
    az group list --query "[?starts_with(name,'AADesignLab10')]".name --output tsv
```

1. 验证输出结果中是否仅包含你在本实验室中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，删除你在本实验室中创建的资源组

```sh
    az group list --query "[?starts_with(name,'AADesignLab10')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **回顾**：在本练习中，你删除了本实验室中使用的资源。
