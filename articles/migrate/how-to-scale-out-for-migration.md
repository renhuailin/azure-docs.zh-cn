---
title: 设置用于无代理 VMware 迁移的 Azure Migrate 扩展设备
description: 了解如何设置 Azure Migrate 横向扩展设备以迁移 Hyper-v Vm。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: fda9026adf46a26927912b9e1983a537470c37ee
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744444"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>将 VMware 虚拟机的无代理迁移到 Azure

本文将帮助你了解如何使用横向扩展设备将大量 VMware 虚拟机 (Vm) 迁移到 Azure，方法是使用 Azure Migrate 服务器迁移工具的无代理迁移功能。 

通过服务器迁移工具的 VMware 虚拟机功能的无代理迁移，你可以：

- 使用一台 Azure Migrate 设备从一个 vCenter 服务器并行复制最多300个 Vm。
- 通过部署用于迁移的第二个横向扩展设备，从单个 vCenter 服务器并行复制最多500个 Vm。

在本文中，你将学习如何：

- 部署用于 VMware 迁移的横向扩展设备
- 使用横向扩展设备同时迁移多达 500 Vm。

##  <a name="prerequisites"></a>先决条件

在开始之前，需要执行以下步骤：

- 创建 Azure Migrate 项目。
-  (主要设备上部署 Azure Migrate 设备) 并完整发现 vCenter 服务器管理的 VMware 虚拟机。
- 为要迁移的一个或多个虚拟机配置复制。
> [!IMPORTANT]
> 你需要在项目中至少具有一个复制虚拟机，然后才能添加横向扩展设备进行迁移。

## <a name="deploy-a-scale-out-appliance"></a>部署横向扩展设备


若要添加横向扩展设备，请执行下面所述的步骤：

1. 单击 "**发现**  >  **" 是否已虚拟化计算机？** 
1. 选择 **"是"，并选择 "VMware VSphere 虚拟机监控程序"。**
1. 选择下一步中的 "无代理复制"。
1. 在 "选择设备类型" 菜单中选择 " **扩展现有的主要设备** "。
1. 使用执行发现 (设备选择要横向扩展) 的主要设备。

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="向外扩展载入的 &quot;发现计算机&quot; 页":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1.生成 Azure Migrate 项目密钥

1. 在 " **生成 Azure Migrate 项目密钥** 中，提供扩展设备的后缀名称。 后缀只能包含字母数字字符，长度限制为14个字符。
2. 单击 " **生成密钥** " 开始创建所需的 Azure 资源。 请不要在创建资源的过程中关闭 "发现" 页。
3. 复制生成的密钥。 稍后将需要此密钥来完成横向扩展设备的注册。

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. 为横向扩展设备下载安装程序

在 " **下载 Azure Migrate 设备** 上，单击"  **下载**"。 需要下载 PowerShell 安装程序脚本以在运行 Windows Server 2016 的现有服务器上部署横向扩展设备，并使用所需的硬件配置 (32-GB RAM、8个 vcpu、大约 80 GB 的磁盘存储和 internet 访问) 。
:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="下载横向扩展设备脚本":::

> [!TIP]
> 您可以使用以下步骤验证已下载的 zip 文件的校验和：
>
> 1. 以管理员身份打开命令提示符
> 2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 公有云的示例用法：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. 如果计算所得的哈希值与此字符串不匹配，请从门户下载最新版本的横向扩展设备安装程序： e9c9a1fe4f3ebae81008328e8f3a7933d78ff835ecd871d1b17f367621ce3c74

### <a name="3-run-the-azure-migrate-installer-script"></a>3.运行 Azure Migrate 安装程序脚本
此安装程序脚本执行以下操作：

- 安装网关代理和设备配置管理器以执行更多并发服务器复制。
- 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
- 下载并安装 IIS 可重写模块。 [了解详细信息](https://www.microsoft.com/download/details.aspx?id=7435)。
- 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
- 在路径下创建以下文件：
    - **配置文件**：%Programdata%\Microsoft Azure\Config
    - **日志文件**：%Programdata%\Microsoft Azure\Logs

按如下所示运行脚本：

1. 将 zip 文件提取到将承载横向扩展设备的服务器上的文件夹中。  请确保不要在具有现有 Azure Migrate 设备的服务器上运行该脚本。
2. 使用管理（提升）权限在上述服务器上启动 PowerShell。
3. 将 PowerShell 目录更改为已从下载的 zip 文件中提取内容的文件夹。
4. 使用以下命令运行名为 **AzureMigrateInstaller.ps1**  的脚本：

    - 对于公有云： 
    
        ``` PS C:\Users\administrator\Desktop\AzureMigrateInstaller-Server-Public> .\AzureMigrateInstaller.ps1 ```

    脚本将在完成执行时启动设备配置管理器。

如果遇到任何问题，可以访问以下位置的脚本日志： <br/> 用于故障排除的 C:\ProgramData\Microsoft<em>azure\logs\ AzureMigrateScenarioInstaller_。</em>


### <a name="4-configure-the-appliance"></a>4.配置设备

在开始之前，请确保可从横向扩展设备访问 [这些 Azure 终结点](migrate-appliance.md#public-cloud-urls) 。

- 在可连接到横向扩展设备服务器的任意计算机上打开浏览器，并打开设备配置管理器的 URL： " **44368 https://**"。

   或者，您可以使用配置管理器的快捷方式从横向扩展设备服务器的桌面打开配置管理器。
- 接受许可条款，并阅读第三方信息。
- 在配置管理器中 > **设置必备组件**，请执行以下操作：
   - **连接**：设备将检查服务器是否具有 internet 访问权限。 如果服务器使用代理：
     1. 单击“设置代理”，指定代理地址（格式为 http://ProxyIPAddress 或 http://ProxyFQDN) ）以及侦听端口。
     2. 如果代理需要身份验证，请指定凭据。
     3. 仅支持 HTTP 代理。
     4. 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以单击 " **查看设备服务** " 查看设备服务器上运行的服务的状态和版本。
   - **安装 VDDK**：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 如果未安装，请从 VMware 下载 VDDK 6.7，并将下载的 zip 内容解压缩到设备上的指定位置，如设备上的 **安装说明** Configuration Manager 屏幕中所述。


### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 Azure Migrate 项目密钥。 如果没有密钥，请参阅 " **服务器评估"> 发现> 管理现有设备**"，选择主要设备名称，查找与其关联的扩展设备，并复制相应的密钥。
1. 需要使用设备代码对 Azure 进行身份验证。 单击“登录”将打开包含设备代码的模式，如下所示。
:::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="显示设备代码的模式":::

1. 单击“复制代码并登录”以复制设备代码，并在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，粘贴设备代码并使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 如果在未登录的情况下意外关闭了登录选项卡，则需要刷新设备配置管理器的浏览器选项卡以再次启用“登录”按钮。
1. 成功登录后，请使用设备配置管理器返回到上一个选项卡。
1. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的权限，会启动设备注册。
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="设备 configuration manager 上的面板2":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>从主要设备导入设备配置

若要完成横向扩展设备注册，请单击 " **导入** " 以从主要设备获取所需的配置文件。

1. 单击 " **导入** " 会打开一个弹出窗口，其中包含有关如何从主要设备导入所需的配置文件的说明。
:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="导入配置模式":::
1. 登录 (远程桌面) 到主要设备并执行以下 PowerShell 命令：

    ``` PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' ```
    
    ``` PS .\ExportConfigFiles.ps1 ```

1. 通过运行上述命令将创建的 zip 文件复制到向外扩展设备上。 Zip 文件包含注册横向扩展设备所需的配置文件。
1. 在上一步打开的弹出窗口中，选择复制的配置 zip 文件的位置，并单击 " **保存**"。

成功导入文件后，将完成横向扩展设备的注册，并显示上次成功导入的时间戳。 还可以通过单击 " **查看详细信息**" 来查看注册详细信息。
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="导入成功":::

此时，应重新验证横向扩展设备是否能够连接到 vCenter 服务器。 单击 "重新 **验证** " 以验证从横向扩展设备 vCenter Server 连接性。
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="导入成功":::

> [!IMPORTANT]
> 如果在主要设备上编辑 vCenter Server 凭据，请确保将配置文件再次导入到横向扩展设备，以获取最新配置并继续任何正在进行的复制。<br/> 如果不再需要向外扩展设备，请确保禁用横向扩展设备。 了解有关如何在不需要时禁用横向扩展设备的 [**详细信息**](./common-questions-appliance)。

## <a name="replicate"></a>复制

1. 注册扩展设备后，在 "Azure Migrate：服务器迁移" 磁贴上，单击 " **复制**"。

2.  按照屏幕上的步骤开始复制更多的虚拟机。 

部署扩展设备后，现在可以同时复制 500 Vm。 还可以通过 Azure 门户批量迁移 Vm （200）。

Azure Migrate Server 迁移工具将负责在复制的主要和横向扩展设备之间分发虚拟机。 完成复制后，你可以迁移虚拟机。

> [!TIP]
> 如果要迁移大量的虚拟机，我们建议以批处理200的方式迁移虚拟机，以获得最佳性能。
  
## <a name="next-steps"></a>后续步骤

在本文中，我们已了解到：
- 如何配置横向扩展设备
- 如何使用横向扩展设备复制 Vm


[详细了解](https://docs.microsoft.com/azure/migrate/tutorial-migrate-vmware) 如何使用 Azure Migrate：服务器迁移工具将服务器迁移到 Azure。