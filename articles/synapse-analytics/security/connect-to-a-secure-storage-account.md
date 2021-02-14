---
title: 从 Azure Synapse 工作区连接到安全存储帐户
description: 本文介绍如何从 Azure Synapse 工作区连接到安全存储帐户
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 599cf17e1ab2b85aac77893e8b2d520d412e1cea
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417127"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>从 Synapse 工作区连接到安全的 Azure 存储帐户

本文介绍如何从 Azure Synapse 工作区连接到安全的 Azure 存储帐户。 创建工作区时，可以将 Azure 存储帐户链接到 Synapse 工作区。 创建工作区之后，可以链接更多的存储帐户。


## <a name="secured-azure-storage-accounts"></a>受保护的 Azure 存储帐户
Azure 存储提供分层的安全模型，使你能够保护和控制对存储帐户的访问。 你可以配置 IP 防火墙规则，以将流量从所选的公共 IP 地址范围授予对存储帐户的访问权限。 你还可以配置网络规则，以便从所选虚拟网络向存储帐户授予流量。 可以合并允许从所选 IP 地址范围访问的 IP 防火墙规则，以及允许从同一存储帐户上的所选虚拟网络访问的网络规则。 这些规则适用于存储帐户的公共终结点。 不需要任何访问规则即可允许从工作区中创建的托管专用终结点到存储帐户的流量。 存储防火墙规则可以应用于现有存储帐户，也可以在创建新存储帐户时应用到新存储帐户。 可在 [此处](https://docs.microsoft.com/azure/storage/common/storage-network-security)详细了解如何保护存储帐户。

## <a name="synapse-workspaces-and-virtual-networks"></a>Synapse 工作区和虚拟网络
当你创建 Synapse 工作区时，你可以选择启用要与之关联的托管虚拟网络。 如果你在创建工作区时未为其启用托管虚拟网络，则你的工作区位于共享虚拟网络中，同时还与其他未关联托管虚拟网络的 Synapse 工作区结合使用。 如果在创建工作区时启用了托管虚拟网络，则工作区与 Azure Synapse 管理的专用虚拟网络相关联。 不会在你的客户订阅中创建这些虚拟网络。 因此，你将无法使用上述网络规则将来自这些虚拟网络的流量授予对受保护的存储帐户的访问权限。  

## <a name="access-a-secured-storage-account"></a>访问受保护的存储帐户
Synapse 操作来自网络规则中不能包含的网络。 若要启用从工作区到安全存储帐户的访问，需要执行以下操作。

* 创建一个 Azure Synapse 工作区，其中包含与其关联的托管虚拟网络，并从该工作区创建托管专用终结点到安全存储帐户
* 允许 Azure Synapse 工作区访问安全存储帐户作为受信任的 Azure 服务。 作为受信任的服务，Azure Synapse 将使用强身份验证安全地连接到你的存储帐户。   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>使用托管虚拟网络创建 Synapse 工作区，并创建托管专用终结点到你的存储帐户
可以按照 [以下步骤](./synapse-workspace-managed-vnet.md) 创建 Synapse 工作区，该工作区具有与之关联的托管虚拟网络。 创建关联的托管虚拟网络的工作区后，可以按照 [此处](./how-to-create-managed-private-endpoints.md)列出的步骤，在安全存储帐户中创建托管专用终结点。 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>允许 Azure Synapse 工作区访问安全存储帐户作为受信任的 Azure 服务
分析功能（如专用 SQL 池和无服务器 SQL 池）使用未部署到托管虚拟网络中的多租户基础结构。 为了使来自这些功能的流量能够访问受保护的存储帐户，你必须按照以下步骤配置对你的存储帐户的访问，方法是基于工作区的系统分配的托管标识。

在 Azure 门户中，导航到受保护的存储帐户。 从左侧导航窗格中选择 " **网络** "。 在 " **资源实例** " 部分中，选择 " *Synapse"/"工作区* " 作为 **资源类型** ，并输入 " **实例名称**" 的工作区名称。 选择“保存”。

![存储帐户网络配置。](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

现在应能够从工作区访问安全存储帐户。


## <a name="next-steps"></a>后续步骤

了解有关 [托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)的详细信息。

了解有关 [托管专用终结点](./synapse-workspace-managed-private-endpoints.md)的详细信息。
