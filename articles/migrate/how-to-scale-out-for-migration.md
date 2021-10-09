---
title: 设置 Azure Migrate 横向扩展设备以进行无代理 VMware 迁移
description: 了解如何设置 Azure Migrate 横向扩展设备以迁移 Hyper-V VM。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/02/2021
ms.openlocfilehash: 187ed49a116b99524f6cfca408bdb0d7e15a47ca
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092578"
---
# <a name="scale-agentless-migration-of-vmware-virtual-machines-to-azure"></a>缩放将 VMware 虚拟机迁移到 Azure 的无代理迁移过程

本文介绍如何借助横向扩展设备，使用 Azure Migrate 服务器迁移工具的用来迁移 VMware VM 的无代理方法将大量 VMware 虚拟机 (VM) 迁移到 Azure。

使用用于 VMware 虚拟机的无代理迁移方法，你可以：

- 使用一台 Azure Migrate 设备，从单个 vCenter 服务器同时复制多达 300 个 VM。
- 通过部署第二台用于迁移的横向扩展设备，可以从单个 vCenter 服务器同时复制多达 500 个 VM。

在本文中，你将学习如何：

- 添加用于对 VMware 虚拟机进行无代理迁移的横向扩展设备
- 使用横向扩展设备同时迁移多达 500 个 VM。

##  <a name="prerequisites"></a>先决条件

开始迁移前，需要执行以下步骤：

- 创建 Azure Migrate 项目。
- 部署 Azure Migrate 设备（主设备），并完成由 vCenter 服务器托管的 VMware 虚拟机的发现操作。
- 为要迁移的一个或多个虚拟机配置复制。
> [!IMPORTANT]
> 在添加横向扩展设备进行迁移之前，项目中至少需要有一个复制虚拟机。

若要了解如何执行上述操作，请查看有关如何[使用无代理迁移方法将 VMware 虚拟机迁移到 Azure](./tutorial-migrate-vmware.md) 的教程。

## <a name="deploy-a-scale-out-appliance"></a>部署横向扩展设备

若要添加横向扩展设备，请执行下面所述的步骤：

1. 单击“发现” > “计算机是否已虚拟化?”  
1. 选择“是，使用 VMware vSphere 虚拟机监控程序”。
1. 在下一步中选择“无代理复制”。
1. 在“选择设备类型”菜单中选择“横向扩展现有主设备”。
1. 选择要横向扩展的主设备（执行发现时所使用的设备）。

:::image type="content" source="./media/how-to-scale-out-for-migration/add-scale-out.png" alt-text="用于横向扩展加入的“发现计算机”页":::

### <a name="1-generate-the-azure-migrate-project-key"></a>1.生成 Azure Migrate 项目密钥

1. 在“生成 Azure Migrate 项目密钥”中，为横向扩展设备提供后缀名。 后缀只能包含字母数字字符，长度限制为 14 个字符。
2. 单击“生成密钥”，开始创建所需的 Azure 资源。 在创建资源期间，不要关闭“发现”页。
3. 复制生成的密钥。 稍后将需要此密钥来完成横向扩展设备的注册。

### <a name="2-download-the-installer-for-the-scale-out-appliance"></a>2. 下载横向扩展设备的安装程序

在“下载 Azure Migrate 设备”中，单击“下载” 。 你需要下载 PowerShell 安装程序脚本，以便在运行 Windows server 2016 的现有服务器上部署横向扩展设备，并使用所需的硬件配置（32 GB RAM、8 个 vCPU、约 80 GB 的磁盘存储和 Internet 访问，直接或通过代理访问）。

:::image type="content" source="./media/how-to-scale-out-for-migration/download-scale-out.png" alt-text="下载横向扩展设备的脚本":::

> [!TIP]
> 可以使用以下步骤验证已下载的 zip 文件的校验和：
>
> 1. 在下载文件的服务器上，打开管理员命令窗口。
> 2. 运行以下命令以生成 zip 文件的哈希：
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 用法示例：```C:\>CertUtil -HashFile C:\Users\administrator\Desktop\AzureMigrateInstaller.zip SHA256 ```
> 3. 如果计算出的哈希值与以下字符串不匹配，请从门户下载最新版本的横向扩展设备安装程序：BA84B58E88DDFE23E5D4CE73530227EBBC187B3634B66A3E0F0B3E5DF5F0A94F

### <a name="3-run-the-azure-migrate-installer-script"></a>3.运行 Azure Migrate 安装程序脚本

1. 将压缩文件解压缩到托管设备的服务器上的某个文件夹中。  请确保不要在现有 Azure Migrate 设备上的服务器上运行该脚本。

2. 使用管理（提升）权限在上述服务器上启动 PowerShell。

3. 将 PowerShell 目录更改为从下载的压缩文件中提取内容的文件夹。

4. 通过运行以下命令，运行名为“`AzureMigrateInstaller.ps1`”的脚本：

  `PS C:\Users\administrator\Desktop\AzureMigrateInstaller> .\AzureMigrateInstaller.ps1 `

5. 从方案、云、配置和连接选项中进行选择，以部署所需的设备。 例如，下面所示的选择会在 Azure 公有云上一个已建立默认（公共终结点）连接的 Azure Migrate 项目中设置一个横向扩展设备，用于在 VMware 环境中运行的服务器上启动并发复制  。

    :::image type="content" source="./media/how-to-scale-out-for-migration/script-vmware-scaleout-inline.png" alt-text="显示如何设置横向扩展设备的屏幕截图。" lightbox="./media/how-to-scale-out-for-migration/script-vmware-scaleout-expanded.png":::

6. 此安装程序脚本执行以下操作：

    - 安装网关代理和设备配置管理器以执行更多并发服务器复制。
    - 安装 Windows 角色，包括 Windows 激活服务、IIS 和 PowerShell ISE。
    - 下载并安装 IIS 可重写模块。
    - 更新 Azure Migrate 的注册表项 (HKLM) 和永久性设置详细信息。
    - 在路径下创建以下文件：
        - **配置文件**：%Programdata%\Microsoft Azure\Config
        - **日志文件**：%Programdata%\Microsoft Azure\Logs

成功执行该脚本后，将自动启动设备配置管理器。

> [!NOTE]
> 如果遇到任何问题，可以访问位于 C:\ProgramData\Microsoft Azure\Logs\AzureMigrateScenarioInstaller_<em>Timestamp</em>.log 的脚本日志来进行故障排除。


### <a name="4-configure-the-appliance"></a>4.配置设备

在开始之前，请确保可从横向扩展设备访问[这些 Azure 终结点](migrate-appliance.md#public-cloud-urls)。

- 在可以连接到横向扩展设备服务器的任何计算机上打开一个浏览器，然后打开设备配置管理器的 URL： https://scale-out appliance name or IP address: 44368。

   或者，可以使用配置管理器的快捷方式，从横向扩展设备服务器桌面打开配置管理器。
- 接受许可条款，并阅读第三方信息。
- 在配置管理器 >“设置先决条件”中执行以下操作：
   - **连接**：设备将检查服务器是否可访问 Internet。 如果服务器使用代理：
     1. 单击“设置代理”，指定代理地址（格式为 http://ProxyIPAddress 或 http://ProxyFQDN) ）和侦听端口。
     2. 如果代理需要身份验证，请指定凭据。
     3. 仅支持 HTTP 代理。
     4. 如果已添加代理详细信息或已禁用代理和/或身份验证，请单击“保存”，再次触发连接性检查。
   - **时间同步**：设备上的时间应与 Internet 时间同步，这样才能正常进行发现。
   - **安装更新**：设备可确保安装最新的更新。 检查完成后，可以单击“查看设备服务”查看设备服务器上运行的服务的状态和版本。
   - **安装 VDDK**：设备将检查是否已安装 VMware vSphere 虚拟磁盘开发工具包 (VDDK)。 从 VMware 下载 VDDK 6.7（如果尚未安装它），并按照“设备配置管理器”屏幕上的“安装说明”中提供的信息将下载的 zip 内容解压缩到设备上的指定位置。


### <a name="register-the-appliance-with-azure-migrate"></a>将设备注册到 Azure Migrate

1. 粘贴从门户复制的 Azure Migrate 项目密钥。 如果没有密钥，请转到“服务器评估”>“发现”>“管理现有设备”，选择主设备名称，找到与其关联的横向扩展设备，然后复制相应的密钥。
1. 需要使用设备代码对 Azure 进行身份验证。 单击“登录”将打开包含设备代码的模式，如下所示。

   :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="显示设备代码的模式":::

1. 单击“复制代码并登录”以复制设备代码，并在新的浏览器选项卡中打开 Azure 登录提示。如果未显示该按钮，请确保已在浏览器中禁用弹出窗口阻止程序。
1. 在新选项卡上，粘贴设备代码并使用 Azure 用户名和密码登录。
   
   不支持使用 PIN 登录。
3. 如果在未登录的情况下意外关闭了登录选项卡，则需要刷新设备配置管理器的浏览器选项卡以再次启用“登录”按钮。
1. 成功登录后，使用设备配置管理器返回到上一个选项卡。
1. 如果用于登录的 Azure 用户帐户对在密钥生成过程中创建的 Azure 资源具有恰当的权限，会启动设备注册。
:::image type="content" source="./media/how-to-scale-out-for-migration/registration-scale-out.png" alt-text="设备配置管理器上的面板 2":::

#### <a name="import-appliance-configuration-from-primary-appliance"></a>从主设备导入设备配置

若要完成横向扩展设备的注册，请单击“导入”以从主设备获取必要的配置文件。

1. 单击“导入”会打开一个弹出窗口，其中包含有关如何从主设备导入必要配置文件的说明。

:::image type="content" source="./media/how-to-scale-out-for-migration/import-modal-scale-out.png" alt-text="导入配置模式":::

1. 登录（远程桌面）到主设备并执行以下 PowerShell 命令：

   `PS cd 'C:\Program Files\Microsoft Azure Appliance Configuration Manager\Scripts\PowerShell' `
    
    `PS .\ExportConfigFiles.ps1 `

1. 将通过运行上述命令创建的 zip 文件复制到横向扩展设备。 zip 文件包含注册横向扩展设备所需的配置文件。

1. 在上一步打开的弹出窗口中，选择复制的配置 zip 文件的位置，然后单击“保存”。

成功导入文件之后，横向扩展设备的注册就完成了，它会显示上次成功导入的时间戳。 还可以通过单击“查看详细信息”来查看注册详细信息。
:::image type="content" source="./media/how-to-scale-out-for-migration/import-success.png" alt-text="屏幕截图显示 Azure Migrate 项目的横向扩展设备注册。":::

此时，应该重新验证横向扩展设备是否能够连接到 vCenter Server。 单击“重新验证”，以验证横向扩展设备中的 vCenter Server 连接。
:::image type="content" source="./media/how-to-scale-out-for-migration/view-sources.png" alt-text="屏幕截图显示了要验证的查看凭据和发现源。":::

> [!IMPORTANT]
> 如果在主设备上编辑 vCenter Server 凭据，请确保将配置文件再次导入到横向扩展设备以获取最新配置并继续进行任何正在进行的复制。<br/> 如果不再需要横向扩展设备，请确保禁用该横向扩展设备。 [详细了解](./common-questions-appliance.md)如何在不需要时禁用横向扩展设备。

## <a name="replicate"></a>复制

1. 注册横向扩展设备后，在“Azure Migrate: 服务器迁移”磁贴上，单击“复制”。

2.  按照屏幕上的步骤开始复制更多虚拟机。 

有了横向扩展设备，现在可以同时复制 500 个 VM。 还可以通过 Azure 门户按批次迁移 VM（每批 200 个）。

Azure Migrate 服务器迁移工具将负责在主设备和横向扩展设备之间分配虚拟机以进行复制。 完成复制后，你可以迁移虚拟机。

> [!TIP]
> 如果要迁移大量虚拟机，我们建议按批次迁移（每批 200 个），以获得最佳性能。
  
## <a name="next-steps"></a>后续步骤

在本文中，我们已了解到：
- 如何配置横向扩展设备
- 如何使用横向扩展设备复制 VM


[详细了解](./tutorial-migrate-vmware.md)如何使用“Azure Migrate: 服务器迁移”工具迁移服务器。
