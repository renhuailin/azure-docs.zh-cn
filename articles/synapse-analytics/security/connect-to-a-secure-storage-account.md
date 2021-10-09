---
title: 从 Azure Synapse 工作区连接到安全存储帐户
description: 本文介绍如何从 Azure Synapse 工作区连接到安全存储帐户
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 103aaa913511c8d61e8e2a28ede81973fca98d1a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774628"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>从 Synapse 工作区连接到安全 Azure 存储帐户

本文介绍如何从 Azure Synapse 工作区连接到安全的 Azure 存储帐户。 创建工作区时，可以将 Azure 存储帐户链接到 Synapse 工作区。 创建工作区之后，可以链接更多存储帐户。


## <a name="secured-azure-storage-accounts"></a>受保护的 Azure 存储帐户
Azure 存储提供分层的安全模型，使你能够保护和控制对存储帐户的访问。 可以配置 IP 防火墙规则，向来自所选公共 IP 地址范围的流量授予对存储帐户的访问权限。 还可配置网络规则，向来自所选所选虚拟网络的流量授予对存储帐户的访问权限。 在同一存储帐户上可以合并允许来自所选 IP 地址范围的访问的 IP 防火墙规则，以及允许来自所选虚拟网络的访问的网络规则。 这些规则应用到存储帐户的公共终结点。 不需要任何访问规则即可允许工作区中所创建的托管专用终结点的流量访问存储帐户。 可对现有的存储帐户应用存储防火墙规则，或者在创建新存储帐户时应用这些规则。 可在[此处](../../storage/common/storage-network-security.md)详细了解如何保护存储帐户。

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse 工作区和虚拟网络
创建 Synapse 工作区时，可以选择启用要与之关联的托管虚拟网络。 如果创建工作区时未为其启用托管虚拟网络，则工作区与其他没有托管虚拟网络与之关联的 Synapse 工作区一起位于共享的虚拟网络中。 如果创建工作区时启用了托管虚拟网络，则工作区与 Azure Synapse 管理的专用虚拟网络相关联。 不会在你的客户订阅中创建这些虚拟网络。 因此，将无法使用上述网络规则向来自这些虚拟网络的流量授予对受保护存储帐户的访问权限。  

## <a name="access-a-secured-storage-account"></a>访问受保护的存储帐户
Synapse 从不能包含在网络规则中的网络进行操作。 若要实现从工作区访问安全存储帐户，需要执行以下操作。

* 创建与托管虚拟网络关联了的 Azure Synapse 工作区，并从该工作区创建托管专用终结点到安全存储帐户。 

    如果使用 Azure 门户来创建工作区，可以在“网络”选项卡下启用托管虚拟网络，如下所示。 如果启用托管的虚拟网络，或者，如果 Synapse 确定主存储帐户是安全存储帐户，则可以选择创建针对该安全存储帐户的托管专用终结点连接请求，如下所示。 存储帐户所有者将需要批准该连接请求，以便建立专用链接。 或者，如果在工作区中创建 Apache Spark 池的用户具有批准连接请求的足够权限，Synapse 将会批准此连接请求。
![启用托管 VNet 和托管专用终结点](./media/connect-to-a-secure-storage-account/enable-managed-virtual-network-managed-private-endpoint.png) 
    


* 允许 Azure Synapse 工作区作为受信任的 Azure 服务访问安全存储帐户。 然后 Azure Synapse 作为受信任服务会使用强身份验证安全地连接到存储帐户。   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>创建具有托管虚拟网络的 Synapse 工作区，并创建托管专用终结点到存储帐户
可以按照[这些步骤](./synapse-workspace-managed-vnet.md)创建关联了托管虚拟网络的 Synapse 工作区。 创建关联了托管虚拟网络的工作区后，可以按照[此处](./how-to-create-managed-private-endpoints.md)列出的步骤创建托管专用终结点到安全存储帐户。 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>允许 Azure Synapse 工作区作为受信任的 Azure 服务访问安全存储帐户
分析功能（如专用 SQL 池和无服务器 SQL 池）使用未部署到托管虚拟网络中的多租户基础结构。 为了使来自这些功能的流量访问受保护的存储帐户，必须按照以下步骤基于工作区系统分配的托管标识配置对存储帐户的访问。

在 Azure 门户中导航到受保护的存储帐户。 从左侧导航窗格选择“网络”。  在“资源实例”部分中，选择 Microsoft.Synapse/workspaces 作为“资源类型”，并输入工作区名称作为“实例名称”。 选择“保存”。

![存储帐户网络配置。](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

现在应能够从工作区访问受保护的存储帐户。


## <a name="next-steps"></a>后续步骤

详细了解[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)。

详细了解[托管专用终结点](./synapse-workspace-managed-private-endpoints.md)。