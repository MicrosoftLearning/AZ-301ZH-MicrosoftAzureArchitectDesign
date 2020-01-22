---
lab:
    title: '部署 Azure 解决方案中使用的网络基础架构'
    module: '模块 3：联网 Azure 应用程序组件'
---

# 部署 Azure 解决方案中使用的网络基础架构 

# 实验室答案密钥: 部署 Azure 解决方案中使用的网络基础架构

## 在开始之前

1. 请确保使用以下凭证登录到 Windows 10 实验室虚拟机：

    - 用户名: **Admin**

    - 密码: **Pa55w.rd**

2. 查看位于 Windows 10 桌面底部的任务栏。 任务栏包含你在实验室中常用的应用程序图标：

    - Microsoft Edge

    - 文件资源管理器

    - [Visual Studio Code](https://code.visualstudio.com/)

    - [Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)

    - Bash on Ubuntu on Windows

    - Windows PowerShell

    > **注**: 你还可以在 **开始菜单** 中找到这些应用程序的快捷方式。

## 练习 1: 配置实验室环境

#### 任务 1: 打开 Azure 门户

1. 单击在任务栏中的 **Microsoft Edge** 图标。

2. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>).

3. 出现提示时，使用你将在本实验室中使用的 Azure 订阅中具有所有者角色的用户账户进行身份验证。

#### 任务 2: 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

    > **注**: **Cloud Shell** 图标是由 *大于* 和 *下划线* 字符组合构成的一个符号。

2. 如果这是你第一次使用订阅打开 **Cloud Shell**，你将看到 **Cloud Shell** 首次使用的配置向导。 出现提示时，在 **欢迎使用 Azure Cloud Shell** 窗格，单击 **Bash (Linux)**。

    > **注**: 如果你没有看到 **Cloud Shell** 的配置选项，这很可能是因为你在本课程的实验室中使用现有订阅。 如果是，请直接进入下一个任务。 

3. 在 **尚未安装存储资源** 窗格，单击 **显示高级设置**，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **Cloud Shell 区域**下拉列表中，选择与你打算在本实验室中部署资源的位置匹配或接近的 Azure 区域

    - 在 **资源组** 部分，选择 **使用现有** 选项，然后在下拉列表中选择 **AADesignLab0901-RG**。

    - 在 **存储账户** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入由 3 到 24 个字符和数字组成的唯一名称。 

    - 在 **文件共享** 部分，确保选中 **创建新的** 选项，然后在下方文本框中输入 **cloudshell**。

    - 单击“创建存储资源”按钮。

4. 等待 **Cloud Shell** 完成其首次设置过程，然后进入下一个任务。

#### 任务 3: 在 Azure Cloud Shell 中安装Azure 构建块 npm 包

1. 在门户底部的 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建本地目录以安装 Azure 构建块 npm 包：

    ```sh
    mkdir ~/.npm-global
    ```

2. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，更新 npm 配置，将新的本地目录包含在内：

    ```sh
    npm config set prefix '~/.npm-global'
    ```

3. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，打开 ~./bashrc 配置文件进行编辑：

    ```sh
    vi ~/.bashrc
    ```

4. 在 **Cloud Shell** 命令提示符处，在 vi 编辑器界面中，向下滚动到文件的底部（或输入 **G**），向右滚动到最后一行的最右侧字符（或输入**$**）输入 **a** 进入 **插入** 模式，按 **Enter 键** 换行，然后输入以下内容将新创建的目录添加到系统路径：

    ```sh
    export PATH="$HOME/.npm-global/bin:$PATH"
    ```

5. 在 **Cloud Shell** 命令提示符处，在 vi 编辑器界面中，按 **Esc**，再按 **：**，输入 **wq!** 并按 **Enter** 键，保存更改并关闭文件。

6. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，安装 Azure 构建块 npm 包：

    ```sh
    npm install -g @mspnp/azure-building-blocks
    ```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，退出 shell：

    ```sh
    exit
    ```

8. 在 **Cloud Shell 超时** 窗格中，单击 **重新连接**。 

    > **注**: 你需要重新启动 Cloud Shell 才能使安装的构建块 npm 包生效。


#### 任务 4: 准备构建块 Hub 和 Spoke 参数文件

1. 在 **Cloud Shell** 窗格，单击 **上传/下载文件** 图标，然后在下拉菜单中单击 **上传**。 

2. 在 **打开** 对话框中，导航到 **\\allfiles\\AZ-301T04\\Module_03\\LabFiles\\Starter\\** 文件夹，选择 **hub-nva.json**，并单击 **打开**。重复相同的步骤，上传文件 **hub-vnet.json**、**hub-vnet-peering.json**、**spoke1.json** 和 **spoke2.json**。

3. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，将 **adminUsername** 参数的占位符替换为构建块参数文件 **hub-vnet.json** 中的值 **Student**：

    ```sh
    sed -i.bak1 's/"adminUsername": ""/"adminUsername": "Student"/' ~/hub-vnet.json
    ```

4. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，将 **adminPassword** 参数的占位符替换为构建块参数文件 **hub-vnet.json** 中的值 **Pa55w.rd1234**：

    ```sh
    sed -i.bak2 's/"adminPassword": ""/"adminPassword": "Pa55w.rd1234"/' ~/hub-vnet.json
    ```

5. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，验证参数值在构建块参数文件 **hub-vnet.json** 中是否修改成功：

    ```sh
    cat ~/hub-vnet.json
    ```

6. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter** 键，将 **adminUsername** 参数的占位符替换为构建块参数文件 **hub-nva.json** 中的值 **Student**：

    ```sh
    sed -i.bak1 's/"adminUsername": ""/"adminUsername": "Student"/' ~/hub-nva.json
    ```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，将 **adminPassword** 参数的占位符替换为构建块参数文件 **hub-nva.json** 中的值 **Pa55w.rd1234**：

    ```sh
    sed -i.bak2 's/"adminPassword": ""/"adminPassword": "Pa55w.rd1234"/' ~/hub-nva.json
    ```

8. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，验证参数值在构建块参数文件 **hub-nva.json** 中是否修改成功：

    ```sh
    cat ~/hub-nva.json
    ```

9. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，将 **adminUsername** 参数的占位符替换为构建块参数文件 **spoke1.json** 中的值 **Student**：

    ```sh
    sed -i.bak1 's/"adminUsername": ""/"adminUsername": "Student"/' ~/spoke1.json
    ```

10. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，将 **adminPassword** 参数的占位符替换为构建块参数文件 **spoke1.json** 中的值 **Pa55w.rd1234** ：

    ```sh
    sed -i.bak2 's/"adminPassword": ""/"adminPassword": "Pa55w.rd1234"/' ~/spoke1.json
    ```

11. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，验证参数值在构建块参数文件 **spoke1.json** 中是否修改成功：

    ```sh
    cat ~/spoke1.json
    ```

12. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，将 **adminUsername** 参数的占位符替换为构建块参数文件 **spoke2.json** 中的值 **Student**：

    ```sh
    sed -i.bak1 's/"adminUsername": ""/"adminUsername": "Student"/' ~/spoke2.json
    ```

13. 在 **Cloud Shell** 命令提示符处，输入以下命令，然后按 **Enter** 键，将 **adminPassword** 参数的占位符替换为构建块参数文件 **spoke2.json** 中的值 **Pa55w.rd1234**：

    ```sh
    sed -i.bak2 's/"adminPassword": ""/"adminPassword": "Pa55w.rd1234"/' ~/spoke2.json
    ```

14. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，验证参数值在构建块参数文件 **spoke2.json** 中是否修改成功：

    ```sh
    cat ~/spoke2.json
    ```

#### 任务 5: 实现 Hub 和 Spoke 设计的主组件

1. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定 Azure 订阅的名称：

    ```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" --output tsv | tr -d '"')
    ```

2. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将包含主虚拟网络的资源组的名称：

    ```sh
    RESOURCE_GROUP_HUB_VNET='AADesignLab08-hub-vnet-rg'
    ```

3. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将用于部署的 Azure 区域（将替换占位符 `` 替换为使用你想要在本实验室中部署资源的 Azure 区域的名称）：

    ```sh
    LOCATION='<Azure region>'
    ```
4.  在 **Cloud Shell** 命令提示符下，键入以下命令并按 **Enter**，创建用于部署的资源组

    ```sh
    az group create --name $RESOURCE_GROUP_HUB_VNET --location $LOCATION
    ```


5. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，使用 Azure 构建块部署 Hub-and-Spoke 拓扑的主组件：

    ```sh
    azbb -g $RESOURCE_GROUP_HUB_VNET -s $SUBSCRIPTION_ID -l $LOCATION -p ~/hub-vnet.json --deploy
    ```

6. 继续下一个任务，无需等待部署完成。


#### 任务 6: 实现 Hub 和 Spoke 设计的辐组件

1. 单击在任务栏中的 **Microsoft Edge** 图标。

2. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>).

3. 出现提示时，使用你先前在本实验室中使用的同一用户账户进行身份验证。

4. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

5. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定 Azure 订阅的名称：

    ```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" --output tsv | tr -d '"')
    ```

6. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将包含第一个辐虚拟网络的资源组的名称：

    ```sh
    RESOURCE_GROUP_SPOKE1_VNET='AADesignLab08-spoke1-vnet-rg'
    ```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将用于部署的 Azure 区域：

    ```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab08-hub-vnet-rg'].location" --output tsv)
    ```
1. 在 **Cloud Shell** 命令提示符下，键入以下命令并按 **Enter**，创建用于部署的资源组:

    ```sh
    az group create --name $RESOURCE_GROUP_SPOKE1_VNET --location $LOCATION
    ```

8. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，使用 Azure 构建块部署 Hub-and-Spoke 拓扑的第一个辐组件：

    ```sh
    azbb -g $RESOURCE_GROUP_SPOKE1_VNET -s $SUBSCRIPTION_ID -l $LOCATION -p ~/spoke1.json --deploy
    ```

9. 继续下一步，无需等待部署完成。

10. 单击在任务栏中的 **Microsoft Edge** 图标。

11. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>).

12. 出现提示时，使用你先前在本实验室中使用的同一用户账户进行身份验证。

13. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

14. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定 Azure 订阅的名称：

    ```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" --output tsv | tr -d '"')
    ```

15. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将包含第二个辐虚拟网络的资源组的名称：

    ```sh
    RESOURCE_GROUP_SPOKE2_VNET='AADesignLab08-spoke2-vnet-rg'
    ```

16. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将用于部署的 Azure 区域：

    ```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab08-hub-vnet-rg'].location" --output tsv)
    ```
17. 在 **Cloud Shell** 命令提示符下，键入以下命令并按 **Enter**，创建用于部署的资源组:

    ```sh
    az group create --name $RESOURCE_GROUP_SPOKE2_VNET --location $LOCATION
    ```

18. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，使用 Azure 构建块部署 Hub-and-Spoke 拓扑的第二个辐组件：

    ```sh
    azbb -g $RESOURCE_GROUP_SPOKE2_VNET -s $SUBSCRIPTION_ID -l $LOCATION -p ~/spoke2.json --deploy
    ```

19. 继续下一个任务，无需等待部署完成。

#### 任务 7: 配置 Hub 和 Spoke 设计的VNet 对等互连

1. 单击在任务栏中的 **Microsoft Edge** 图标。

2. 在打开的浏览器窗口中，导航到 **Azure 门户** (<https://portal.azure.com>).

3. 出现提示时，使用你先前在本实验室中使用的同一用户账户进行身份验证。

4. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

5. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定 Azure 订阅的名称：

    ```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" --output tsv | tr -d '"')
    ```

6. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定包含主虚拟网络的资源组的名称：

    ```sh
    RESOURCE_GROUP_HUB_VNET='AADesignLab08-hub-vnet-rg'
    ```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将用于部署的 Azure 区域：

    ```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab08-hub-vnet-rg'].location" --output tsv)
    ```

8. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，使用 Azure 构建块为 Hub-and-Spoke 拓扑中的虚拟网络进行对等互连：

    ```sh
    azbb -g $RESOURCE_GROUP_HUB_VNET -s $SUBSCRIPTION_ID -l $LOCATION -p ~/hub-vnet-peering.json --deploy
    ```

9. 继续下一个任务，无需等待部署完成。


#### 任务 8: 配置 Hub 和 Spoke 设计的路由

1. 单击在任务栏中的 **Microsoft Edge** 图标。

2. 在打开的浏览器窗口中，导航到 **Azure 门户**  (<https://portal.azure.com>).

3. 出现提示时，使用你先前在本实验室中使用的同一用户账户进行身份验证。

4. 在门户顶部，单击 **Cloud Shell** 图标，打开新的 shell 实例。

5. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定 Azure 订阅的名称：

    ```sh
    SUBSCRIPTION_ID=$(az account list --query "[0].id" --output tsv | tr -d '"')
    ```

6. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将包含主网络虚拟设备 (NVA) 的资源组的名称：

    ```sh
    RESOURCE_GROUP_HUB_NVA='AADesignLab08-hub-nva-rg'
    ```

7. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，创建一个变量，其值用于指定将用于部署的 Azure 区域：

    ```sh
    LOCATION=$(az group list --query "[?name == 'AADesignLab08-hub-vnet-rg'].location" --output tsv)
    ```
8. 在 **Cloud Shell** 命令提示符下，键入以下命令，创建将在部署中使用的资源组.

    ```sh
    az group create --name $RESOURCE_GROUP_HUB_NVA --location $LOCATION
    ```
    
9. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，使用 Azure 构建块部署 Hub-and-Spoke 拓扑的 NVA 组件：

    ```sh
    azbb -g $RESOURCE_GROUP_HUB_NVA -s $SUBSCRIPTION_ID -l $LOCATION -p ~/hub-nva.json --deploy
    ```

10. 请等待部署完成，再执行下一个任务。

    > **注**: 部署可能需要大约 10 分钟。


## 练习 2: 查看 Hub-spoke 拓扑

#### 任务 1: 检查对等互连配置

1. 在 Azure 门户的主菜单中，单击 **所有服务**。

2. 在 **所有服务** 菜单中，在 **过滤器** 文本框中输入 **虚拟网络** 并按 **Enter** 键。

3. 在结果列表中，单击 **虚拟网络**。

4. 在 **虚拟网络** 边栏选项卡中，单击 **hub-vnet**。

5. 在 **hub-vnet** 边栏选项卡上，单击 **Peerings**。

6. 在 **hub-vnet - Peerings** 边栏选项卡上，查看对等互连列表及其状态。

7. 导航回 **虚拟网络** 边栏选项卡，然后单击 **spoke1-vnet**。

8. 在 **spoke1-vnet** 边栏选项卡上，单击 **Peerings**。

9. 在 **spoke1-vnet - Peerings** 边栏选项卡上，查看现有的对等互连及其状态。

10. 导航回 **虚拟网络** 边栏选项卡，然后单击 **spoke2-vnet**。

11. 在 **spoke2-vnet** 边栏选项卡上，单击 **Peerings**。

12. 在 **spoke2-vnet - Peerings** 边栏选项卡上，查看现有的对等互连及其状态。

#### 任务 2: 检查路由配置

1. 在 **所有服务** 菜单中，在 **过滤器** 文本框中输入 **路由表** 并按 **Enter** 键。

2. 在结果列表中，单击 **路由表**。

3. 在 **路由表** 边栏选项卡上，单击 **spoke1-rt**。

4. 在 **spoke1-rt** 边栏选项卡上，查看路由列表。 注意路由 **toSpoke2** 的 **下一跳** 条目。

5. 导航回 **路由表** 边栏选项卡并单击 **spoke2-rt**。

6. 在 **spoke2-rt** 边栏选项卡上，查看路由列表。 注意路由 **toSpoke1** 的 **下一跳** 条目。

#### 任务 3: 验证辐组件之间的连接

1. 在 Azure 门户的主菜单中，单击 **所有服务**。

2. 在 **所有服务** 菜单中，在 **过滤器** 文本框中输入 **网络监视器** 并按 **Enter** 键。

3. 在结果列表中，单击 **网络监视器**。

4. 在 **网络监视器** 边栏选项卡上，在 **网络诊断工具** 部分，单击 **连接疑难解答**。

5. 在 **网络监视器 - 连接疑难解答** 边栏选项卡上，执行以下任务：

    - 将 **订阅** 下拉列表条目设置为默认值。

    - 在 **资源组** 下拉列表中，选择 **AADesignLab08-spoke1-vnet-rg** 条目。

    - 在 **虚拟机** 下拉列表中，保留默认条目。

    - 确保 **目的地** 选项设置为 **手动指定**。

    - 在 **URI、FQDN 或 IPv4** 文本框中，输入 **10.2.0.68** 条目。

    - 在 **目标端口** 文本框中，输入 3389。

    - 单击“检查”按钮。

6. 待返回连接检查结果之后，验证状态是否为 **可连接**。

    > **注**: 如果这是你第一次使用网络监视器，则检查可能最多需要 5 分钟。


## 练习 3: 删除实验室资源

#### 任务 1: 打开 Cloud Shell

1. 在门户顶部，单击 **Cloud Shell** 图标，打开 Cloud Shell 窗格。

2. 在 **Cloud Shell** 命令提示符处，输入以下命令并按 **Enter** 键，列出你在本实验室中创建的所有资源组：

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab08')]".name --output tsv
    ```

3. 验证输出结果中是否仅包含你在本实验室中创建的资源组。

#### 任务 2: 删除资源组

1. 在** Cloud Shell **命令提示符处，输入以下命令并按**Enter** 键，删除在本实验室中创建的资源组

    ```sh
    az group list --query "[?starts_with(name,'AADesignLab08')]".name --output tsv | xargs -L1 bash -c 'az group delete --name $0 --no-wait --yes'
    ```

1. 关闭门户底部的 **Cloud Shell** 提示。

> **回顾**: 在本练习中，你删除了本实验室中使用的资源。
