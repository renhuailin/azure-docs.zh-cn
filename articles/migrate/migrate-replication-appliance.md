---
title: Azure Migrate 复制设备
description: 了解基于代理的 VMware 迁移的 Azure Migrate 复制设备。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4a06267aae473ecc1b42812f7ed07405faafd7aa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607463"
---
# <a name="replication-appliance"></a>复制设备

本文介绍了使用基于代理的迁移将 VMware VM、物理计算机和私有/公有云 VM 迁移到 Azure 时 [Azure Migrate: 服务器迁移](migrate-services-overview.md#azure-migrate-server-migration-tool)工具使用的复制设备。 


## <a name="overview"></a>概述

在设置 VMware VM 或物理服务器的基于代理的迁移时，将部署复制设备。 复制设备的部署方式包括本地计算机、VMware VM 或物理服务器。 其运行：

- 复制设备：复制设备可协调通信，并可管理数据复制，以将本地 VMware VM 和物理服务器复制到 Azure。
- **流程服务器**：流程服务器默认安装在复制设备上，并执行以下操作：
    - **复制网关**：充当复制网关。 从允许进行复制的计算机接收复制数据。 通过缓存、压缩和加密对复制数据进行优化，然后将其发送到 Azure。
    - **代理安装程序**：执行移动服务的推送安装。 必须在要复制以进行迁移的每台本地计算机上安装和运行此服务。

## <a name="appliance-deployment"></a>设备部署

**用于** | **详细信息**
--- |  ---
**基于 VMware VM 代理的迁移** | 从 Azure Migrate 中心下载 .OVA 模板，并导入到 vCenter Server 以创建设备 VM。
**基于物理计算机代理的迁移** | 如果没有 VMware 基础结构，或者无法使用 .OVA 模板创建 VMware VM，请从 Azure Migrate 中心下载软件安装程序，并运行它以设置设备计算机。

> [!NOTE]
> 如果要在 Azure 政府版中部署，请使用安装文件来部署复制设备。

## <a name="appliance-requirements"></a>设备要求

使用 Azure Migrate 中心提供的 OVA 模板设置复制设备时，设备将运行 Windows Server 2016 并符合支持要求。 如果在物理服务器上手动设置复制设备，请确保它符合要求。

**组件** | **要求**
--- | ---
 | **VMware VM 设备**
PowerCLI | 如果复制设备在 VMware VM 上运行，则应安装 [PowerCLI 版本 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1)。
NIC 类型 | VMXNET3（如果设备是 VMware VM）
 | 硬件设置
CPU 核心数 | 8
RAM | 16 GB
磁盘数目 | 3 个：操作系统磁盘、进程服务器缓存磁盘和保留驱动器。
可用磁盘空间（缓存） | 600 GB
可用磁盘空间（保留磁盘） | 600 GB
软件设置 |
操作系统 | Windows Server 2016 或 Windows Server 2012 R2
许可证 | 设备附带有效期为 180 天的 Windows Server 2016 评估许可证。<br/><br/> 如果评估期临近过期，建议下载并部署新设备，或者激活设备 VM 的操作系统许可证。
操作系统区域设置 | 英语 (en-us)
TLS | 应启用 TLS 1.2。
.NET Framework | 计算机上必须安装 .NET Framework 4.6 或更高版本（已启用强加密）。
MySQL | 必须在设备上安装 MySQL。<br/> 应安装 MySQL。 可以手动安装，或者让 Azure Migrate 在设备部署期间进行安装。
其他应用 | 请勿在复制设备上运行其他应用。
Windows Server 角色 | 请勿启用以下角色： <br> - Active Directory 域服务 <br>- Internet Information Services <br> - Hyper-V
组策略 | 请勿启用以下组策略： <br> - 阻止访问命令提示符。 <br> - 阻止访问注册表编辑工具。 <br> - 信任文件附件的逻辑。 <br> - 打开脚本执行。 <br> [了解详细信息](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - 无预先存在的默认网站 <br> - 端口 443 上没有预先存在的网站/应用程序侦听 <br>- 启用[匿名身份验证](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - 启用 [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10)) 设置
**网络设置** |
IP 地址类型 | 静态
端口 | 443（控制通道协调）<br>9443（数据传输）
NIC 类型 | VMXNET3

## <a name="mysql-installation"></a>MySQL 安装 

必须在复制设备计算机上安装 MySQL。 可以使用以下方法之一来安装。

**方法** | **详细信息**
--- | ---
手动下载并安装 | 下载 MySQL 应用程序，将其置于 C:\Temp\ASRSetup 文件夹中，然后手动安装。<br/> 安装设备时，MySQL 显示为“已安装”。
无在线下载 | 将 MySQL 安装程序应用程序置于 C:\Temp\ASRSetup 文件夹中。 安装设备并选择下载并安装 MySQL 时，将使用所添加的安装程序来完成安装过程。
在 Azure Migrate 中下载并安装 | 当你安装设备并且系统提示 MySQL 时，请选择“下载并安装”。

## <a name="url-access"></a>URL 访问

复制设备需要在 Azure 公共云中访问这些 URL。

**URL** | **详细信息**
--- | ---
\*.backup.windowsazure.com | 用于复制的数据传输和协调
\*.store.core.windows.net | 用于复制的数据传输和协调
\*.blob.core.windows.net | 用于访问存储所复制数据的存储帐户
\*.hypervrecoverymanager.windowsazure.com | 用于复制管理操作和协调
https:\//management.azure.com | 用于复制管理操作和协调
*.services.visualstudio.com | 用于日志记录目的（可选）
time.windows.com | 用于检查系统时间与全球时间之间的时间同步。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | 设备设置需要访问以下这些 URL。 它们由 Azure Active Directory 用于访问控制和标识管理
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下载。 在某些区域中，下载可能会被重定向到 CDN URL。 如果需要，请确保还允许 CDN URL。


## <a name="azure-government-url-access"></a>Azure 政府版 URL 访问

复制设备需要在 Azure 政府版中访问这些 URL。

**URL** | **详细信息**
--- | ---
\*.backup.windowsazure.us | 用于复制的数据传输和协调
\*.store.core.windows.net | 用于复制的数据传输和协调
\*.blob.core.windows.net | 用于访问存储所复制数据的存储帐户
\*.hypervrecoverymanager.windowsazure.us | 用于复制管理操作和协调
https:\//management.usgovcloudapi.net | 用于复制管理操作和协调
*.services.visualstudio.com | 用于日志记录目的（可选）
time.nist.gov | 用于检查系统时间与全球时间之间的时间同步。
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | 带 OVA 设备设置需要访问以下这些 URL。 它们由 Azure Active Directory 用于访问控制和标识管理。
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | 完成 MySQL 下载。 在某些区域中，下载可能会被重定向到 CDN URL。 如果需要，请确保还允许 CDN URL。  

>[!Note]
>
> 如果你的迁移项目具有专用终结点连接，则除了私有链接访问之外，还需要访问以下 URL：   
> - *.blob.core.windows.com - 用于访问存储已复制数据的存储帐户 在存储帐户附加了专用终结点的情况下，这是可选的，不是必需的。 
> - https:\//management.azure.com，用于复制管理操作和协调。 
>- https:\//login.microsoftonline.com <br/>https:\//login.windows.net <br/> https:\//www.live.com _和_ <br/> https:\//www.microsoft.com，由 Azure Active Directory 用于访问控制和标识管理

## <a name="port-access"></a>端口访问

**设备** | **Connection**
--- | ---
VM | 为了进行复制管理，VM 上运行的出行服务需要与 HTTPS 443 入站端口上的本地复制设备（配置服务器）通信。<br/><br/> VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在配置服务器计算机上运行）。 可以修改此端口。
复制设备 | 复制设备通过 HTTPS 443 出站端口来与 Azure 协调复制。
进程服务器 | 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。<br/> 默认情况下，进程服务器在复制设备上运行。


## <a name="replication-process"></a>复制过程

1. 为某台 VM 启用复制时，将使用指定的复制策略开始到 Azure 存储的初始复制。 
2. 流量通过 Internet 复制到 Azure 存储公共终结点。 不支持通过站点到站点虚拟专用网络 (VPN) 将流量从本地站点复制到 Azure。
3. 初始复制完成后，增量复制将会开始。 计算机的受跟踪更改将记录在日志中。
4. 通信按如下方式发生：
    - VM 通过端口 HTTPS 443 入站与复制设备进行通信，以进行复制管理。
    - 复制设备通过 HTTPS 443 出站端口来与 Azure 协调复制。
    - VM 将复制数据发送到 HTTPS 9443 入站端口上的进程服务器（在复制设备上运行）。 可以修改此端口。
    - 进程服务器接收复制数据、优化和加密数据，然后通过 443 出站端口将其发送到 Azure 存储。
5. 复制数据首先登陆 Azure 中的缓存存储帐户。 处理这些日志，并将数据存储在 Azure 托管磁盘中。

![该关系图显示了复制过程的体系结构。](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>设备升级

需要从 Azure Migrate 中心手动升级设备。 我们建议始终运行最新版本。

1. 在“Azure Migrate”>“服务器”>“Azure Migrate: 服务器评估，基础结构服务器”中，选择“配置服务器”。
2. 在“配置服务器”中，当复制设备的新版本可用时，代理版本中会出现一个链接。  
3. 将安装程序下载到复制设备计算机，然后安装升级。 安装程序检测设备上运行的当前版本。
 
## <a name="next-steps"></a>后续步骤

- [了解如何](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance)为基于代理的 VMware VM 迁移设置复制设备。
- [了解如何](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance)为物理服务器设置复制设备。
