---
title: 适用于 FHIR 的 Azure API 的专用链接
description: 本文介绍如何为 FHIR services 的 Azure API 设置专用终结点
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/03/2021
ms.author: zxue
ms.openlocfilehash: c73e11d8f89e50c77b5a140a5f77c749f8a092b6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017925"
---
# <a name="configure-private-link"></a>配置专用链接

专用链接使你可以通过专用终结点访问 Azure API for FHIR，这是一个网络接口，该接口通过你的虚拟网络从专用 IP 地址进行私下连接和安全连接。 借助专用链接，你可以从 Vnet 安全地将服务作为第一方服务来访问，而无需经过公共 DNS。 本文逐步讲解如何创建、测试和管理用于 FHIR 的 Azure API 的专用终结点。

>[!Note]
>启用 "专用" 链接后，不能将专用链接或 Azure API for FHIR 从一个资源组移动到另一个资源组或订阅。 若要移动，请先删除专用链接，然后移动 Azure API for FHIR，并在移动完成后创建新的专用链接。 删除私有链接之前，请评估潜在的安全措施。
>
>如果为 FHIR 的 Azure API 启用了导出审核日志和/度量值，请通过门户中的诊断设置更新导出设置。

## <a name="prerequisites"></a>先决条件

创建专用终结点之前，需要先创建一些 Azure 资源：

- 资源组–将包含虚拟网络和专用终结点的 Azure 资源组。
- 用于 FHIR 的 Azure API –要置于专用终结点后面的 FHIR 资源。
- 虚拟网络-客户端服务和专用终结点将连接到的 VNet。

有关详细信息，请查看 [专用链接文档](../../private-link/index.yml)。

## <a name="disable-public-network-access"></a>禁用公用网络访问

为 FHIR 资源创建专用终结点不会自动禁用向其发送的公共流量。 为此，必须更新 FHIR 资源，以便将新的 "公共访问" 属性从 "Enabled" 设置为 "Disabled"。 禁用公共网络访问时要小心，因为所有不是来自正确配置的专用终结点的 FHIR 服务请求都将被拒绝。 仅允许来自专用终结点的流量。

![禁用公共网络访问](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>创建专用终结点

若要创建专用终结点，具有 FHIR 资源的 RBAC 权限的开发人员可以使用 Azure 门户、 [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)或 [Azure CLI](../../private-link/create-private-endpoint-cli.md)。 本文将指导你完成使用 Azure 门户的步骤。 建议使用 Azure 门户，因为它会自动创建和配置专用 DNS 区域。 有关更多详细信息，可以参考 [专用链接快速入门指南](../../private-link/create-private-endpoint-portal.md) 。

可以通过两种方法创建专用终结点。 自动审批流允许对 FHIR 资源具有 RBAC 权限的用户创建专用终结点，而无需批准。 手动审批流允许无 FHIR 资源权限的用户请求私有终结点，由 FHIR 资源的所有者批准。

### <a name="auto-approval"></a>自动批准

请确保新的专用终结点的区域与虚拟网络的区域相同。 FHIR 资源的区域可能不同。

![Azure 门户基本信息选项卡](media/private-link/private-link-portal2.png)

对于 "资源类型"，请搜索并选择 "HealthcareApis/服务"。 对于 "资源"，请选择 "FHIR" 资源。 对于目标子资源，请选择 "fhir"。

![Azure 门户资源 "选项卡](media/private-link/private-link-portal1.png)

如果尚未设置现有专用 DNS 区域，请选择 " (New) privatelink.azurehealthcareapis.com"。 如果已配置专用 DNS 区域，可以从列表中选择它。 它的格式必须为 "privatelink.azurehealthcareapis.com"。

![Azure 门户配置选项卡](media/private-link/private-link-portal3.png)

部署完成后，您可以返回到 "专用终结点连接" 选项卡，您将在该选项卡上看到 "已批准" 作为连接状态。

### <a name="manual-approval"></a>手动批准

对于 "手动批准"，请选择 "资源" 下的第二个选项 "按资源 ID 或别名连接到 Azure 资源"。 对于目标子资源，输入 "fhir" 作为 "自动批准"。

![手动批准](media/private-link/private-link-manual.png)

部署完成后，你可以返回到 "专用终结点连接" 选项卡，你可以在该选项卡上批准、拒绝或删除你的连接。

![选项](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>测试专用终结点

若要确保在禁用公共网络访问后 FHIR 服务器无法接收公共流量，请尝试从计算机中命中服务器的/metadata 终结点。 你应收到403禁止访问。 请注意，在更新公共网络访问标志后，可能需要长达5分钟的时间才会阻止公共通信。

确保专用终结点可以将流量发送到服务器：

1. 创建一个 VM，该 VM 连接到你的专用终结点在其上配置的虚拟网络和子网。 若要确保来自 VM 的流量仅用于专用网络，可以通过 NSG 规则禁用出站 internet 流量。
2. 通过 RDP 连接到 VM。
3. 尝试从 VM 命中 FHIR 服务器的/metadata 终结点，你应收到作为响应的功能声明。

## <a name="manage-private-endpoint"></a>管理专用终结点

### <a name="view"></a>查看

专用终结点和关联的 NIC 在中创建它们的资源组 Azure 门户可见。

![在资源中查看](media/private-link/private-link-view.png)

### <a name="delete"></a>删除

只能通过 "概述" 边栏选项卡 (Azure 门户删除专用终结点，如下所示) 或通过 "网络 (预览) 的" 专用终结点连接 "选项卡下的" 删除 "选项。单击 "删除" 按钮将删除专用终结点和关联的 NIC。 如果删除了 FHIR 资源的所有专用终结点，并且已禁用公共网络访问，则不会请求将其提供给 FHIR 服务器。

![删除专用终结点](media/private-link/private-link-delete.png)
