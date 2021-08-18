---
title: Azure 应用程序解决方案模板产品/服务发布指南 - Azure 市场
description: 本文介绍了在 Azure 市场中发布解决方案模板的要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: msjogarrig
ms.author: jogarrig
ms.date: 07/01/2021
ms.openlocfilehash: 5090b5e4c34e70146182fbd472b7e24f9f919c15
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113231280"
---
# <a name="publishing-guide-for-azure-applications-solution-template-offers"></a>Azure 应用程序解决方案模板产品/服务发布指南

本文介绍了发布解决方案模板产品/服务的要求，这是在 Azure 市场中发布 Azure 应用程序产品/服务的一种方法。 解决方案模板产品/服务类型要求使用 [Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)来自动部署解决方案基础结构。

在以下情况下，使用 Azure 应用程序解决方案模板产品/服务类型：

- 你的解决方案需要单个虚拟机 (VM) 之外的其他部署和配置自动化，例如 VM、网络和存储资源的组合。
- 你的客户将自行管理解决方案。

针对此产品/服务类型，客户看到的列表选项为“立即获取”。

## <a name="requirements-for-solution-template-offers"></a>解决方案模板产品/服务的要求

| **要求** | **详细信息**  |
| ---------------  | -----------  |
|计费和计量    |  解决方案模板产品/服务在商业市场中不是交易产品/服务，但可用于部署通过商业市场计费的付费 VM 产品/服务。 解决方案的 ARM 模板部署的资源是在客户的 Azure 订阅中设置的。 即用即付虚拟机将通过 Microsoft 与客户进行交易并且通过客户的 Azure 订阅进行计费。<br/> 对于自带许可 (BYOL) 的计费，尽管 Microsoft 会对客户订阅产生的任何基础结构成本计费，但由你直接与客户处理你的软件许可证费用。   |
|与 Azure 兼容的虚拟硬盘 (VHD)  |   必须基于 Windows 或 Linux 构建 VM。 有关详细信息，请参阅： <ul> <li>[创建 Azure 应用程序产品/服务](azure-app-offer-setup.md)（针对 Windows VHD）。</li><li>[Azure 支持的 Linux 发行版](../virtual-machines/linux/endorsed-distros.md)（针对 Linux VHD）。</li></ul> |
| 客户使用情况归因 | 发布到 Azure 市场的所有解决方案模板都需要启用客户使用情况归因。 有关客户使用情况属性及其启用方式，请参阅 [Azure 合作伙伴客户使用情况属性](./azure-partner-customer-usage-attribution.md)。  |
| 使用托管磁盘 | [托管磁盘](../virtual-machines/managed-disks-overview.md)是 Azure 中的基础结构即服务 (IaaS) VM 的持久性磁盘的默认选项。 必须使用解决方案模板中的托管磁盘。 <ul><li>若要更新解决方案模板，请遵循[使用 Azure 资源管理器模板中的托管磁盘](../virtual-machines/using-managed-disks-template-deployments.md)中的指南，并使用提供的[示例](https://github.com/Azure/azure-quickstart-templates)。<br><br> </li><li>若要在 Azure 市场中将 VHD 作为映像发布，请使用下面任一方法将托管磁盘的基础 VHD 导入到存储帐户：<ul><li>[Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) </li> <li> [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) </li> </ul></ul> |

## <a name="next-steps"></a>后续步骤

如果尚未执行此操作，请了解如何[使用 Azure 市场发展云业务](https://azuremarketplace.microsoft.com/sell)。

若要注册并开始使用合作伙伴中心，请执行以下操作：

- [登录合作伙伴中心](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership)以创建或完成产品/服务。
- 有关详细信息，请参阅[创建 Azure 应用程序产品/服务](./azure-app-offer-setup.md)。
