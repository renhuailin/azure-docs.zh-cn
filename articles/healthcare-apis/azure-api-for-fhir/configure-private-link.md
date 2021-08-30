---
title: Azure API for FHIR 专用链接
description: 本文介绍如何为 Azure API for FHIR 服务设置专用终结点
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/27/2021
ms.author: zxue
ms.openlocfilehash: 6b025c35c5faa64a2333710693c992a410cafecc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778238"
---
# <a name="configure-private-link"></a>配置专用链接

借助专用链接，可通过专用终结点访问 Azure API for FHIR，这是一个网络接口，可使用虚拟网络中的专用 IP 地址进行专用安全连接。 借助专用链接，可从 VNet 安全地将服务作为第一方服务来访问，而无需通过公共域名系统 (DNS)。 本文介绍如何为 Azure API for FHIR 创建、测试和管理专用终结点。

>[!Note]
>启用专用链接后，就无法将专用链接或 Azure API for FHIR 从一个资源组或订阅移动到另一个资源组或订阅。 若要进行移动，请先删除专用链接，然后移动 Azure API for FHIR。 移动完成后，创建新的专用链接。 请在删除专用链接之前评估潜在的安全后果。
>
>如果为 Azure API for FHIR 启用了审核日志和指标的导出，请通过门户中的“诊断设置”更新导出设置。

## <a name="prerequisites"></a>先决条件

在创建专用终结点之前，需要先创建一些 Azure 资源：

- 资源组 - 将包含虚拟网络和专用终结点的 Azure 资源组。
- Azure API for FHIR - 要放在专用终结点后面的 FHIR 资源。
- 虚拟网络 - 客户端服务和专用终结点将连接到的 VNet。

有关详细信息，请参阅[专用链接文档](../../private-link/index.yml)。

## <a name="create-private-endpoint"></a>创建专用终结点

若要创建专用终结点，对 FHIR 资源具有基于角色的访问控制 (RBAC) 权限的开发人员可以使用 Azure 门户、[Azure PowerShell](../../private-link/create-private-endpoint-powershell.md) 或 [Azure CLI](../../private-link/create-private-endpoint-cli.md)。 本文将指导你完成使用 Azure 门户所对应的步骤。 建议使用 Azure 门户，因为它会自动创建和配置专用 DNS 区域。 有关详细信息，请参阅[专用链接快速入门指南](../../private-link/create-private-endpoint-portal.md)。

可通过两种方法创建专用终结点。 借助自动审批流，对 FHIR 资源具有 RBAC 权限的用户无需审批即可创建专用终结点。 借助手动审批流，无权访问 FHIR 资源的用户可请求 FHIR 资源所有者批准专用终结点。

> [!NOTE]
> 在为 Azure API for FHIR 创建经过审批的专用终结点后，会自动禁用指向它的公共流量。 

### <a name="auto-approval"></a>自动审批

确保新专用终结点的区域与虚拟网络的区域相同。 FHIR 资源的区域可能有所不同。

![Azure 门户的“基本信息”选项卡](media/private-link/private-link-portal2.png)

对于资源类型，请搜索并选择“Microsoft.HealthcareApis/services”。 对于资源，请选择 FHIR 资源。 对于目标子资源，请选择“FHIR”。

![Azure 门户的“资源”选项卡](media/private-link/private-link-portal1.png)

如果未设置现有的专用 DNS 区域，请选择“(New)privatelink.azurehealthcareapis.com”。 如果已配置专用 DNS 区域，可从列表中选择该区域。 格式必须为 privatelink.azurehealthcareapis.com。

![Azure 门户的“配置”选项卡](media/private-link/private-link-portal3.png)

部署完成后，可返回到“专用终结点连接”选项卡，其中显示连接状态为“已批准” 。

### <a name="manual-approval"></a>手动审批

对于手动批准，请选择“资源”下的第二个选项，即“按资源 ID 或别名连接到 Azure 资源”。 对于目标子资源，与在自动审批流程中一样，输入“fhir”。

![手动审批](media/private-link/private-link-manual.png)

部署完成后，可返回到“专用终结点连接”选项卡，可在该选项卡中批准、拒绝或删除连接。

![选项](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>测试专用终结点

若要确保 FHIR 服务器在禁用公用网络访问后不会接收公共流量，请从计算机中选择服务器的 /metadata 终结点。 应该会收到“403 禁止访问”消息。 


> [!NOTE]
> 在更新公用网络访问标志后，最多可能需要 5 分钟才会阻止公共流量。

若要确保专用终结点可将流量发送到服务器：

1. 创建连接到配置专用终结点的虚拟网络和子网的虚拟机 (VM)。 若要确保来自该 VM 的流量仅使用专用网络，请使用网络安全组 (NSG) 规则禁用出站 Internet 流量。
2. 通过 RDP 访问 VM。
3. 从 VM 访问 FHIR 服务器的 /metadata 终结点。 应会收到功能声明作为响应。

## <a name="manage-private-endpoint"></a>管理专用终结点

### <a name="view"></a>查看

在 Azure 门户中，可在创建专用终结点和关联的网络接口控制器 (NIC) 的资源组中查看这些内容。

![在资源中查看](media/private-link/private-link-view.png)

### <a name="delete"></a>删除

只能从 Azure 门户中删除专用终结点，方式是使用“概述”边栏选项卡，或者选择“网络专用终结点连接”选项卡下的“删除”选项。如果选择“删除”选项，将删除专用终结点及关联的 NIC   。 如果删除 FHIR 资源和公用网络的所有专用终结点，则会禁用访问，并且不会向 FHIR 服务器发送任何请求。

![删除专用终结点](media/private-link/private-link-delete.png)
