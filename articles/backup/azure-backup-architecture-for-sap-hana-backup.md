---
title: 适用于 SAP HANA 备份的 Azure 备份体系结构
description: 了解适用于 SAP HANA 备份的 Azure 备份体系结构。
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 7ca16e9990f097a98b8395c97567cabfbcb71eae
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084208"
---
# <a name="azure-backup-architecture-for-sap-hana-backup"></a>适用于 SAP HANA 备份的 Azure 备份体系结构

使用 [Azure 备份服务](/azure/backup/backup-overview)能够以一致的方式从应用程序中的 SAP HANA 数据库备份数据。 本文介绍 Azure 备份体系结构组件和过程。

## <a name="how-does-azure-backup-work-with-sap-hana-databases"></a>Azure 备份如何处理 SAP HANA 数据库？

Azure 备份提供一种流式处理备份解决方案来备份 Azure VM 上运行的 SAP HANA 数据库。 此备份产品/服务无需设置任何基础结构，因此无需部署和管理备份基础结构。

Azure 备份已通过 SAP 的 [Backint 认证](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)，它使用 SAP HANA 的本机 API 提供本机备份支持。 使用此解决方案，可以无缝备份和还原在 Azure VM 上运行的 SAP HANA 数据库，并使用 Azure 备份提供的企业管理功能。

[详细了解](/azure/backup/sap-hana-db-about#added-value) Azure 备份为 SAP HANA 提供的附加价值。

## <a name="where-is-the-data-backed-up"></a>备份的数据在何处？

Azure 备份将备份数据存储在恢复服务保管库中。 保管库是 Azure 中的联机存储实体，用于存储备份副本、恢复点和备份策略等数据。

[详细了解](/azure/backup/backup-azure-backup-faq#recovery-services-vault)恢复服务保管库。

## <a name="backup-agents"></a>备份代理

若要备份在 Azure VM 上运行的 SAP HANA 数据库，需要允许在 Azure VM 上安装插件（SAP HANA 备份代理）。 此插件与 HANA Backint 连接，可帮助 Azure 备份服务将数据移动到保管库。 它还使 Azure 备份能够执行还原。

## <a name="backup-types"></a>备份类型

[了解](/azure/backup/backup-architecture#sap-hana-backup-types) SAP HANA 备份类型。

## <a name="about-architecture"></a>关于体系结构

请参阅[适用于 SAP HANA 数据库的 Azure 备份的概要体系结构](/azure/backup/sap-hana-db-about#backup-architecture)。 若要详细了解备份过程，请参阅以下过程：

:::image type="content" source="./media/sap-hana-db-about/backup-architecture.png" alt-text="显示 SAP HANA 数据库备份过程的示意图。":::

1. 若要开始备份过程，请在 Azure 中[创建一个恢复服务保管库](/azure/backup/tutorial-backup-sap-hana-db#create-a-recovery-services-vault)。 此保管库将用来存储随时间推移创建的备份和恢复点。

1. 运行 SAP HANA 服务器的 Azure VM 将注册到保管库，并且要备份的数据库会被[发现](/azure/backup/tutorial-backup-sap-hana-db#discover-the-databases)。 要使 Azure 备份服务能够发现数据库，必须以 root 用户身份在 HANA 服务器上运行此[注册前脚本](https://go.microsoft.com/fwlink/?linkid=2173610)。 
   >[!Note]
   >在发现 HANA 实例中的数据库期间，请确保此实例已启动并正在运行。

1. 此外，请确保满足[其他先决条件](/azure/backup/tutorial-backup-sap-hana-db#prerequisites)。

   >[!Important]
   >确保满足设置正确网络连接的先决条件。 请参阅有关[如何设置具有其他网络组件的 SAP HANA 中运行的 Azure VM 以使用备份产品/服务](/azure/backup/tutorial-backup-sap-hana-db#set-up-network-connectivity)的建议。

1. 参阅有关[注册前脚本的功能](/azure/backup/tutorial-backup-sap-hana-db#what-the-pre-registration-script-does)的详细信息。 如果你尝试在不运行此脚本的情况下为 SAP HANA 数据库配置备份，可能会收到错误 UserErrorHanaScriptNotRun。

1. Azure 备份服务现在在已注册的 SAP HANA 服务器上安装适用于 HANA 的 Azure 备份插件。 此插件使用注册前脚本创建的备份用户来执行所有备份和还原操作。

1. 若要在已发现的数据库上[配置备份](/azure/backup/tutorial-backup-sap-hana-db#configure-backup)，请选择所需的备份策略并启用备份。

1. 适用于 SAP HANA 的 Azure 备份（已通过 Backint 认证的解决方案）不依赖于基础磁盘或 VM 类型。 备份由 SAP HANA 生成的流执行。

## <a name="backup-flow"></a>备份流

1. 计划的备份由在 HANA VM 上创建的 crontab 条目进行管理，而按需备份直接由 Azure 备份服务触发。

1. SAP HANA 备份引擎/Backint 收到备份请求后，会通过创建保存点并将数据移动到基础存储卷来为备份准备 SAP HANA 数据库。

1. 然后，Backint 从基础数据卷（租户数据库的索引服务器和 XS 引擎，以及 SYSTEMDB 的名称服务器）执行读取操作。 高级 SSD 磁盘可为备份流式处理操作提供最佳 I/O 吞吐量。 但是，将未缓存的磁盘与 M64I 一起使用可以提供更高的速度。

1. 为了流式处理备份数据，Backint 将创建最多三个管道，这些管道直接写入 Azure 备份的恢复服务保管库。

   如果未在设置中使用防火墙/NVA，则备份流将通过 Azure 网络传输到恢复服务保管库。 此外，可以设置[虚拟网络服务终结点](/azure/virtual-network/virtual-network-service-endpoints-overview)或[专用终结点](/azure/private-link/private-endpoint-overview)，以允许 SAP HANA 将备份流量直接发送到 Azure 存储（跳过 NVA/Azure 防火墙）。 此外，在使用防火墙/NVA 时，发往 Azure Active Directory 和恢复服务保管库的流量将通过防火墙/NVA，而不会影响整体备份性能。 

1. 对于非日志备份和日志备份，Azure 备份分别会尽量实现高达 420 MB/秒和 100 MB/秒的速度。 [详细了解](/azure/backup/tutorial-backup-sap-hana-db#understanding-backup-and-restore-throughput-performance)备份和还原吞吐量性能。

1. 详细日志将写入 SAP HANA 实例上的 backup.log 和 backint.log 文件 。

1. 备份流式处理完成后，目录将流式传输到恢复服务保管库。 如果备份（完整/差异/增量/日志）和此备份的目录已成功流式传输并保存到恢复服务保管库，则 Azure 备份认为备份操作成功。

参阅以下 SAP HANA 设置并查看上述备份操作的执行情况：

SAP HANA 设置方案：Azure 网络 - 没有任何 NVA/Azure 防火墙

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-without-nva-or-azure-firewall.png" alt-text="显示 Azure 网络没有任何 NVA/Azure 防火墙时的 SAP HANA 设置的示意图。":::

SAP HANA 设置方案：Azure 网络 - 具有 UDR + NVA/Azure 防火墙

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall.png" alt-text="显示 Azure 网络具有 UDR + NVA/Azure 防火墙时的 SAP HANA 设置的示意图。":::

>[!Note]
>当 SAP HANA 流备份到 Azure 存储/恢复服务保管库（数据平面）时，NVA/Azure 防火墙可能会增加开销。 请参阅上图中的第 6 点。

SAP HANA 设置方案：Azure 网络 - 具有 UDR + NVA/Azure 防火墙 + 专用终结点或服务终结点

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall-and-private-endpoint-or-service-endpoint.png" alt-text="显示 Azure 网络具有 UDR + NVA/Azure 防火墙 + 专用终结点或服务终结点时的 SAP HANA 设置的示意图。":::

## <a name="next-steps"></a>后续步骤

[备份 Azure VM 中的 SAP HANA 数据库](/azure/backup/backup-azure-sap-hana-database)。

