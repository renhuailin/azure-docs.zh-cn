---
title: 使用 Azure 文件存储 (SMB) 实现 Windows 上 Azure VM 的 SAP NW 高可用性 | Microsoft Docs
description: 使用适用于 SAP 应用程序的 Azure 文件存储 (SMB) 实现 Windows 上 Azure VM 的 SAP NetWeaver 高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: stmuelle
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 09/13/2021
ms.author: stmuelle
ms.openlocfilehash: d1e01f51d4d83254031407a125c3578fb3feb718
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594299"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-files-premium-smb-for-sap-applications"></a>使用适用于 SAP 应用程序的 Azure 文件存储高级 SMB 实现 Windows 上 Azure VM 的 SAP NetWeaver 高可用性

## <a name="introduction"></a>简介
Microsoft 和 SAP 现在完全支持 Azure 文件存储高级 SMB。 SWPM 1.0 SP32 和 SWPM 2.0 SP09 及更高版本支持 Azure 文件存储高级 SMB 存储 。  需要满足特殊的要求才能调整 Azure 文件存储高级 SMB 共享的大小。 本文档提供有关如何在 Azure 文件存储高级 SMB 上分配工作负载、如何充分调整 Azure 文件存储高级 SMB 的大小以及 Azure 文件存储高级 SMB 最低安装要求的具体建议。

高可用性 SAP 解决方案需要通过高度可用的文件共享来托管 sapmnt、trans 和 interface 目录  。 Azure 文件存储高级 SMB 是一个简单的 Azure PaaS 解决方案，适用于 Windows 版 SAP 环境的共享文件系统。 Azure 文件存储高级 SMB 可与可用性集和可用性区域结合使用。 Azure 文件存储高级 SMB 还可用于实现到另一个区域的灾难恢复方案。  
 
## <a name="sizing--distribution-of-azure-files-premium-smb-for-sap-systems"></a>适用于 SAP 系统的 Azure 文件存储高级 SMB 的大小调整和分发

在规划 Azure 文件存储高级 SMB 的部署时，应评估以下几点：
* 可为每个存储帐户创建文件共享名 sapmnt 一次。  可在同一 /sapmnt 共享中创建其他 SID 作为目录，例如 - /sapmnt/\<SID1\> 和 /sapmnt/\<SID2\>   
* 选择适当的大小、IOPS 和吞吐量。  对于每个 SID，建议的共享大小为 256 GB。  最大共享大小为 5120 GB
* 对于每个存储帐户的包含 1-2 百万个以上文件的极大型 sapmnt 共享，Azure 文件存储高级 SMB 可能无法发挥最佳性能 **** 。需要使用数百万个批处理作业创建数百万个作业日志文件的客户应根据 [SAP 说明 16083][16083] 定期重新组织这些文件。如果需要，可将旧作业日志移动/存档到另一个 Azure 文件存储高级 SMB。 如果预计 sapmnt 极大，则应考虑采用替代方案（例如 Azure ANF）。
* 建议使用专用网络终结点
* 避免将过多的 SID 合并到单个存储帐户及其文件共享。
* 一般指导原则是，将最多 2 到 4 个非生产 SID 合并在一起。
* 不要将整个开发、QAS + 生产环境合并到一个存储帐户和/或文件共享。共享失败会导致整个 SAP 环境发生停机。
* 不建议将 sapmnt 和 transport 目录合并到同一个存储帐户（极小的系统除外） 。 在安装 SAP PAS 实例期间，SAPInst 将请求传输主机名。  不同存储帐户的 FQDN 应输入为 <storage_account>.file.core.windows.net。
* 不要将用于接口的文件系统合并到 /sapmnt/\<SID> 所在的同一个存储帐户 
* SAP 用户/组必须添加到“sapmnt”共享，并且应在 Azure 门户中为其设置以下权限：存储文件数据 SMB 共享提升参与者。

出于重要的原因，需要将 Transport、Interface 和 sapmnt 分隔到单独的存储帐户  。  将这些组件分布到单独的存储帐户可提高吞吐量和复原能力并简化性能分析。  如果将许多 SID 和其他文件系统合并到单个 Azure 文件存储帐户，并且由于达到吞吐量限制而导致存储帐户性能低下，则很难确定问题是由哪个 SID 或应用程序造成的。 

## <a name="planning"></a>规划 
> [!IMPORTANT]
> 通过 Active Directory 集成在 Azure 文件存储高级 SMB 上安装 SAP 高可用性系统需要跨团队协作。  强烈建议由基础团队、Active Directory 团队和 Azure 团队共同完成这些任务： 
>
* Azure 团队 – 设置并配置存储帐户、脚本执行和 AD 目录同步。
* Active Directory 团队 – 创建用户帐户和组。
* 基础团队 – 运行 SWPM 并设置 ACL（如果需要）。

通过 Active Directory 集成在 Azure 文件存储高级 SMB 上安装 SAP NetWeaver 高可用性系统的先决条件。

* 必须将 SAP 服务器加入 Active Directory 域。
* 必须使用 Azure AD Connect 将包含 SAP 服务器的 Active Directory 域复制到 Azure Active Directory。
* 强烈建议在 Azure 环境中至少提供一个 Active Directory 域控制器，以避免经由 Express Route 来联系本地域控制器。
* Azure 支持团队应参考 [Active Directory 集成](/azure/storage/files/storage-files-identity-auth-active-directory-enable#videos)文档来评审 Azure 文件存储 SMB。 视频演示了其他配置选项，这些选项已经过修改 (DNS)，并且为简单起见，跳过了一些选项 (DFS-N)。 尽管如此，这些配置选项都是有效的。 
* 执行 Azure 文件存储 PowerShell 脚本的用户必须有权在 Active Directory 中创建对象。
* 需要 SWPM 1.0 SP32 和 SWPM 2.0 SP09 或更高版本。SAPInst 补丁必须是 749.0.91 或更高版本。
* 应在执行脚本的 Windows Server 上安装最新版本的 PowerShell。 

## <a name="installation-sequence"></a>安装顺序
 1. Active Directory 管理员应提前在本地 Windows AD 中创建 3 个拥有“本地管理员”权限的域用户和一个全局组：SAPCONT_ADMIN@SAPCONTOSO.local 拥有域管理员权限，用于以 SAP 系统用户和 SAP_\<SAPSID>_GlobalAdmin 组身份运行 SAPInst、\<sid>adm 和 SAPService\<SID>      。 SAP 安装指南包含了这些帐户所需的具体详细信息。  SAP 用户帐户不应是域管理员。 一般不建议使用 \<sid>adm 来运行 SAPInst。
 2. Active Directory 管理员或 Azure 管理员应检查 Azure AD Connect Synchronization Service Manager。 默认情况下，复制到 Azure Active Directory 大约需要 30 分钟。 
 3. Azure 管理员应完成以下任务：
     1. 使用“高级 ZRS”或“LRS”创建存储帐户 。 采用区域部署的客户应选择“ZRS”。 需在此处选择是要设置标准帐户还是高级帐户：![创建存储帐户 - 步骤 1 的 Azure 门户屏幕截图](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-1.png)创建存储帐户 - 步骤 1 的 Azure 门户屏幕截图 
         > [!IMPORTANT]
         > 对于生产用途，建议使用高级帐户。 对于非生产用途，使用标准帐户就足够了。 
         >
         ![创建存储帐户 - 步骤 2 的 Azure 门户屏幕截图](media/virtual-machines-shared-sap-high-availability-guide/create-storage-account-2.png)创建存储帐户 - 步骤 2 的 Azure 门户屏幕截图
         
         在此屏幕中，可以保留默认设置。
        
         ![创建存储帐户 - 步骤 3 的 Azure 门户屏幕截图](media/virtual-machines-shared-sap-high-availability-guide/create-sa-4.png)创建存储帐户 - 步骤 3 的 Azure 门户屏幕截图 
         
         在此步骤中决定使用专用终结点。
     1. 为存储帐户选择专用网络终结点。
     根据需要，将 DNS A 记录添加到 <storage_account_name>.file.core.windows.net 的 Windows DNS（可能需要在新的 DNS 区域中执行此操作）。  请咨询 DNS 管理员。  新区域不应在组织外部更新。  
         ![pivate-endpoint-creation](media/virtual-machines-shared-sap-high-availability-guide/create-sa-3.png)专用终结点定义的 Azure 门户屏幕截图。
         ![private-endpoint-dns-1](media/virtual-machines-shared-sap-high-availability-guide/pe-dns-1.png)专用终结点 DNS 定义的 DNS 服务器屏幕截图。    
     1. 创建具有适当大小的 sapmnt 文件共享。  建议的大小为 256GB，它可提供 650 IOPS、75 MB/秒的传出速率和 50 MB/秒的传入速率。
         ![create-storage-account-5](media/virtual-machines-shared-sap-high-availability-guide/create-sa-5.png)SMB 共享定义的 Azure 门户屏幕截图。 
      
     1. 下载 [Azure 文件存储 GitHub](/azure/storage/files/storage-files-identity-ad-ds-enable#download-azfileshybrid-module) 内容并执行[脚本](/azure/storage/files/storage-files-identity-ad-ds-enable#run-join-azstorageaccountforauth)。   
     此脚本将在 Active Directory 中创建计算机帐户或服务帐户。  运行脚本的用户必须具有以下属性： 
         * 运行脚本的用户必须有权在包含 SAP 服务器的 Active Directory 域中创建对象。 通常会使用诸如 SAPCONT_ADMIN@SAPCONTOSO.local 的域管理员帐户 
         * 在执行脚本之前，请确认此 Active Directory 域用户帐户已与 Azure Active Directory (AAD) 同步。  例如，打开 Azure 门户并导航到 AAD 用户，然后检查用户 SAPCONT_ADMIN@SAPCONTOSO.local 是否存在并验证 AAD 用户帐户 SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com 。
         * 向此 Azure Active Directory 用户帐户授予对包含文件共享的存储帐户所在的资源组的“参与者 RBAC”角色。  在此示例中，为用户 SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com 授予了对相应资源组的参与者角色  
         * 应在使用拥有上面指定权限的 Active Directory 域用户帐户（在此示例中将使用帐户 SAPCONT_ADMIN@SAPCONTOSO.local）登录到 Windows 服务器时执行该脚本。
         >[!IMPORTANT]
         > 执行 PowerShell 脚本命令 Connect-AzAccount 时，强烈建议输入对应于并映射到用于登录 Windows Server 的 Active Directory 域用户帐户的 Azure Active Directory 用户帐户（在此示例中为用户帐户 SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com ）
         >
         在此示例场景中，Active Directory 管理员将以 SAPCONT_ADMIN@SAPCONTOSO.local 身份登录到 Windows Server，并在使用 PS 命令 Connect-AzAccount 时以用户 SAPCONT_ADMIN@SAPCONTOSO.onmicrosoft.com 的身份进行连接  。  最好是由 Active Directory 管理员和 Azure 管理员共同完成此任务。
         ![powershell-script-1](media/virtual-machines-shared-sap-high-availability-guide/ps-script-1.png)用于创建本地 AD 帐户的 PowerShell 脚本的屏幕截图。

         ![smb-configured-screenshot](media/virtual-machines-shared-sap-high-availability-guide/smb-config-1.png)成功执行 PowerShell 脚本后的 Azure 门户屏幕截图。 

         以下设置应显示为“已配置”  
         存储 -> 文件共享“Active Directory: 已配置”
     1. 在 Azure 门户中，将具有“存储文件数据 SMB 共享提升参与者”角色的 SAP 用户 \<sid>adm、SAPService\<SID> 和 SAP_\<SAPSID>_GlobalAdmin 组分配到 Azure 文件存储高级 SMB 文件共享    
     1. 安装后检查 sapmnt 文件共享的 ACL，并添加 DOMAIN\CLUSTER_NAME$ 帐户、DOMAIN\\\<sid>adm、DOMAIN\SAPService\<SID> 和组 SAP_\<SID>_GlobalAdmin    。 这些帐户和组应该可以完全控制 sapmnt 目录。

         > [!IMPORTANT]
         > 此步骤必须在安装 SAPInst 之前完成，否则 SAPInst 在文件共享上创建目录和文件后，将很难甚至无法更改 ACL
         >
         ![ACL 属性](media/virtual-machines-shared-sap-high-availability-guide/smb-share-properties-1.png)分配的用户权限的 Windows 资源管理器屏幕截图。

         以下屏幕截图显示如何通过选择“对象类型”->“计算机”来添加“计算机”帐户![将群集名称添加到本地 AD 的 Windows Server 屏幕截图](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-2.png)将群集名称添加到本地 AD 的 Windows Server 屏幕截图。
         
         可以通过从“对象类型”中选择“计算机”找到 DOMAIN\CLUSTER_NAME$  
         ![添加 AD 计算机帐户 - 步骤 2 的屏幕截图](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-3.png)添加 AD 计算机帐户 - 步骤 2 的屏幕截图![添加 AD 计算机帐户 - 步骤 3 的屏幕截图](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-4.png)添加 AD 计算机帐户 - 步骤 3 的屏幕截图![计算机帐户访问属性的屏幕截图](media/virtual-machines-shared-sap-high-availability-guide/add-computer-account-5.png)计算机帐户访问属性的屏幕截图。

     8. 如果需要，请将为 Azure 文件存储创建的计算机帐户移到未设置帐户过期时间的 Active Directory 容器。  计算机帐户的名称是存储帐户的短名称 

     
     > [!IMPORTANT]
     > 若要初始化 SMB 共享的 Windows ACL，需要将该共享装载到驱动器号一次。
     >
     存储密钥为密码，用户为 Azure\\\<SMB share name>，如下所示：![一次性 net use 装载命令](media/virtual-machines-shared-sap-high-availability-guide/one-time-net-use-mount-1.png)SMB 共享的一次性 net use 装载命令的 Windows 屏幕截图。

 4. 基础管理员应完成以下任务：
     1. [在 ASCS/ERS 节点上安装 Windows 群集并添加云见证](sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
     2. 首次安装群集节点时，系统会要求提供 Azure 文件存储 SMB 存储帐户名称。  输入 FQDN <storage_account_name>.file.core.windows.net。  如果 SAPInst 不接受 13 个以上的字符，则表示 SWPM 版本太低。
     3. [修改 ASCS/SCS 实例的 SAP 配置文件](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     4. [更新 WSFC 中 SAP \<SID> 角色的探测端口](sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)
     5. 继续为第二个 ASCS/ERS 节点进行 SWPM 安装。 只需为 SWPM 提供配置文件目录的路径。  输入配置文件目录的完整 UNC 路径。
     6. 输入 DB 和 PAS/AAS 安装的 UNC 配置文件路径。
     7. PAS 安装会要求提供传输主机名。 为 transport 目录提供单独的存储帐户名称的 FQDN。
     8. 验证 SID 和 trans 目录的 ACL。

## <a name="disaster-recovery-setup"></a>灾难恢复设置
Azure 文件存储高级 SMB 支持灾难恢复方案或跨区域复制方案。 可以使用此链接将 Azure 文件存储高级 SMB 目录中的所有数据持续同步到 DR 区域存储帐户。 在发生灾难恢复事件并将 ASCS 实例故障转移到 DR 区域后，将 SAPGLOBALHOST 配置文件参数更改为指向 DR 区域中的 Azure 文件存储 SMB。 应在 DR 存储帐户上执行相同的准备步骤，以将存储帐户加入 Active Directory 并为 SAP 用户和组分配 RBAC 角色。

## <a name="troubleshooting"></a>故障排除
在步骤 3.c 中下载的 PowerShell 脚本包含一个调试脚本，用于执行一些基本检查来验证配置。
```powershell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
![Powershell-script-output](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-2.png)调试脚本输出的 PowerShell 屏幕截图。

![Powershell-script-technical-info](media/virtual-machines-shared-sap-high-availability-guide/smb-share-validation-1.png)以下屏幕显示了用于验证域加入是否成功的技术信息。
## <a name="useful-links--resources"></a>有用的链接和资源

* SAP 说明 [2273806][2273806]：SAP 对存储或文件系统相关解决方案的支持 
* [在 Windows 故障转移群集上安装 SAP NetWeaver 高可用性，在 Azure 上安装适用于 SAP ASCS/SCS 实例的文件共享](./sap-high-availability-installation-wsfc-file-share.md) 
* [适用于 SAP NetWeaver 的 Azure 虚拟机高可用性体系结构和方案](./sap-high-availability-architecture-scenarios.md)
* [在 ASCS 群集配置中添加探测端口](sap-high-availability-installation-wsfc-file-share.md)
* [在故障转移群集上安装 (A)SCS 实例](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)

[16083]:https://launchpad.support.sap.com/#/notes/16083
[2273806]:https://launchpad.support.sap.com/#/notes/2273806
