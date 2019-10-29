---
lab:
    title: '保护 Azure 中的机密'
    module: '模块 1：管理 Azure 解决方案的安全性和标识'
---

# 管理 Azure 解决方案的安全性和标识 

# 实验室答案密钥：保护 Azure 中的机密

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

## 练习 1：部署密钥保管库资源

#### 任务 1：打开 Azure 门户

1. 单击任务栏中的 **Microsoft Edge** 图标。

1. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>)。

1. 出现提示时，使用你将在本实验室中使用的 Azure 订阅中具有所有者角色的用户帐户进行身份验证。

#### 任务 2：部署密钥保管库

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **密钥保管库** 并按 **Enter 键**。

1. 在 **全部** 边栏选项卡上，在搜索结果中，单击 **密钥保管库**。

1. 在 **密钥保管库** 边栏选项卡上，单击 **创建** 按钮。

1. 在 **创建密钥保管库** 边栏选项卡上，执行以下任务：

    - 在 **名称** 文本框中输入全球唯一值。

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，确保 **创建新的** 选择选项，然后在下方文本框中输入 **AADesignLab0901-RG**。

    - 在 **位置** 下拉列表中，选择你想要在本实验室中部署资源的 Azure 区域。

    - 单击 **定价等级**，在 **定价等级** 边栏选项卡上，单击 **A1 标准**，然后单击 **选择**。

    - 将其余所有设置均设置为默认值。

    - 单击 **创建** 按钮。

1. 请等待配置完成，再执行下一个任务。

#### 任务 3：通过 Azure 门户将机密添加到密钥保管库

1. 在 Azure 门户的主菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡上，单击 **AADesignLab0901-RG**。

1. 在 **AADesignLab0901-RG** 边栏选项卡上，单击表示新创建密钥保管库的条目。

1. 在“密钥保管库”边栏选项卡上，单击 **机密**。

1. 在“密钥保管库机密”边栏选项卡上，单击窗格顶部的 **生成/导入** 按钮。

1. 在 **创建机密** 边栏选项卡上，执行以下任务：

    - 在 **上传选项** 下拉列表中，确保选中 **手动** 条目。

    - 在 **名称** 文本框中输入 **thirdPartyKey**。

    - 在 **值** 文本框中输入值 **56d95961e597ed0f04b76e58**。

    - 将其余所有设置均设置为默认值。

    - 单击 **创建** 按钮。

#### 任务 4：打开 loud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

    > **注意**：**Cloud Shell** 图标是由 *大于* 和 *下划线* 字符组合构成的一个符号。

1. 如果这是你第一次使用订阅打开 **Cloud Shell**，你将看到 **Cloud Shell** 首次使用的配置向导。出现提示时，在 **欢迎使用Azure Cloud Shell** 窗格，单击 **Bash (Linux)**。

    > **注意**：如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你在本课程的实验室中使用现有订阅。如果是，请直接进入下一个任务。 

1. 在 **尚未安装存储资源** 窗格，单击 **显示高级设置**，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **Cloud Shell 区域** 下拉列表中，选择与你打算在本实验室中部署资源的位置匹配或接近的 Azure 区域

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下拉列表中选择 **AADesignLab0901-RG**。

    - 在 **存储帐户** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入由 3 到 24 个字符和数字组成的唯一名称。 

    - 在 **文件共享** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入 **cloudshell**。

    - 单击 **创建存储资源** 按钮。

1. 等待 **Cloud Shell** 完成其首次设置过程，然后进入下一个任务。

#### 任务 5：使用 CLI 将机密添加到密钥保管库

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，创建一个变量，其值用于指定包含你先前在本练习中部署的 Azure 密钥保管库的资源组的名称：

    ```sh
    RESOURCE_GROUP='AADesignLab0901-RG'
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，检索你先前在本练习中创建的 Azure 密钥保管库的名称：

    ```sh
    KEY_VAULT_NAME=$(az keyvault list --resource-group $RESOURCE_GROUP --query "[0].name" --output tsv)
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，列出密钥保险库中的秘密：

    ```sh
    az keyvault secret list --vault-name $KEY_VAULT_NAME
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，显示 **thirdPartyKey** 机密的值：

    ```sh
    az keyvault secret show --vault-name $KEY_VAULT_NAME --name thirdPartyKey --query value --output tsv
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，向密钥保管库添加新的机密：

    ```sh
    az keyvault secret set --vault-name $KEY_VAULT_NAME --name firstPartyKey --value 56f8a55119845511c81de488
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，列出密钥保管库中的机密：

    ```sh
    az keyvault secret list --vault-name $KEY_VAULT_NAME --query "[*].{Id:id,Created:attributes.created}" --out table
    ```

1. 关闭 **Cloud Shell** 窗格。

#### 任务 6：使用 Azure 资源管理器模板将机密添加到密钥保管库

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **模板部署** 并按 **Enter 键**。

1. 在 **全部** 边栏选项卡上，在搜索结果中，单击 **模板部署**。

1. 在 **模板部署** 边栏选项卡上，单击 **创建** 按钮。

1. 在 **自定义部署** 边栏选项卡上，单击 **在编辑器中构建自己的模板** 链接。

1. 在 **编辑模板** 边栏选项卡上，单击 **加载文件**。

1. 在 **选择需要上传的文件** 对话框中，导航到 **\\allfiles\\AZ-301T01\\Module_01\\LabFiles\\Starter\\** 文件夹，选择 **secret-template.json** 文件，然后单击 **打开**。这样就可以将以下内容加载到模板编辑器窗格中：

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vaultName": {
                "type": "string"
            }
        },
        "variables": {
            "secretName": "vmPassword"
        },
        "resources": [
            {
                "apiVersion": "2016-10-01",
                "type": "Microsoft.KeyVault/vaults/secrets",
                "name": "[concat(parameters('vaultName'), '/', variables('secretName'))]",
                "properties": {
                    "contentType": "text/plain",
                    "value": "StudentPa$$w.rd"
                }
            }
        ]
    }
    ```

1. 单击 **保存** 按钮保存模板。

1. 返回至 **自定义部署** 边栏选项卡，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下拉列表中选择 **AADesignLab0901-RG**。

    - 在 **保管库名称** 文本框中，输入你先前在本练习中创建的密钥保管库的名称。

    - 在 **条款和条件** 部分，选择 **我同意上述条款和条件** 复选框。

    - 单击 **购买** 按钮。

1. 继续下一步，无需等待部署完成。

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **模板部署** 并按 **Enter 键**。

1. 在 **全部** 边栏选项卡上，在搜索结果中，单击 **模板部署**。

1. 在 **模板部署** 边栏选项卡上，单击 **创建** 按钮。

1. 在 **自定义部署** 边栏选项卡上，单击 **在编辑器中构建自己的模板** 链接。

1. 在 **编辑模板** 边栏选项卡上，单击 **加载文件**。

1. 在 **选择需要上传的文件** 对话框中，导航到 **\\allfiles\\AZ-301T01\\Module_01\\LabFiles\\Starter\\** 文件夹，选择 **storage-template.json** 文件，然后单击 **打开**。这样就可以将以下内容加载到模板编辑器窗格中：

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "vaultName": {
                "type": "string"
            }
        },
        "variables": {
            "secretName": "storageConnectionString",
            "storageName": "[concat('stor', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2017-10-01",
                "type": "Microsoft.Storage/storageAccounts",
                "name": "[variables('storageName')]",
                "location": "[resourceGroup().location]",
                "kind": "Storage",
                "sku": {
                    "name": "Standard_LRS"
                },
                "properties": {                
                }
            },
            {
                "apiVersion": "2016/10/1",
                "type": "Microsoft.KeyVault/vaults/secrets",
                "name": "[concat(parameters('vaultName'), '/', variables('secretName'))]",
                "dependsOn": [
                    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageName'))]"
                ],
                "properties": {
                    "contentType": "text/plain",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageName'), ';', 'AccountKey=', listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageName')), providers('Microsoft.Storage', 'storageAccounts').apiVersions[0]).keys[0].value, ';')]"
                }
            }
        ]
    }
    ```

1. 单击 **保存** 按钮保存模板。

1. 返回至 **自定义部署** 边栏选项卡，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下拉列表中选择 **AADesignLab0901-RG**。

    - 在 **保管库名称** 字段中，输入你先前在本练习中创建的密钥保管库的名称。

    - 在 **条款和条件** 部分，选择 **我同意上述条款和条件** 复选框。

    - 单击 **购买** 按钮。

1. 请等待部署完成，再执行下一个任务。

#### 任务 7：查看密钥保管库机密

1. 在 Azure 门户的主菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡上，单击 **AADesignLab0901-RG**。

1. 在 **AADesignLab0901-RG** 边栏选项卡上，单击表示你先前在本练习中创建的密钥保管库的条目。

1. 在“密钥保管库”边栏选项卡上，单击 **机密**。

1. 在“密钥保管库机密”边栏选项卡上，查看本实验室中创建的机密列表。

1. 单击表示 **vmPassword** 机密的条目。

1. 在 **vmPassword** 边栏选项卡上，单击表示机密当前版本的条目。

1. 在“机密版本”边栏选项卡上，单击 **显示机密值** 按钮。

1. 验证机密的值是否与你在上一个任务中部署的模板中包含的值相匹配。

> **回顾**：在本练习中，你创建了一个 **密钥保管库** 实例并使用了几种不同的方法将机密添加到密钥保管库。


## 练习 2：使用密钥保管库机密部署 Azure VM

#### 任务 1：检索密钥保管库资源 Id 参数的值

1. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 Clould Shell 实例。

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，创建一个变量，其值用于指定将包含中心虚拟网络的资源组的名称：

    ```
    RESOURCE_GROUP='AADesignLab0901-RG'
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，检索你先前在本练习中创建的 Azure 密钥保管库的资源 ID：

    ```sh
    KEY_VAULT_ID=$(az keyvault list --resource-group $RESOURCE_GROUP --query "[0].id" --output tsv)
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，创建一个变量，其值用于指定 Azure 密钥保管库资源 ID 的名称，其中考虑了资源 ID 可能包含的任何特殊字符：

    ```sh
    KEY_VAULT_ID_REGEX="$(echo $KEY_VAULT_ID | sed -e 's/\\/\\\\/g; s/\//\\\//g; s/&/\\\&/g')"
    ```

#### 任务 2：准备 Azure 资源管理器部署模板和参数文件

1. 在 **Cloud Shell** 窗格，单击 **上传/下载文件** 图标，然后在下拉菜单中单击 **上传**。 

1. 在 **打开** 对话框中，导航到 **\\allfiles\\AZ-301T01\\Module_01\\LabFiles\\Starter\\** 文件夹，选择 **vm-template.json** 文件，然后单击 **打开**。 

1. 在 **Cloud Shell** 窗格，单击 **上传/下载文件** 图标，然后在下拉菜单中单击 **上传**。 

1. 在 **打开** 对话框中，导航到 **\\allfiles\\AZ-301T01\\Module_01\\LabFiles\\Starter\\** 文件夹，选择 **vm-template.parameters.json** 文件，然后单击 **打开**。 

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，使用 **$KEY_VAULT_ID** 变量的值替换 **vm-template.parameters.json** 参数文件中 **$KEY_VAULT_ID** 参数占位符：

    ```sh
    sed -i.bak1 's/"$KEY_VAULT_ID"/"'"$KEY_VAULT_ID_REGEX"'"/' ~/vm-template.parameters.json
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，验证参数文件中的占位符是否替换成功：

    ```sh
    cat ~/vm-template.parameters.json
    ```

#### 任务 3：配置密钥保管库以部署 Azure 资源管理器模板

1. 在 Azure 门户的主菜单中，单击 **资源组**。

1. 在 **资源组** 边栏选项卡上，单击 **AADesignLab0901-RG**。

1. 在 **AADesignLab0901-RG** 边栏选项卡上，单击表示你在上一个练习中创建的密钥保管库的条目。

1. 在“密钥保管库”边栏选项卡上，单击 **访问策略**。

1. 在 **访问策略** 边栏选项卡上，单击 **单击显示高级访问策略** 链接。

1. 选择 **启用对 Azure 资源管理器的访问以进行模板部署** 复选框。

1. 单击窗格顶部的 **保存** 按钮。

#### 任务 4：部署通过密钥保管库机密设置密码参数的 Linux VM。

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，使用指定参数文件部署 Azure 资源管理器模板：

    ```sh
    az group deployment create --resource-group $RESOURCE_GROUP --template-file ~/vm-template.json --parameters @~/vm-template.parameters.json
    ```

1. 请等待部署完成，再执行下一个任务。

#### 任务 5：验证部署结果

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，创建一个变量，其值用于指定包含新部署的 Azure VM 的资源组的名称：

    ```
    RESOURCE_GROUP='AADesignLab0901-RG'
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，检索包含存储本地管理员帐户密码值的密码的 Azure 密钥保管库的名称：

    ```sh
    KEY_VAULT_NAME=$(az keyvault list --resource-group $RESOURCE_GROUP --query "[0].name" --output tsv)
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，检索机密的值：

    ```sh
    az keyvault secret show --vault-name $KEY_VAULT_NAME --name vmPassword --query value --output tsv
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，检索你在上一个任务中部署的 Azure VM 的公共 IP 地址：

    ```sh
    PUBLIC_IP=$(az network public-ip list --resource-group $RESOURCE_GROUP --query "[0].ipAddress" --output tsv)
    ```

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，通过 SSH 连接到 Azure VM：

    ```sh
    ssh Student@$PUBLIC_IP
    ```

1. 在 **Cloud Shell** 命令提示符处，当系统提示你是否要继续连接时，输入`yes`并按 **Enter 键**。

1. 在 **Cloud Shell** 命令提示符处，当系统提示输入密码时，输入你先前在本任务中检索到的机密值，然后按 **Enter 键**。

1. 验证你是否已经成功通过身份验证。

1. 在 **Cloud Shell** 命令提示符处，输入 `exit`从Azure VM 注销。

> **回顾**：在本练习中，你使用存储为密钥保管库机密的密码部署了 Linux VM。

## 练习 3：删除实验室资源

#### 任务 1：打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，列出你在本实验室中创建的所有资源组：

    ```
    az group list --query "[?starts_with(name,'AADesignLab09')]".name --output tsv
    ```

1. 验证输出结果中是否仅包含你在本实验室中创建的资源组。这些组将在下一个任务中删除。

#### 任务 2：删除资源组

1. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter 键**，删除你在本实验室中创建的资源组

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab09')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **回顾**：在本练习中，你删除了本实验室中使用的资源。
