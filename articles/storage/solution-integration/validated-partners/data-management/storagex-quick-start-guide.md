---
title: 使用 Data Dynamics StorageX 分析文件数据并将文件数据迁移到 Azure
titleSuffix: Azure Storage
description: Data Dynamics StorageX 实施入门指南。 指南介绍如何将数据迁移到 Azure 文件存储、Azure NetApp 文件、Azure Blob 存储或任何可用的 ISV NAS 解决方案
author: dukicn
ms.author: nikoduki
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 8613d641f305f93598ac070c5eaaeee5fed81b8d
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290232"
---
# <a name="migrate-data-to-azure-with-data-dynamics-storagex"></a>使用 Data Dynamics StorageX 将数据迁移到 Azure

本文帮助你在 Microsoft Azure 中部署 Data Dynamics StorageX。 我们将围绕 StorageX 工作方式、部署先决条件、安装过程和操作方法介绍一些关键概念，以提供操作方面的指导。 有关更深入的信息，请访问 [Data Dynamics 客户门户](https://www.datdynsupport.com/)。

Data Dynamics StorageX 是一个统一的非结构化数据管理平台，能够跨异构存储环境分析、管理和移动数据。 基本功能包括：
- 数据移动功能
    - NAS 到 NAS（进行分析或不进行分析）
    - NAS 到对象复制
    - NAS 到对象存档
- 重复文件检查报告
- 打开共享报表
- 使用自定义标记进行文件元数据分析
  
可以在我们的[比较表](./migration-tools-comparison.md)中发现 StorageX 的更多功能以及与类似工具的比较。

本文档涵盖三个主要主题：
- 在 Azure 环境中部署 StorageX 产品/服务的过程，
- 将文件和云存储资源添加到 StorageX 的基本步骤，
- 创建数据移动策略。

## <a name="reference-architecture"></a>参考体系结构

下图提供了本地到 Azure 的部署以及 StorageX 的 Azure 内部部署的参考体系结构。

:::image type="content" source="./media/storagex-quick-start-guide/storagex-architecture.png" alt-text="StorageX 实施参考体系结构图示":::

## <a name="storagex-interoperability-matrix"></a>Veeam 互操作性一览表

| 功能 | SMB | NFS | 说明 |
| ------- |--- | --- | --- |
| **迁移** | SMB 到 SMB | NFSv3 到 NFSv3 | 可以组合使用以符合多协议 |
| 文件到对象存档 | SMB 到 Azure Blob 存储 | NFSv3 到 Azure Blob 存储 | 存档数据以优化成本 |
| 文件到对象复制 | SMB 到 Azure Blob 存储 | NFSv3 到 Azure Blob 存储 | 复制数据以增加保护 |

## <a name="before-you-begin"></a>在开始之前

前期规划将简化迁移并降低风险。 以下是简化迁移的一些提示：

- 收集有关源和目标服务的数据，
- 请务必列出连接点和所有必要的网络端口。
- Azure 中的 StorageX 服务器需要成为 Active Directory 基础结构的一部分。 确保与具有 Active Directory 特权的管理员联系，以将服务器添加到 Active Directory。
- 要简化实施，可使用 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview)。

### <a name="get-started-with-azure"></a>Azure 入门

Microsoft 提供一个框架来帮助你开始使用 Azure。 [云采用框架](/azure/architecture/cloud-adoption/) (CAF) 提供实现企业数字变革的详细措施，以及有关规划生产级云采用方案的综合指导。 CAF 包含循序渐进的 [Azure 设置指南](/azure/cloud-adoption-framework/ready/azure-setup-guide/)，帮助你快速安全地开始运作。 可以在 [Azure 门户](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)中找到交互式版本。 在其中可以找到示例体系结构、有关部署应用程序的具体最佳做法，以及用于将你从新手培养成为 Azure 专家的免费培训资源。

### <a name="considerations-for-migrations"></a>迁移注意事项

考虑将文件数据迁移到 Azure 时，需要注意几个方面。 继续之前，请先详细了解以下内容：

- [Azure 存储迁移概述](../../../common/storage-migration-overview.md)
- [迁移工具比较一览表](./migration-tools-comparison.md)中 Data Dynamics StorageX 支持的最新功能。

请记住，需有足够的网络容量才能在不影响生产应用程序的情况下支持迁移。 本部分概述了可用于评估网络需求的工具和方法。

#### <a name="determine-unutilized-internet-bandwidth"></a>确定未利用的 Internet 带宽

必须知道在日常中通常有多少带宽不被利用到（即“空余空间”）。 帮助评估是否达到以下目标：

- 不将 Azure Data Box 用于脱机方法时的初始迁移时间
- 在最终切换到目标文件服务之前执行增量重新同步所需的时间

使用以下方法来确定到 Azure 的可任意占用的带宽空余空间。

- 如果你是现有的 Azure ExpressRoute 客户，请在 Azure 门户中查看[线路用量](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics)。
- 请与 ISP 联系并请求提供报表，以显示当前每日和每月利用率。
- 有多个工具可以通过监视路由器/交换机级别的网络流量来测量利用率：
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

#### <a name="creating-a-storagex-service-account"></a>创建 StorageX 服务帐户

对于 SMB 迁移，建议使用服务帐户来运行 StorageX 服务。 服务帐户允许出于提供支持的原因而跟踪访问权限，同时提供授予完全访问权限的适当特权。

在 StorageX 通用数据引擎上运行数据移动策略的进程将使用 StorageX 服务帐户。 必须在 Active Directory 域中为该帐户分配所有必要的特权。

如果你的环境中允许，可以创建一个域管理员之类的帐户。 建议的方法是直接在 NAS 服务器上授予权限。 要执行后一种操作，请将服务帐户置于“管理员”和“备份操作员”组中。

StorageX 服务帐户需要以下特权：

- SeServiceLogonRight
- Sesecurityprivilege 
- SeBackupPrivilege 
- SeRestorePrivilege 
- SeAuditPrivilege
- SeInteractiveLogonRight

要访问 NFS，则需要 StorageX 服务器 IP 的导出规则，并授予 RW 和 root 访问权限。 例如，“root=10.2.3.12, rw=10.2.3.12”，其中 10.2.3.12 是 StorageX 服务器 IP 地址。

## <a name="deployment-guide"></a>部署指南

本节介绍关如何在 Azure 环境中部署 StorageX 服务器和其他 StorageX 组件。

在开始实施之前，有几个先决条件很重要：

- 必须标识并创建目标存储服务，
- 请确保已打开正确的端口来访问存储服务。 您可以使用 Microsoft 的 [portqryui](https://www.microsoft.com/download/details.aspx?id=24009) 工具来验证必要的端口是否已打开。

可在此处了解详细信息：

- 如何创建 [Azure 文件共享](../../../files/storage-how-to-create-file-share.md)
- 在 Azure NetApp 文件中，如何创建 [SMB 卷](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md)或 [NFS 导出](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md)

对于任何 SMB 迁移，在添加任何资源之前，都必须先正确设置 Active Directory。 我们将使用包含新卷的现有 Azure NetApp 文件实施作为迁移目标。 要创建新的 Azure NetApp 文件卷，我们需要首先执行以下操作：

- [创建 Azure NetApp 文件帐户](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-netapp-account)
- [创建容量池](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#set-up-a-capacity-pool)
- [将 Azure NetApp 文件帐户加入 Active Directory 域](../../../../azure-netapp-files/create-active-directory-connections.md)

完成 Azure NetApp 文件的配置后，我们便可以创建用于迁移的共享。 

1. 导航到现有的 Azure NetApp 文件帐户，然后选择“卷”。 选择“添加卷”。 命名卷，选择容量池和网络设置。

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-1.png" alt-text="添加新 Azure NetApp 文件卷的屏幕截图":::

1. 选择 SMB 协议，选择现有的 Active Directory 连接，并提供共享名称。 

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-2.png" alt-text="Azure NetApp 文件卷中 SMB 属性的屏幕截图":::

1. 如果要使用标签，请添加标签，然后创建共享。 

    创建卷后，我们可以继续进行 StorageX 部署。

1.  在 Azure 中部署 Data Dynamics 数据移动和移动性产品/服务。 建议的方法是使用 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview)。 将已部署的服务器添加到 Active Directory 域中。
1.  在已部署的服务器上，验证是否打开了所有必需的网络端口。
1.  运行 StorageX 安装脚本。 登录到 Azure 中部署的服务器时，可在桌面上使用安装脚本。 
    所有安装文件和输出日志都位于 C:\ProgramData\data Dynamics\StorageX 文件夹中。 在该文件夹中，InstallationFiles 子文件夹包含压缩的源安装文件，而 SilentInstaller-DD 文件夹包含配置 XML 文件和安装日志。 

    默认情况下，安装脚本将部署以下服务：

    - StorageX
    - StorageX API
    - StorageX 处理引擎
    - StorageX 通用数据引擎
    - StorageX 文件恢复
    - Elasticsearch
    - Microsoft SQL Express
    - Microsoft SQL Management Studio
    - Kibana
    - 任何支持软件（例如 Java）

    安装脚本有一个 XML 配置文件，可以在其中自定义这些不同的服务，但我们不建议手动修改配置。

    要安装，请右键单击桌面上的 StorageXPOCSilentInstaller - Shortcut，然后选择“以管理员身份运行”。  安装开始后，将要求你提供 StorageX 服务帐户以及该帐户的密码。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-1.jpg" alt-text="安装 CLI 的屏幕截图":::

    安装脚本将继续安装所有服务。 安装过程完成后，命令行界面中会显示消息。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-2.jpg" alt-text="显示已完成安装的屏幕截图":::

    StorageX 将自动启动。

## <a name="migration-guide"></a>迁移指南

安装完成并启动所有服务后，我们便可以开始迁移过程。

1.  打开 StorageX 控制台。
1.  单击“存储资源”选项卡。
1.  在“我的资源”视图中，添加您的 NAS 设备或存储服务，在 StorageX 中称为“存储资源”。  可以添加不同类型：
    - 为本地 NAS 设备、Windows 文件服务器、Azure NetApp 文件或通用文件服务器添加文件存储资源，
    - 为 S3 或 Azure Blob 存储（包括 Azure 文件服务）添加对象存储资源，
    - 为 DFS 文件服务器添加 DFS 命名空间。

    选择“添加文件存储资源”以启动添加 Azure NetApp 文件迁移目标的向导。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-1.png" alt-text="将新资源添加到 StorageX 部署的屏幕截图":::

1. 为 CIFS 和 SMB 迁移选择通用 CIFS。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-2.png" alt-text="将 Generic CIFS 资源添加到 StorageX 部署的屏幕截图":::

1. 浏览以查找 Azure NetApp 文件的计算机帐户。 在将 Azure NetApp Files 计算机帐户添加到 Active Directory 域时便会创建这个帐户。
   
    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-3.png" alt-text="添加 Azure NetApp 文件计算机帐户的屏幕截图":::

    完成向导，确认帐户已添加并包含我们创建的共享。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-4.png" alt-text="浏览已添加资源的屏幕截图":::

    必须将每个源和目标添加为单独的资源。 演示如何添加 Azure NetApp 文件共享的示例，对于其他服务，可能会略有不同。

1.  单击“数据移动”选项卡。

1.  在树窗格中，右键单击要创建新策略的文件夹，然后选择“分阶段迁移策略”。

1.  按照向导中的步骤创建分阶段迁移策略。 选择适当的“来源”和“目的地”目标。 

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-1.png" alt-text="选择适当的源和目的地目标的屏幕截图":::

1. 提供策略的名称和描述并完成向导。

1.  右键单击新策略并选择“运行”以运行测试迁移。 建议检查源资源上多个文件的安全配置，验证是否提供了所需的权限。

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-2.png" alt-text="运行已定义策略的屏幕截图":::

1. 检查策略的状态，确保已成功运行初始迁移。 

## <a name="support"></a>支持

如果出现问题，Microsoft 和 Data Dynamics 可以使用常规支持渠道提供帮助。 如果遇到基础结构问题或任何 Azure 服务方面的问题，请与 Microsoft 联系。 有关与 Data Dynamics StorageX 安装相关的问题，请联系 Data Dynamics。

### <a name="how-to-open-a-case-with-azure"></a>如何向 Azure 提出案例

在 [Azure 门户](https://portal.azure.com)的顶部搜索栏中搜索“支持”。 选择“帮助 + 支持” -> “新建支持请求” 。

### <a name="how-to-open-a-case-with-data-dynamics"></a>如何向 Data Dynamics 提出案例

转到 [Data Dynamics 支持门户](https://www.datdynsupport.com/)。 如果尚未注册，请提供电子邮件地址，我们的支持团队将为你创建一个帐户。 在你登录后，提出用户请求。 如果已提出 Azure 支持案例，请记下创建请求时的支持请求编号。

## <a name="next-steps"></a>后续步骤

提供以下各种资源以帮助你了解更多信息：

- [Azure 存储迁移概述](../../../common/storage-migration-overview.md)
- [迁移工具比较一览表](./migration-tools-comparison.md)中 Data Dynamics StorageX 支持的功能
- [Data Dynamics](https://www.datadynamicsinc.com/)
- [数据 Dynamics 客户门户](https://www.datdynsupport.com/)包含有关 StorageX 的完整文档