---
lab:
    title: '使用 Azure ARM 模板和 Azure 构建块构建基于 Azure IaaS 的服务器应用程序'
    module: '模块 2：构建基于 Azure IaaS 的服务器应用程序 (ADSK)'
---

# 构建基于 Azure IaaS 的服务器应用程序
# 验室答案密钥: 使用 Azure ARM 模板和 Azure 构建块构建基于 Azure IaaS 的服务器应用程序.

## 在开始之前

1. 请确保使用以下凭证登录到 Windows 10 实验室虚拟机：

    - 用户名: **Admin**

    - 密码: **Pa55w.rd**

1. 查看位于 Windows 10 桌面底部的任务栏。 任务栏包含你在实验室中常用的应用程序图标：

    - Microsoft Edge

    - 文件资源管理器

    - [Visual Studio Code](https://code.visualstudio.com/)

    - [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

    - Bash on Ubuntu on Windows

    - Windows PowerShell

    > **注意**: 你还可以在 **开始菜单** 中找到这些应用程序的快捷方式。


## 练习 1: 使用 Azure 资源管理器模板和 PowerShell 期望状态配置 (DSC) 扩展，从 Azure 门户部署 Azure VM。

#### 任务 1: 打开 Azure 门户

1. 单击在任务栏中的 **Microsoft Edge** 图标。

2. 在打开的浏览器窗口中，导航到 **Azure 门户**  (<https://portal.azure.com>).

3. 出现提示时，使用你将在本实验室中使用的 Azure 订阅中具有所有者角色的用户账户进行身份验证。

#### 任务 2: 创建运行 Windows Server 2016 数据中心的 Azure VM。

1. 在 Azure 门户的左上角，单击 **创建资源**。

1. 在 **“新建”** 边栏选项卡顶部的 **“搜索市场”** 文本框中，输入 **“Windows Server”** 并按 Enter。

1. 在 **“所有内容”** 边栏选项卡的搜索结果中，单击 **“Windows Server”**。

1. 在 **“Windows Server”** 边栏选项卡上，选择 **“[smalldisk] Windows Server 2016 数据中心”** 软件计划，然后单击 **“创建”** 按钮。

5. 在 **基础** 选项卡上，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。
    
    - 在 **资源组** 部分，单击 **创建新的**，在文本框中输入 **AADesignLab0301-RG**，然后单击 **确定**。
      
    - 在 **名称** 文本框中输入值 **lab03vm0**。

    - 在 **区域** 下拉列表中，选择你想要在本实验室中部署资源的 Azure 区域。
    
    - 在 **可用性选项** 下拉列表中，选择 **可用性集**。

    - 在 **可用性集** 部分，单击 **创建新的**，然后输入值 **lab03avset0**，将 **故障域** 设置为最大值，将 **更新域** 设置为默认值，然后单击 **确定**。

    - 将 **图像** 下拉列表中的条目设置为默认值。

    - 确保将大小设置为 **标准 DS1 v2**。

    - 在 **用户名** 文本框中输入值 **Student**。

    - 在 **密码** 和 **确认密码** 文本框中输入值 **Pa55w.rd1234**。

    - 在 **公共入站端口** 部分，选择 **允许选定端口** 选项，然后在 **选择入站端口** 下拉列表中选择 **HTTP**。

    - 将 **已经拥有 Windows 许可证？** 选项设置为 **否**。
    
    - 单击 **下一步： 磁盘 >**
    
6. 在 **磁盘** 选项卡上，执行以下任务：

    - 确保 **OS 磁盘类型** 下拉列表条目设置为 **高级 SSD** 

    - 单击 **下一步： 网络 >**
    
7. 在 **网络** 选项卡上，执行以下任务： 

    - 在 **虚拟网络** 部分，单击 **创建新的**。 
    
    - 在 **创建虚拟网络** 边栏选项卡上，指定以下设置并单击 **确定**：

        - 在 **名称** 文本框中，输入值 **lab03vnet0**。

        - 在 **地址范围** 文本框中，输入值 **10.3.0.0/16**。

        - 在 **子网名称** 文本框中，输入值 **subnet-0**。

        - 在 **子网地址范围** 文本框中，输入值 **10.3.0.0/24**，然后单击 **确定**。

    - 将 **公共 IP** 条目设置为默认值。
    
    - 将 **NIC 网络安全组** 选项设置为 **基础**。
    
    - 将 **公共入站端口** 选项设置为 **允许选定端口**
    
    - 将 **选择入站端口** 条目设置为 **HTTP**

    - 将 **加速网络** 条目设置为默认值。

    - 单击 **下一步： 管理 >**
    
8. 在 **管理** 选项卡上，执行以下任务： 

    - 将 **开机诊断** 选项设置为默认值。

    - 将 **OS 访客诊断** 选项设置为默认值。

    - 将 **诊断存储账户** 条目设置为默认值。
    
    - 将 **系统分配托管标识** 选项设置为默认值。
    
    - 将 **启用自动关闭** 选项设置为默认值。

    - 将 **启用备份** 选项设置为默认值。

    - 单击 **查看 + 创建** 按钮。
    
9. 在 **创建虚拟机** 边栏选项卡上，查看新虚拟机的设置，然后单击 **创建** 按钮。

10. 继续下一个任务，无需等待部署完成。


#### 任务 3: 查看 DSC 配置文件

1. 在任务栏上，单击 **文件资源管理器** 图标。

2. 在出现的 **文件资源管理器** 窗口中，导航到 **\\allfiles\\AZ-301T04\\Module_02\\LabFiles\\Starter\\** 文件夹。

3. 右键单击 **IISWebServer.zip** 文件，然后选择 **解压所有...** 选项。

4. 在 **解压压缩文件夹** 对话框中，执行以下任务：

    - 在 **将文件解压缩到此文件夹：** 字段中，输入你想要将文件解压到其中的文件夹的名称。

    - 确保选中 **完成时显示解压的文件** 复选框。
    
    - 单击 **解压** 按钮。

5. 在出现的新 **文件资源管理器** 窗口中，右键单击 **IISWebServer.ps1** 文件并选择 **使用代码打开** 选项，启动 **Visual Studio 代码** 应用程序。

6. 在出现的 **Visual Studio 代码** 窗口中，查看 PowerShell 脚本的内容。

7. 在 **Visual Studio 代码** 窗口的顶部，单击 **文件** 菜单并选择 **关闭窗口** 选项。

8. 关闭 **文件资源管理器** 窗口。

9. 返回 **Microsoft Edge** 窗口，不必关闭 **Azure 门户**。


#### 任务 4: 创建 Azure 存储账户

1. 在 Azure 门户的左上角，单击 **创建资源**。

2. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **存储账户** 并按 **Enter 键**。

3. 在 **全部** 边栏选项卡上，在搜索结果中，单击 **存储账户**。

4. 在 **存储账户** 边栏选项卡上，单击 **创建** 按钮。

5. 在 **创建存储账户** 边栏选项卡上，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。
    
    - 在 **资源组** 部分，确保选中 **使用现有** 选项，然后在下方下拉列表中选择你先前在本练习中创建的资源组。

    - 在 **名称** 文本框中输入由 3 到 24 个字符和数字组合而成的唯一名称。
    
    - 将 **位置** 条目设置为你先前在本练习中选择的同一 Azure 区域。
    
    - 在 **性能** 部分，确保选中 **标准** 选项。
       

    - 在 **账户类型** 下拉列表中，确保选中 **存储（通用 v1）** 选项。
    
    - 在 **复制** 下拉列表中，选择 **本地冗余存储 (LRS)** 条目。

    - 单击 **查看 + 创建** 按钮，然后单击 **创建**。

3. 请等待部署完成，再执行下一个任务。

    > **注**: 此操作大约需要 2 分钟。


#### 任务 5: 将 DSC 配置上传到 Azure 存储

1. 在 Azure 门户的主菜单中，单击 **资源组**。

2. 在 **资源组** 边栏选项卡上，单击表示你将存储账户部署到其中的资源组的条目。

3. 在资源组边栏选项卡上，单击表示新创建的存储账户的条目。

4. 启用 **概述** 选项后，在“存储账户边”栏选项卡上，单击 **Blob**。

5. 单击边栏选项卡顶部的 **容器** 按钮 。

6. 在出现的 **新容器** 窗格中，指定以下设置并单击 **确定**：

    - 在 **名称** 文本框中，输入值 **config**。

    - 在 **公共访问级别** 列表中，选择 **Blob（仅限 blob 的匿名读取访问）** 选项。

7. 回到 **Blob 服务** 边栏选项卡，单击表示新 **配置** 容器的条目。

8. 在 **配置** 边栏选项卡上，单击边栏选项卡顶部的 **上传** 按钮。

9. 在 **上传 blob** 窗格中，执行以下任务：

    - 在 **文件** 字段中，单击该字段右侧的蓝色文件夹按钮。

    - 在出现的 **打开文件** 对话框中，导航到 **\\allfiles\\AZ-301T04\\Module_02\\LabFiles\\Starter\\** 文件夹。

    - 选择 **IISWebServer.zip** 文件。

    - 单击 **打开** 按钮关闭对话框并返回 **上传 blob** 弹出窗口。

    - 单击 **上传** 按钮。

10. 导航到 **配置** 边栏选项卡，然后单击表示 **IISWebServer.zip** blob 的条目。

11. 在出现的 **Blob 属性** 弹出窗口中，找到并记录 **URL** 属性的值。 此 URL 稍后将在本实验室中使用。


#### 任务 6：使用 Azure 资源管理器模板和 PowerShell DSC 扩展，从 Azure 门户部署 Azure VM。

1. 在 Azure 门户的左上角，单击 **创建资源**。

2. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **模板部署** 并按 **Enter 键**。

3. 在 **全部** 边栏选项卡上，在搜索结果中，单击 **模板部署**。

4. 在 **模板部署** 边栏选项卡上，单击 **创建** 按钮。

5. 在 **自定义部署** 边栏选项卡上，单击 **在编辑器中构建自己的模板** 链接。

6. 在 **编辑模板** 边栏选项卡上，单击 **加载文件**。

7. 在 **选择需要上传的文件** 对话框中，导航到 **\\allfiles\\AZ-301T04\\Module_02\\LabFiles\\Starter\\** 文件夹，选择 **dsc-extension-template.json** 文件，然后单击 **打开**。 这样就可以将以下内容加载到模板编辑器窗格中：

```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualMachineName": {
                "type": "string",
                "defaultValue": "lab03vm0"
            },
            "configurationModuleUrl": {
                "type": "string"
            },
            "extensionFunction": {
                "type": "string",
                "defaultValue": "IISWebServer.ps1\\IISWebServer"
            }
        },
        "resources": [
            {
                "apiVersion": "2018-06-01",
                "type": "Microsoft.Compute/virtualMachines/extensions",
                "name": "[concat(parameters('virtualMachineName'), '/dscExtension')]",
                "location": "[resourceGroup().location]",
                "properties": {
                    "publisher": "Microsoft.Powershell",
                    "type": "DSC",
                    "typeHandlerVersion": "2.75",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "ModulesUrl": "[parameters('configurationModuleUrl')]",
                        "ConfigurationFunction": "[parameters('extensionFunction')]",
                        "Properties": {
                            "MachineName": "[parameters('virtualMachineName')]"
                        }
                    },
                    "protectedSettings": null
                }
            }
        ]
    }
```

8. 单击 **保存** 按钮保存模板。

9. 返回至 **自定义部署** 边栏选项卡，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下方下拉列表中选择你先前在本练习中创建的资源组。

    - 将 **位置** 下拉列表设置为默认值。

    - 将 **虚拟机名称** 字段设置为默认值： **lab03vm0**.

    - 在 **配置模块 Url** 字段中，输入你在上一个任务中记录的 URL 值。

    - 将 **扩展功能** 字段设置为默认值： **IISWebServer.ps1\\IISWebServer**.

    - 在 **条款和条件** 部分，选择 **我同意上述条款和条件** 复选框。

    - 选择 **购买** 按钮。

10. 请等待 DSC 配置部署完成，再执行下一个任务。

    > **注**: DSC 配置部署最多可能需要十分钟。


#### 任务 7: 验证 Azure VM 是否正在为 Web内容提供服务

1. 在 Azure 门户的主菜单中，单击 **资源组**。

2. 在 **资源组** 边栏选项卡上，单击表示你将虚拟机部署到其中的资源组的条目。

3. 在“资源组”边栏选项卡上，单击表示你部署的 **虚拟机** 的条目。

4. 在 **虚拟机** 边栏选项卡上，找到 **公共IP地址** 条目，并确定其值。

5. 打开新的 Microsoft Edge 选项卡，然后导航到上一步中确定的 IP 地址。

6. 验证你是否能够访问默认的互联网信息服务网页。

7. 关闭新的浏览器选项卡。

> **回顾***: 在本练习中，你从 Azure 门户部署了 **虚拟机**，然后使用 **PowerShell DSC** 扩展以无人值守的方式将更改应用于虚拟机。


## 练习 2: 使用 Azure 资源管理器模板和 PowerShell 期望状态配置 (DSC) 扩展，从 Azure 门户部署 Azure 虚拟机规模集 (VMSS)。

#### 任务 1: 查看 Azure 资源管理器模板。

1. 在任务栏上，单击 **文件资源管理器** 图标。

2. 在出现的 **文件资源管理器** 窗口中，导航到 **\\allfiles\\AZ-301T04\\Module_02\\LabFiles\\Starter\\** 文件夹。

3. 右键单击 **vmss-template.json** 文件并选择 **使用代码打开** 选项，启动 **Visual Studio 代码** 应用程序。

4. 在出现的 **Visual Studio 代码** 窗口中，查看 JSON 文件的内容。

5. 在 **Visual Studio 代码** 窗口的顶部，单击 **文件** 菜单并选择 **关闭窗口** 选项。

6. 关闭 **文件资源管理器** 窗口。

7. 返回 **Microsoft Edge** 窗口，不必关闭 **Azure 门户**。


#### 任务 2: 使用 ARM 部署 VMSS

1. 在 Azure 门户的主菜单中，单击 **创建资源**。

2. 在 **新建** 边栏选项卡的顶部，在 **搜索市场** 文本框中输入 **模板部署** 并按 **Enter 键**。

3. 在 **全部** 边栏选项卡上，在搜索结果中，单击 **模板部署**。

4. 在 **模板部署** 边栏选项卡上，单击 **创建** 按钮。

5. 在 **模板部署** 边栏选项卡上，单击 **创建** 按钮。

6. 在 **编辑模板** 边栏选项卡上，单击 **加载文件**。

7. 在出现的 **“打开”** 文件对话框中，导航到 **“\\allfiles\\AZ-301T04\\Module_02\\LabFiles\\Starter\\”** 文件夹。

8. 选择 **vmss-template.json** 文件。

9. 单击 **打开** 按钮。

10. 返回 **编辑模板** 边栏选项卡，单击 **保存** 按钮保存模板。

11. 返回至 **自定义部署** 边栏选项卡，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 部分，选择 **创建新的** 选项，然后在文本框中输入 **AADesignLab0302-RG**。

    - 将 **位置** 条目设置为默认值。

    - 在 **管理员用户名** 文本框中，输入值 **Student**。

    - 在 **管理员密码** 文本框中，输入值 **Pa55w.rd1234**。

    - 在 **示例计数** 文本框中，输入值 **2**。

    - 将 **超额配置** 文本框设置为默认值： **true**.

    - 在 **配置模块 Url** 文本框中，输入你在本实验室上一个练习中为上传的 blob 所记录的 URL。

    - 在 **条款和条件** 部分，选择 **我同意上述条款和条件** 复选框。

    - 选择 **购买** 按钮。

12. 请等待部署完成，再执行下一个任务。


#### 任务 3: 验证 VMSS 实例是否正在为 Web 内容提供服务

1. 在 Azure 门户的主菜单中，单击 **资源组**。

2. 在 **资源组** 边栏选项卡上，单击表示你将虚拟机规模集部署到其中的资源组的条目。

3. 在“资源组”边栏选项卡上，单击 **公共 IP 地址** 类型的资源。

4. 在“公共 IP 地址”资源边栏选项卡，在 **Essentials** 部分，确定 **IP地址** 条目的值。

5. 打开新的 Microsoft Edge 选项卡，然后导航到上一步中确定的 IP 地址。

6. 验证你是否能够访问默认的互联网信息服务网页。

7. 关闭新的浏览器选项卡并返回浏览器选项卡，其中 **Azure 门户** 无需关闭。

> **回顾**: 在本练习中，你创建了一个虚拟机比例集，并使用 PowerShell DSC 配置了各个实例。


## 练习 3: 使用 Azure 构建块和 PowerShell 期望状态配置 (DSC) 扩展，从 Azure Cloud Shell 部署运行 Windows Server 2016 和 Linux 的 Azure VM。

#### 任务 1: 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

    > **注**: **Cloud Shell** 图标是由 *大于* 和 *下划线* 字符组合构成的一个符号。

2. 如果这是你第一次使用订阅打开 **Cloud Shell**，你将看到 **Cloud Shell** 首次使用的配置向导。 出现提示时，在 **欢迎使用 Azure Cloud Shell** 窗格，单击 **Bash (Linux)**。

    > **注**: 如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你在本课程的实验室中使用现有订阅。 如果是，请直接进入下一个任务。

3. 在 **尚未安装存储资源** 窗格，单击 **显示高级设置**，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **Cloud Shell 区域** 下拉列表中，选择与你打算在本实验室中部署资源的位置匹配或接近的 Azure 区域。

    - 在 **“资源组”** 部分，确保已选中 **“使用现有”** 选项，然后选择 **“AADesignLab0301-RG”**。

    - 在 **存储账户** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入由 3 到 24 个字符和数字组成的唯一名称。 

    - 在 **文件共享** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入 **cloudshell**。

    - 单击“创建存储资源”按钮。

4. 等待 **Cloud Shell** 完成其首次设置过程，然后进入下一个任务。


#### 任务  2: 在 Azure Cloud Shell 中安装Azure 构建块 npm 包

1. 在门户底部的 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建本地目录以安装 Azure 构建块 npm 包：

```sh
    mkdir ~/.npm-global
```

2. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，更新 npm 配置，将新的本地目录包含在内：

```sh
    npm config set prefix '~/.npm-global'
```

3. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，打开 ~./bashrc 配置文件进行编辑：

```sh
    vi ~/.bashrc
```

4. 在 **Cloud Shell**命令提示符处，在 vi 编辑器界面中，向下滚动到文件的底部（或输入 **G**），向右滚动到最后一行的最右侧字符（或输入 **$**）输入 **a** 进入 **插入** 模式，按 **Enter 键** 换行，然后输入以下内容将新创建的目录添加到系统路径：

```sh
    export PATH="$HOME/.npm-global/bin:$PATH"
```

5. 在 **Cloud Shell** 命令提示符处，在 vi 编辑器界面中，按 **Esc**，再按 **：**，输入 **wq!** 并按 **Enter 键**，保存更改并关闭文件。

6. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，安装 Azure 构建块 npm 包：

```sh
    npm install -g @mspnp/azure-building-blocks
```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，退出 shell：

```sh
    exit
```

8. 在 **Cloud Shell 超时** 窗格中，单击 **重新连接**。 

    > **注**: 你需要重新启动 Cloud Shell 才能使安装的构建块 npm 包生效。


#### 任务 3: 使用 Azure 构建块从 Cloud Shell 部署 Windows Server 2016 Azure VM

1. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，下载包含 Azure 构建块参考架构文件的 GitHub 存储库：

```
    git clone https://github.com/mspnp/reference-architectures.git
```

2.  在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，查看将用于此部署的 Azure 构建块参数文件的内容：

```sh
    cat ./reference-architectures/virtual-machines/single-vm/parameters/windows/single-vm.json
```

3. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定 Azure 订阅的名称：

```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" --output tsv | tr -d '"')
```

4. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定你先前在本练习中创建的资源组的名称：

```sh
    RESOURCE_GROUP='AADesignLab0303-RG'
```

5. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定将用于部署的 Azure 区域：

```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab0301-RG'].location" --output tsv)
```

6. 在 **Cloud Shell** 命令提示符下，键入以下命令并按 **Enter**，创建将用于部署的资源组：
```sh
    az group create --name $RESOURCE_GROUP --location $LOCATION
```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，将 **adminUsername** 参数的占位符替换为构建块参数文件中的值 **Student**：

```sh
    sed -i.bak1 's/"adminUsername": ""/"adminUsername": "Student"/' ./reference-architectures/virtual-machines/single-vm/parameters/windows/single-vm.json
```

8. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，将 **adminPassword** 参数的占位符替换为构建块参数文件中的值 **Pa55w.rd1234**：

```sh
    sed -i.bak2 's/"adminPassword": ""/"adminPassword": "Pa55w.rd1234"/' ./reference-architectures/virtual-machines/single-vm/parameters/windows/single-vm.json
```

9. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，验证参数值在构建块参数文件中是否修改成功：

```sh
    cat ./reference-architectures/virtual-machines/single-vm/parameters/windows/single-vm.json
```

10. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，使用 Azure 构建块部署 Windows Server 2016 Azure VM：

```sh
    azbb -g $RESOURCE_GROUP -s $SUBSCRIPTION_ID -l $LOCATION -p ./reference-architectures/virtual-machines/single-vm/parameters/windows/single-vm.json --deploy
```

11. 请等待部署完成，再执行下一个任务。


#### 任务 4: 验证 Windows Server 2016 Azure VM 是否正在为 Web 内容提供服务

1. 在 Azure 门户的主菜单中，单击 **资源组**。

2. 在 **资源组** 边栏选项卡上，单击表示你先前在本练习部署 Windows Server 2016 数据中心虚拟机到其中的资源组的条目。

3. 在“资源组”边栏选项卡上，单击表示你部署的虚拟机的条目。

4. 在 **虚拟机** 边栏选项卡上，找到 **公共IP地址** 条目，并确定其值。

5. 打开新的 Microsoft Edge 选项卡，然后导航到上一步中确定的 IP 地址。

6. 验证你是否能够访问默认的互联网信息服务网页。

7. 关闭新的浏览器选项卡。


#### 任务 5: 使用 Azure 构建块从 Cloud Shell 部署 Linux Azure VM

1.  在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，查看将用于此部署的 Azure 构建块参数文件的内容：

```sh
    cat ./reference-architectures/virtual-machines/single-vm/parameters/linux/single-vm.json
```

2. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，生成用于在访问 Linux VM 时进行身份验证的 SSH 密钥对：

```sh
    ssh-keygen -t rsa -b 2048
```

    - 当提示输入要将密钥保存在其中的文件时，按 **Enter 键** 接受默认值 **(~/.ssh/id_rsa)**。

    - 提示输入密码时，双击 **Enter 键**。

3. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定新生成的密钥对的公钥：

```sh
    PUBLIC_KEY=$(cat ~/.ssh/id_rsa.pub)
```

4. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定新生成的密钥对的公钥，其中考虑了公钥可能包含的任何特殊字符：

```sh
    PUBLIC_KEY_REGEX="$(echo $PUBLIC_KEY | sed -e 's/\\/\\\\/g; s/\//\\\//g; s/&/\\\&/g')"
```

    > **注**: 因为你将使用 **sed** 实用程序将此字符串插入 Azure 构建块参数文件中，所以必需这么做。 或者，你只需打开文件并直接在文件中输入公钥字符串即可。

5. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定 Azure 订阅的名称：

```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" --output tsv | tr -d '"')
```

6. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定将用于部署的资源组的名称：

```sh
    RESOURCE_GROUP='AADesignLab0304-RG'
```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建一个变量，其值用于指定将用于部署的 Azure 区域：

```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab0301-RG'].location" --output tsv)
```

8. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，将 **adminUsername** 参数的占位符替换为构建块参数文件中的值 **Student**：

```sh
    sed -i.bak1 's/"adminUsername": ""/"adminUsername": "Student"/' ./reference-architectures/virtual-machines/single-vm/parameters/linux/single-vm.json
```

9. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，将 **sshPublicKey** 参数的占位符替换为构建块参数文件中的 **$PUBLIC_KEY_REGEX** 变量：

```sh
    sed -i.bak2 's/"sshPublicKey": ""/"sshPublicKey": "'"$PUBLIC_KEY_REGEX"'"/' ./reference-architectures/virtual-machines/single-vm/parameters/linux/single-vm.json
```

10. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，验证参数值在构建块参数文件中是否修改成功：

```sh
    cat ./reference-architectures/virtual-machines/single-vm/parameters/linux/single-vm.json
```

11. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，创建新资源组：

```sh
    az group create --name $RESOURCE_GROUP --location $LOCATION
```

12. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，使用 Azure 构建块部署 Linux Azure VM：

```sh
    azbb -g $RESOURCE_GROUP -s $SUBSCRIPTION_ID -l $LOCATION -p ./reference-architectures/virtual-machines/single-vm/parameters/linux/single-vm.json --deploy
```

13. 请等待部署完成，再执行下一个任务。


#### 任务 7: 验证 Linux Azure VM 是否正在为 Web 内容提供服务

1. 在 Azure 门户的主菜单中，单击 **资源组**。

2. 在 **资源组** 边栏选项卡上，单击表示你将虚拟机部署到其中的资源组的条目。

3. 在“资源组”边栏选项卡上，单击表示你部署的虚拟机的条目。

4. 在 **虚拟机** 边栏选项卡上，找到 **公共IP地址** 条目，并确定其值。

5. 打开新的 Microsoft Edge 选项卡，然后导航到上一步中确定的 IP 地址。

6. 验证你是否能够访问默认的 Apache2 Ubuntu 网页。

7. 关闭新的浏览器选项卡。

8. 关闭 **Cloud Shell** 窗格。

> **回顾**: 在本练习中，你使用 Azure 构建块从 Cloud Shell 部署了运行 Windows Server 2016 数据中心和 Linux 的 Azure VM。


## 练习 4: 删除实验室资源 #### 任务

#### 任务 1: 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

2. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，列出你在本实验室中创建的所有资源组：

```sh
    az group list --query "[?starts_with(name,'AADesignLab03')]".name --output tsv
```

3. 验证输出结果中是否仅包含你在本实验室中创建的资源组。 这些组将在下一个任务中删除。

#### 任务 2: 删除资源组

1. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter 键**，删除在本实验室中创建的资源组

```sh
    az group list --query "[?starts_with(name,'AADesignLab03')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
```

2. 关闭门户底部的 **Cloud Shell** 提示。


> **回顾**: 在本练习中，你删除了本实验室中使用的资源。
