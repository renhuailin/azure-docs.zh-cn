---
title: 从受限网络连接到 Azure Synapse Analytics Studio 中的工作区资源
description: 本文介绍如何从受限网络连接到工作区资源
author: xujxu
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 10/25/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: de7c5dba5a4868b7a8fdb390f974134cfaef7395
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384514"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>从受限网络连接到工作区资源

假设你是 IT 管理员，负责管理组织的受限网络。 你希望在 Azure Synapse Analytics Studio 和这个受限网络内的工作站之间建立网络连接。 本文介绍如何进行此操作。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure Synapse Analytics 工作区**：你可以通过Azure Synapse Analytics 创建一个工作区。 步骤 4 中需要此工作区名称。
* **受限网络**：IT 管理员为组织维护受限网络，并且有权配置网络策略。 步骤 3 中需要虚拟网络名称及其子网。


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>步骤 1：向受限网络添加网络出站安全规则

你需要使用四个服务标记添加四个网络出站安全规则。 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor（此类型的规则是可选的。 仅当要与 Microsoft 共享数据时才添加此规则。)

以下屏幕截图显示 Azure 资源管理器出站规则的详细信息。

![Azure 资源管理器服务标记详细信息的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

创建其他三个规则时，请将“目标服务标记”的值替换为列表中的 AzureFrontDoor.Frontend、AzureActiveDirectory 或 AzureMonitor   。

有关详细信息，请参阅[服务标记概述](../../virtual-network/service-tags-overview.md)。

## <a name="step-2-create-private-link-hubs"></a>步骤 2：创建专用链接中心

接下来，通过 Azure 门户创建专用链接中心。 若要在门户中查找此项，请搜索“Azure Synapse Analytics（专用链接中心）”，然后填写所需信息进行创建。 

![“创建 Synapse 专用链接中心”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>步骤 3：为 Synapse Studio 创建专用终结点

若要访问 Azure Synapse Analytics Studio，必须从 Azure 门户创建专用终结点。 若要在门户中查找此项，请搜索“专用链接”。 在“专用链接中心”中，选择“创建专用终结点”，然后填写所需信息进行创建 。 

> [!Note]
> 确保“区域”值与 Azure Synapse Analytics 工作区所在区域的值相同。

![“创建专用终结点”中“基本信息”选项卡的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

在“资源”选项卡上，选择你在步骤 2 中创建的专用链接中心。

![“创建专用终结点”中“资源”选项卡的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

在“配置”选项卡上： 
* 对于“虚拟网络”，请选择受限虚拟网络名称。
* 对于“子网”，请选择受限虚拟网络的子网。 
* 对于“与专用 DNS 区域集成”，请选择“是” 。

![创建专用终结点中“配置”选项卡的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

创建专用链接终结点后，可以访问 Azure Synapse Analytics Studio 的 Web 工具登录页面。 但是，你还无法访问工作区中的资源。 为此，需要完成下一步。

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>步骤 4：为工作区资源创建专用终结点

若要访问 Azure Synapse Analytics Studio 工作区资源中的资源，需要创建以下内容：

- 至少一个“目标子资源类型”为“Dev”的专用链接终结点 。
- 两个类型为 Sql 或 SqlOnDemand 的其他可选专用链接终结点，具体取决于要访问工作区中的哪些资源 。

创建这些终结点的方法与在上一步中创建终结点的方法类似。  

在“资源”选项卡上：

* 对于“资源类型”，请选择“Microsoft.Synapse/workspaces” 。
* 对于“资源”，请选择之前创建的工作区名称。
* 对于“目标子资源”，请选择终结点类型：
  * Sql，用于在 SQL 池中执行 SQL 查询。
  * SqlOnDemand，用于执行 SQL 内置查询。
  * Dev，用于访问 Azure Synapse Analytics Studio 工作区中的其他所有内容。 需要至少创建一个此类型的专用链接终结点。

![“创建专用终结点”-“资源”选项卡-“工作区”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>步骤 5：为与工作区关联的存储创建专用终结点

若要使用 Azure Synapse Analytics Studio 工作区中的存储资源管理器访问关联的存储，需要创建一个专用终结点。 此操作的步骤与步骤 3 中的步骤类似。 

在“资源”选项卡上：
* 对于“资源类型”，请选择“Microsoft.Synapse/storageAccounts” 。
* 对于“资源”，请选择之前创建的存储帐户名称。
* 对于“目标子资源”，请选择终结点类型：
  * blob，用于 Azure Blob 存储。
  * dfs，用于 Azure Data Lake Storage Gen2。

![创建专用终结点 -“资源”选项卡 -“存储”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

现在，你便可以访问关联的存储资源。 在虚拟网络中的 Azure Synapse Analytics Studio 工作区中，可以使用存储资源管理器来访问关联的存储资源。

你可以为工作区启用托管虚拟网络，如以下屏幕截图所示：

![“创建 Synapse 工作区”的屏幕截图，其中突出显示了“启用托管虚拟网络”选项。](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

如果希望笔记本在特定存储帐户下访问关联的存储资源，请在 Azure Synapse Analytics Studio 下添加托管专用终结点。 存储帐户名称应为笔记本需要访问的帐户的名称。 有关详细信息，请参阅[创建数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)。

创建此终结点后，审批状态将显示“挂起”状态。 在 Azure 门户的这个存储帐户的“专用终结点连接”选项卡中，请求该存储帐户的所有者批准。 批准后，笔记本就可以访问此存储帐户下关联的存储资源。

现已完成全部设置。 接下来，便可以访问 Azure Synapse Analytics Studio 工作区资源了。

## <a name="appendix-dns-registration-for-private-endpoint"></a>附录：专用终结点的 DNS 注册

如果在专用终结点创建过程中没有启用“与专用 DNS 区域集成”，（如以下屏幕截图所示），则必须为每个专用终结点创建“专用 DNS 区域”。
![“创建 Synapse 专用 DNS 区域 1”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

若要在门户中查找“专用 DNS 区域”，请搜索“专用 DNS 区域”。 在“专用 DNS 区域”中，填写以下所需信息以创建它。

* 对于“名称”，请输入特定专用终结点的专用 DNS 区域专用名称，如下所示：
  * **`privatelink.azuresynapse.net`** 是针对用于访问 Azure Synapse Analytics Studio 网关的专用终结点。 请参阅步骤 3，了解如何创建这类专用终结点。
  * **`privatelink.sql.azuresynapse.net`** 是针对用于在 SQL 池和内置池中执行这类 sql 查询的专用终结点。 请参阅步骤 4，了解如何创建这类终结点。
  * **`privatelink.dev.azuresynapse.net`** 是针对用于访问 Azure Synapse Analytics Studio 工作区中其他所有内容的这类专用终结点。 请参阅步骤 4，了解如何创建这类专用终结点。
  * **`privatelink.dfs.core.windows.net`** 是针对用于访问与工作区关联的 Azure Data Lake Storage Gen2 的专用终结点。 请参阅步骤 5，了解如何创建这类专用终结点。
  * **`privatelink.blob.core.windows.net`** 是针对用于访问与工作区关联的 Azure Blob 存储的专用终结点。 请参阅步骤 5，了解如何创建这类专用终结点。

![“创建 Synapse 专用 DNS 区域 2”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

创建“专用 DNS 区域”后，输入创建的专用 DNS 区域，并选择“虚拟网络链接”以添加虚拟网络的链接 。 

![“创建 Synapse 专用 DNS 区域 3”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

按如下所示填写必填字段：
* 对于“链接名称”，请输入链接名称。
* 对于“虚拟网络”，请选择虚拟网络。

![“创建 Synapse 专用 DNS 区域 4”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

添加虚拟网络链接后，需要在之前创建的“专用 DNS 区域”中添加 DNS 记录集。

* 对于“名称”，请输入不同专用终结点的专用名称字符串： 
  * **web** 是针对用于访问 Azure Synapse Analytics Studio 的专用终结点。
  * “***YourWorkSpaceName***“是针对用于在 SQL 池中执行 sql 查询的专用终结点，也针对用于访问 Azure Synapse Analytics Studio 工作区中其他所有内容的专用终结点。
  * “**YourWorkSpaceName-ondemand**”是针对用于在内置池中执行 sql 查询的专用终结点。
* 对于“类型”，只能选择 DNS 记录类型“A” 。 
* 对于“IP 地址”，请输入每个专用终结点对应的 IP 地址。 你可以通过专用终结点概述获取“网络接口”中的 IP 地址。

![“创建 Synapse 专用 DNS 区域 5”的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>后续步骤

详细了解[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)。

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)。
