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
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100384514"
---
# <a name="connect-to-workspace-resources-from-a-restricted-network"></a>从受限网络连接到工作区资源

假设你是 IT 管理员，负责管理组织的受限网络。 希望在 Azure Synapse Analytics Studio 和此受限网络内的工作站之间启用网络连接。 本文介绍如何进行此操作。

## <a name="prerequisites"></a>先决条件

* **Azure 订阅**：如果还没有 Azure 订阅，可以在开始前创建一个 [免费 Azure 帐户](https://azure.microsoft.com/free/)。
* **Azure Synapse analytics 工作区**：你可以从 Azure Synapse analytics 创建一个工作区。 你需要步骤4中的工作区名称。
* **受限网络**： IT 管理员为组织维护受限网络，并且有权配置网络策略。 步骤3中需要虚拟网络名称及其子网。


## <a name="step-1-add-network-outbound-security-rules-to-the-restricted-network"></a>步骤1：向受限网络添加网络出站安全规则

需要添加四个包含四个服务标记的网络出站安全规则。 
* AzureResourceManager
* AzureFrontDoor.Frontend
* AzureActiveDirectory
* AzureMonitor (此类型的规则是可选的。 仅当想要与 Microsoft 共享数据时才进行添加。 ) 

以下屏幕截图显示 Azure 资源管理器出站规则的详细信息。

![Azure 资源管理器服务标记详细信息的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/arm-servicetag.png)

创建其他三个规则时，请将 **目标服务标记** 的值替换为列表中的 **AzureFrontDoor**、 **AzureActiveDirectory** 或 **AzureMonitor** 。

有关详细信息，请参阅 [服务标记概述](../../virtual-network/service-tags-overview.md)。

## <a name="step-2-create-private-link-hubs"></a>步骤2：创建专用链接中心

接下来，从 Azure 门户创建专用链接中心。 若要在门户中找到此项，请搜索 *Azure Synapse Analytics (专用链接中心)*，然后填写所需信息以创建它。 

![Create Synapse 专用链接中心的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/private-links.png)

## <a name="step-3-create-a-private-endpoint-for-your-synapse-studio"></a>步骤3：创建 Synapse Studio 的专用终结点

若要访问 Azure Synapse Analytics Studio，必须从 Azure 门户创建专用终结点。 若要在门户中查找此项，请搜索 " *专用链接*"。 在 **专用链接中心** 中，选择 " **创建专用终结点**"，然后填写所需信息以创建它。 

> [!Note]
> 确保 **区域** 值与 Azure Synapse Analytics 工作区的值相同。

![创建专用终结点 "基本信息" 选项卡的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-1.png)

在 " **资源** " 选项卡上，选择你在步骤2中创建的专用链接中心。

![创建专用终结点，"资源" 选项卡的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-2.png)

在 " **配置** " 选项卡上： 
* 对于 " **虚拟网络**"，请选择受限制的虚拟网络名称。
* 对于 " **子网**"，请选择受限制的虚拟网络的子网。 
* 若要 **与专用 DNS 区域集成**，请选择 **"是"**。

![创建专用终结点，"配置" 选项卡的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-3.png)

创建专用链接端点后，可以访问 Azure Synapse Analytics Studio 的 web 工具的登录页面。 但是，你无法访问你的工作区中的资源。 为此，需要完成下一步。

## <a name="step-4-create-private-endpoints-for-your-workspace-resource"></a>步骤4：创建工作区资源的专用终结点

若要访问 Azure Synapse Analytics Studio 工作区资源中的资源，需要创建以下内容：

- 至少一个 **目标子资源** 类型为 **Dev** 的专用链接端点。
- 使用 **Sql** 或 **SqlOnDemand** 类型的两个其他可选专用链接终结点，具体取决于要访问的工作区中的资源。

创建这些方法类似于在上一步中创建终结点的方式。  

在 " **资源** " 选项卡上：

* 对于 " **资源类型**"，请选择 " **Synapse"/"工作区**"。
* 对于 " **资源**"，请选择之前创建的工作区名称。
* 对于 " **目标子资源**"，请选择终结点类型：
  * **Sql 用于** sql 池中的 sql 查询执行。
  * **SqlOnDemand** 用于 SQL 内置查询执行。
  * **Dev** 用于访问 Azure Synapse Analytics Studio 工作区中的其他所有内容。 需要至少创建一个此类型的私有链接端点。

![创建专用终结点、资源选项卡、工作区的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plinks-endpoint-ws-1.png)


## <a name="step-5-create-private-endpoints-for-workspace-linked-storage"></a>步骤5：为工作区链接存储创建专用终结点

若要使用 Azure Synapse Analytics Studio 工作区中的存储资源管理器访问链接的存储，则必须创建一个私有终结点。 此操作的步骤与步骤3中的步骤类似。 

在 " **资源** " 选项卡上：
* 对于 " **资源类型**"，请选择 " **Synapse/storageAccounts**"。
* 对于 " **资源**"，请选择之前创建的存储帐户名称。
* 对于 " **目标子资源**"，请选择终结点类型：
  * **blob** 适用于 Azure blob 存储。
  * **dfs** 用于 Azure Data Lake Storage Gen2。

![创建专用终结点、资源选项卡、存储的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/plink-endpoint-storage.png)

现在，你可以访问链接的存储资源。 在虚拟网络中的 Azure Synapse Analytics Studio 工作区中，可以使用存储资源管理器来访问链接的存储资源。

如以下屏幕截图所示，你可以为你的工作区启用托管的虚拟网络：

!["创建 Synapse" 工作区的屏幕截图，其中突出显示了 "启用托管虚拟网络" 选项。](./media/how-to-connect-to-workspace-from-restricted-network/ws-network-config.png)

如果希望笔记本在特定存储帐户下访问链接的存储资源，请在 Azure Synapse Analytics Studio 下添加托管专用终结点。 存储帐户名称应为笔记本需要访问的名称。 有关详细信息，请参阅 [创建托管专用终结点到你的数据源](./how-to-create-managed-private-endpoints.md)。

创建此终结点后，审批状态将显示 " **挂起**" 状态。 在 Azure 门户的此存储帐户的 " **专用终结点连接** " 选项卡中，请求批准此存储帐户的所有者。 批准后，笔记本可以访问此存储帐户下的链接存储资源。

现在，全部设置。 可以访问 Azure Synapse Analytics Studio 工作区资源。

## <a name="appendix-dns-registration-for-private-endpoint"></a>附录：专用终结点的 DNS 注册

如果在创建专用终结点过程中未启用 "与专用 DNS 区域集成"，则必须为每个专用终结点创建 "**专用 DNS 区域**"。
![Create Synapse 专用 DNS 区域1的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-1.png)

若要在门户中查找 **专用 DNS 区域** ，请搜索 *专用 DNS 区域*。 在 **专用 DNS 区域** 中，在下面填写所需信息以创建它。

* 对于 " **名称**"，请输入特定专用终结点专用 DNS 区域的专用名称，如下所示：
  * **`privatelink.azuresynapse.net`** 适用于访问 Azure Synapse Analytics Studio 网关的专用终结点。 请参阅在步骤3中创建这种类型的专用终结点。
  * **`privatelink.sql.azuresynapse.net`** 适用于 SQL 池中的这种类型的专用终结点和内置池中的 sql 查询执行。 请参阅在步骤4中创建的终结点。
  * **`privatelink.dev.azuresynapse.net`** 适用于这种类型的专用终结点，用于访问 Azure Synapse Analytics Studio 工作区中的其他所有内容。 请参阅在步骤4中创建这种类型的专用终结点。
  * **`privatelink.dfs.core.windows.net`** 适用于访问工作区链接 Azure Data Lake Storage Gen2 的专用终结点。 请参阅步骤5中的此类型的专用终结点创建。
  * **`privatelink.blob.core.windows.net`** 适用于访问工作区链接的 Azure Blob 存储的专用终结点。 请参阅步骤5中的此类型的专用终结点创建。

![Create Synapse 专用 DNS 区域2的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-2.png)

创建 **专用 DNS 区域** 后，输入创建的专用 DNS 区域，并选择 **虚拟网络链接** 以添加指向虚拟网络的链接。 

![Create Synapse 专用 DNS 区域3的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-3.png)

填写以下必填字段：
* 对于 " **链接名称**"，请输入链接名称。
* 对于 " **虚拟网络**"，请选择虚拟网络。

![Create Synapse 专用 DNS 区域4的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-4.png)

添加虚拟网络链接后，需要在之前创建的 **专用 DNS 区域** 中添加 DNS 记录集。

* 对于 " **名称**"，请输入不同专用终结点的专用名称字符串： 
  * **web** 适用于访问 Azure Synapse Analytics Studio 的专用终结点。
  * "***YourWorkSpaceName***" 适用于 sql 池中 sql 查询执行的专用终结点，还用于访问 Azure Synapse Analytics Studio 工作区中的其他所有项的专用终结点。
  * "***YourWorkSpaceName *-ondemand**" 用于内置池中的 sql 查询的专用终结点。
* 对于 " **类型**"，请选择 **"仅 DNS** 记录类型"。 
* 对于 " **IP 地址**"，请输入每个专用终结点对应的 IP 地址。 可以从专用终结点概述获取 **网络接口** 中的 IP 地址。

![Create Synapse 专用 DNS 区域5的屏幕截图。](./media/how-to-connect-to-workspace-from-restricted-network/pdns-zone-5.png)


## <a name="next-steps"></a>后续步骤

了解有关 [托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)的详细信息。

了解有关 [托管专用终结点](./synapse-workspace-managed-private-endpoints.md)的详细信息。
