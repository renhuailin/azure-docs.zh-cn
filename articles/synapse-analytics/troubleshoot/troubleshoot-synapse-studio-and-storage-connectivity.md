---
title: 排查 Synapse Studio 与存储之间的连接问题
description: 排查 Synapse Studio 与存储之间的连接问题
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117055"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>排查 Azure Synapse Analytics Synapse Studio 与存储之间的连接问题

在 Synapse Studio 中，你可以浏览位于链接存储中的数据资源。 本指南将帮助你解决在尝试访问数据资源时遇到的连接问题。 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>案例 #1：存储帐户缺少正确的权限

如果你的存储帐户缺少适当的权限，你将无法通过 Synapse Studio 中的 "数据"--> "链接" 来展开存储结构。 请参阅下面的问题症状屏幕截图。 

详细的错误消息可能会有所不同，但错误消息的一般含义为： "此请求无权执行此操作。"。

在 "链接存储" 节点中：  
![存储连接问题1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

在 "存储容器" 节点中：  
![存储连接问题1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**解决方案**：若要将帐户分配到适当的角色，请参阅 [使用 Azure 门户分配用于访问 blob 和队列数据的 Azure 角色](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Case #2：未能将请求发送到存储服务器

选择箭头以展开 "数据" 中 "Synapse" > "链接" 的存储结构时，可能会在左侧面板中看到 "REQUEST_SEND_ERROR" 问题。 请参阅下面的问题症状屏幕截图：

在 "链接存储" 节点中：  
![存储连接问题2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

在 "存储容器" 节点中：  
![存储连接问题2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

此问题可能有几个可能的原因：

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>存储资源位于 vNet 之后，需要配置存储专用终结点

**解决方案**：在这种情况下，需要为存储帐户配置存储专用终结点。 有关如何为 vNet 配置存储专用终结点的详细说明，请参阅 [使用 Azure 门户分配 Azure 角色以访问 blob 和队列数据](../security/how-to-connect-to-workspace-from-restricted-network.md)。

\<storage-account-name\>配置存储专用终结点后，可以使用命令 "dfs.core.windows.net" 来检查连接性。 它应返回类似于 " \<storage-account-name\> privatelink.dfs.core.windows.net" 的字符串。

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>存储资源不在 vNet 后面，但由于防火墙配置，无法访问 Blob 服务 (Azure AD) 终结点

**解决方案**：在这种情况下，需要在 Azure 门户中打开存储帐户。 在左侧导航栏中向下滚动到 " **支持 + 故障排除** "，然后选择 " **连接检查** "，以检查 **Blob 服务 (Azure AD)** 连接状态。 如果无法访问，请按照升级后的指南检查存储帐户页下的 **防火墙和虚拟网络** 配置。 有关存储防火墙的详细信息，请参阅 [配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md)。

### <a name="other-issues-to-check"></a>要检查的其他问题 

* 要访问的存储资源是 Azure Data Lake Storage Gen2 的，位于防火墙和 vNet (，同时) 配置了存储专用终结点。
* 你正在访问的容器资源已删除或不存在。
* 跨租户：用户登录时所用的工作区租户与存储帐户的租户不相同。 


## <a name="next-steps"></a>后续步骤
如果前面的步骤不能帮助解决问题，请 [创建支持票证](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)。