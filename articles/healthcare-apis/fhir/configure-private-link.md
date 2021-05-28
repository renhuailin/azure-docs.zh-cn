---
title: 适用于 FHIR 的 Azure API 的专用链接
description: 本文介绍如何为 FHIR services 的 Azure API 设置专用终结点
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/27/2021
ms.author: zxue
ms.openlocfilehash: 6b025c35c5faa64a2333710693c992a410cafecc
ms.sourcegitcommit: 1b698fb8ceb46e75c2ef9ef8fece697852c0356c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2021
ms.locfileid: "110652716"
---
# <a name="configure-private-link"></a>配置专用链接

通过专用链接，你可以通过专用终结点访问 Azure API for FHIR，该终结点是一个网络接口，该接口使用虚拟网络中的专用 IP 地址与你私下建立安全连接。 借助专用链接，你可以将服务作为第一方服务安全地从 VNet 访问，而无需通过公共域名系统 (DNS) 。 本文介绍如何创建、测试和管理适用于 FHIR 的 Azure API 的专用终结点。

>[!Note]
>启用 "专用" 链接后，不能将专用链接或 Azure API for FHIR 从一个资源组移动到另一个资源组或订阅。 若要进行移动，请先删除私有链接，然后移动 Azure API for FHIR。 移动完成后，创建新的专用链接。 删除私有链接之前，请评估潜在的安全措施。
>
>如果为 FHIR 的 Azure API 启用导出审核日志和度量值，请通过门户中的 **诊断设置** 更新导出设置。

## <a name="prerequisites"></a>先决条件

创建专用终结点之前，需要先创建一些 Azure 资源：

- 资源组–将包含虚拟网络和专用终结点的 Azure 资源组。
- 用于 FHIR 的 Azure API –要置于专用终结点后面的 FHIR 资源。
- 虚拟网络-客户端服务和专用终结点将连接到的 VNet。

有关详细信息，请参阅 [私有链接文档](../../private-link/index.yml)。

## <a name="create-private-endpoint"></a>创建专用终结点

若要创建专用终结点，具有基于角色的访问控制的开发人员 (RBAC) 对 FHIR 资源的权限可以使用 Azure 门户、 [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)或 [Azure CLI](../../private-link/create-private-endpoint-cli.md)。 本文将指导你完成使用 Azure 门户的步骤。 建议使用 Azure 门户，因为它会自动创建和配置专用 DNS 区域。 有关详细信息，请参阅 [Private Link 快速入门指南](../../private-link/create-private-endpoint-portal.md)。

有两种方法可以创建专用终结点。 自动审批流允许对 FHIR 资源具有 RBAC 权限的用户创建专用终结点，而无需审批。 手动审批流允许无权访问 FHIR 资源的用户请求 FHIR 资源所有者批准专用终结点。

> [!NOTE]
> 为应用程序创建已批准的专用终结点Azure API for FHIR，会自动禁用发到该终结点的公共流量。 

### <a name="auto-approval"></a>自动批准

确保新专用终结点的区域与虚拟网络的区域相同。 FHIR 资源的区域可能不同。

![Azure 门户"基本信息"选项卡](media/private-link/private-link-portal2.png)

对于资源类型，搜索并选择 **"Microsoft.HealthcareApis/services"。** 对于资源，请选择 FHIR 资源。 对于目标子资源，请选择 **"FHIR"。**

![Azure 门户"资源"选项卡](media/private-link/private-link-portal1.png)

如果尚未设置现有区域专用 DNS，请选择 **" (") privatelink.azurehealthcareapis.com"。** 如果已配置专用 DNS区域，可以从列表中选择它。 它必须采用 privatelink.azurehealthcareapis.com **格式。**

![Azure 门户配置选项卡](media/private-link/private-link-portal3.png)

部署完成后，可以返回到"专用终结点连接"选项卡，其中 **你会注意到"** 已批准"作为连接状态。

### <a name="manual-approval"></a>手动批准

对于手动批准，请选择"资源"下的第二个选项"按资源 ID 或别名连接到 Azure 资源"。 对于"目标子资源"，输入"fhir"，如"自动批准"一样。

![手动批准](media/private-link/private-link-manual.png)

部署完成后，你可以返回到 "专用终结点连接" 选项卡，你可以在该选项卡上批准、拒绝或删除你的连接。

![选项](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>测试专用终结点

若要确保在禁用公共网络访问后 FHIR 服务器无法接收公共流量，请从计算机选择服务器的/metadata 终结点。 你应收到403禁止访问。 


> [!NOTE]
> 更新公共网络访问标志后，可能需要长达5分钟的时间才会阻止公共通信。

确保专用终结点可以将流量发送到服务器：

1. 创建 (VM) 的虚拟机，该虚拟机连接到你的专用终结点在其上配置的虚拟网络和子网。 若要确保来自 VM 的流量仅使用专用网络，请使用网络安全组 (NSG) 规则禁用出站 internet 流量。
2. 通过 RDP 连接到 VM。
3. 从 VM 访问 FHIR 服务器的/metadata 终结点。 你应收到作为响应的功能声明。

## <a name="manage-private-endpoint"></a>管理专用终结点

### <a name="view"></a>查看

专用终结点和关联的网络接口控制器 (NIC) 在 Azure 门户中可在创建它们的资源组中看到。

![在资源中查看](media/private-link/private-link-view.png)

### <a name="delete"></a>删除

只能从 "**概述**" 边栏选项卡或通过选择 "**网络专用终结点连接**" 选项卡下的 "**删除**" 选项 Azure 门户删除专用终结点。选择 "**删除**" 将删除专用终结点和关联的 NIC。 如果删除了 FHIR 资源和公用网络的所有专用终结点，则会禁用访问权限，并且不会请求将其提供给 FHIR 服务器。

![删除专用终结点](media/private-link/private-link-delete.png)
