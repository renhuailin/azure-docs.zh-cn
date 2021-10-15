---
title: 使用 Azure 防火墙管理器配置 Azure DDoS 防护计划
description: 了解如何使用 Azure 防火墙管理器配置 Azure DDoS 防护计划标准版
author: vhorne
ms.author: victorh
ms.service: firewall-manager
ms.topic: how-to
ms.date: 09/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 4ec6c08fab14c2c08ed719d616b84f1a4d744eb9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367349"
---
# <a name="configure-an-azure-ddos-protection-plan-using-azure-firewall-manager-preview"></a>使用 Azure 防火墙管理器配置 Azure DDoS 防护计划（预览版）

Azure 防火墙管理器是用于大规模管理和保护网络资源的平台。 可以在 Azure 防火墙管理器中将虚拟网络与 DDoS 防护计划相关联。

> [!IMPORTANT]
> 使用 Azure 防火墙管理器配置 Azure DDoS 防护计划的功能目前以预览版提供。
> 有关 beta 版本、预览版或尚未正式发布的版本的 Azure 功能所适用的法律条款，请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 

> [!TIP]
> DDoS 防护标准版目前不支持虚拟 WAN。 但是，可以通过在关联了 DDoS 防护计划的虚拟网络中强制通过隧道向 Azure 防火墙发送 Internet 流量，来解决此限制。

在单一租户下，DDoS 防护计划可应用于跨多个订阅的虚拟网络。 有关 DDoS 防护计划的详细信息，请参阅 [Azure DDoS 防护标准版概述](../ddos-protection/ddos-protection-overview.md)。

若要了解此功能的工作原理，请创建一个防火墙策略，并创建一个使用 Azure 防火墙保护的虚拟网络。 然后，创建一个 DDoS 防护计划并将其与该虚拟网络相关联。

## <a name="create-a-firewall-policy"></a>创建防火墙策略

使用防火墙管理器创建防火墙策略。

1. 在 [Azure 门户](https://portal.azure.com)中打开防火墙管理器。
1. 选择“Azure 防火墙策略”。
1. 选择“创建 Azure 防火墙策略”。
1. 对于“资源组”，请选择“DDoS-Test-rg” 。
1. 在“策略详细信息”下的“名称”中，键入“fw-pol-01”  。
1. 对于“区域”，请选择“美国西部 2” 。
1. 选择“查看 + 创建”。
1. 选择“创建”。


## <a name="create-a-secured-virtual-network"></a>创建受保护的虚拟网络

使用防火墙管理器创建受保护的虚拟网络。

1. 开启防火墙管理器。
1. 选择“虚拟网络”。
1. 选择“创建新的受保护虚拟网络”。
1. 对于“资源组”，请选择“DDoS-Test-rg” 。
1. 对于“区域”，请选择“美国西部 2” 。
1. 对于“中心虚拟网络名称”，请键入“Hub-vnet-01” 。
1. 对于“地址范围”，请键入“10.0.0.0/16” 。
1. 选择“下一步: Azure 防火墙”。
1. 对于“公共 IP 地址”，请选择“新建”并键入“fw-pip”作为名称，然后选择“确定”   。
1. 对于“防火墙子网地址空间”，请键入“10.0.0.0/24” 。
1. 对“防火墙策略”选择“registry.pol-01” 。
1. **选择“下一步:** 查看 + 创建”。
1. 选择“创建”。

## <a name="create-a-ddos-protection-plan"></a>创建 DDoS 防护计划

使用防火墙管理器创建 DDoS 防护计划。 可以使用“DDoS 防护计划”页来创建和管理 Azure DDoS 防护计划。

:::image type="content" source="media/configure-ddos/firewall-ddos.png" alt-text="防火墙管理器“DDoS 防护计划”页的屏幕截图":::

1. 开启防火墙管理器。
1. 选择“DDoS 防护计划”。
1. 选择“创建”。
1. 对于“资源组”，选择“新建”。
1. 键入“DDos-Test-rg”作为资源组名称。
1. 在“实例详细信息”下的“名称”中，键入“DDoS-plan-01”  。
1. 对于“区域”，请选择“(美国)美国西部 2” 。
1. 选择“查看 + 创建”。
1. 选择“创建”。

## <a name="associate-a-ddos-protection-plan"></a>关联 DDoS 防护计划

现在，可以将 DDoS 防护计划与受保护的虚拟网络相关联。

1. 开启防火墙管理器。
1. 选择“虚拟网络”。
1. 选中“Hub-vnet-01”对应的复选框。
1. 选择“管理安全性”、“添加 DDoS 防护计划” 。
1. 对于“DDoS 防护标准版”，请选择“启用” 。
1. 对于“DDoS 防护计划”，请选择“DDoS-plan-01” 。
1. 选择“添加”。
1. 部署完成时，选择“刷新”。

现在应会看到虚拟网络具有一个关联的 DDoS 防护计划。

:::image type="content" source="media/configure-ddos/ddos-protection.png" alt-text="显示具有 DDoS 防护计划的虚拟网络的屏幕截图":::

## <a name="next-steps"></a>后续步骤

若要详细了解 DDoS 防护计划，请参阅：

- [Azure DDoS 保护标准概述](../ddos-protection/ddos-protection-overview.md)