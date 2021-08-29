---
title: 托管专用终结点
description: 本文介绍 Azure Synapse Analytics 中的托管专用终结点
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 01/12/2020
ms.author: seshin
ms.reviewer: jrasnick
ms.openlocfilehash: 0be33ec2e9815585bfbd810be00cb12a001bd26f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729712"
---
# <a name="synapse-managed-private-endpoints"></a>Synapse 托管专用终结点

本文将介绍 Azure Synapse Analytics 中的托管专用终结点。

## <a name="managed-private-endpoints"></a>托管专用终结点

托管专用终结点是在与 Azure Synapse 工作区关联的托管虚拟网络中创建的专用终结点。 托管专用终结点建立与 Azure 资源的专用链接。 Azure Synapse 代表你管理这些专用终结点。 你可以从 Azure Synapse 工作区创建专用终结点来访问 Azure 服务（例如 Azure 存储和 Azure Cosmos DB）和 Azure 托管的客户/合作伙伴服务。

使用托管专用终结点时，Azure Synapse 工作区和其他 Azure 资源之间的流量完全通过 Microsoft 主干网络进行遍历。 托管专用终结点防止数据外泄。 托管专用终结点使用托管虚拟网络中的专用 IP 地址，从而有效地将 Azure Synapse 工作区正在通信的 Azure 服务引入虚拟网络中。 托管专用终结点映射到 Azure 中的特定资源，而不是整个服务。 客户可以将连接限制到其组织批准的特定资源。 

详细了解[专用链接和专用终结点](../../private-link/index.yml)。

>[!IMPORTANT]
>托管专用终结点仅在具有托管工作区虚拟网络的 Azure Synapse 工作区中受支持。

>[!NOTE]
>创建 Azure Synapse 工作区时，可以选择将托管虚拟网络与其关联。 如果选择将托管虚拟网络与工作区关联，则还可以选择将工作区中的出站流量设置为仅限批准的目标。 必须为这些目标创建托管专用终结点。 


在 Azure Synapse 中创建托管专用终结点时，创建的专用终结点连接将处于“挂起”状态。 系统将启动审批工作流。 专用链接资源所有者负责批准或拒绝该连接。 如果所有者批准该连接，则会建立专用链接。 但是，如果所有者不批准该连接，则不会建立专用链接。 在任一情况下，都会以连接状态更新托管专用终结点。 只能使用处于已批准状态的托管专用终结点将流量发送到链接到托管专用终结点的专用链接资源。

## <a name="managed-private-endpoints-for-dedicated-sql-pool-and-serverless-sql-pool"></a>专用 SQL 池和无服务器 SQL 池的托管专用终结点

专用 SQL 池和无服务器 SQL 池是 Azure Synapse 工作区中的分析功能。 这些功能使用未部署到[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)中的多租户基础结构。

创建工作区时，Azure Synapse 会在该工作区中创建两个托管专用终结点，分别用于专用 SQL 池和无服务器 SQL 池。 

这两个托管专用终结点会在 Synapse Studio 中列出。 在 Studio 的左侧导航栏中选择“管理”，然后选择“托管专用终结点”即可查看它们 。

面向 SQL 池的托管专用终结点名为 synapse-ws-sql--\<workspacename\>，面向无服务器 SQL 池的托管专用终结点名为 ynapse-ws-sqlOnDemand--\<workspacename\> 。

![专用 SQL 池和无服务器 SQL 池的托管专用终结点](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

创建 Azure Synapse 工作区时，会自动为你创建这两个托管专用终结点。 对于这两个托管专用终结点，你无需付费。

## <a name="next-steps"></a>后续步骤

若要了解详细信息，请继续学习[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)一文。