---
title: 计划 Azure 应用程序产品/服务的解决方案模板
description: 了解使用 Microsoft 合作伙伴中心的商业市场门户创建新的 Azure 应用程序产品/服务的解决方案模板计划所需的内容。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 5f70febb77d61e9d797c618faa82c8275f37bfef
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113431377"
---
# <a name="plan-a-solution-template-for-an-azure-application-offer"></a>计划 Azure 应用程序产品/服务的解决方案模板

本文介绍发布 Azure 应用程序产品/服务的解决方案模板计划的要求。 解决方案模板计划是 Azure 应用程序产品/服务支持的两种计划类型之一。 有关这两种计划类型之间的差异的信息，请参阅[计划类型](plan-azure-application-offer.md#plans)。 如果尚未执行此操作，请参阅[计划 Azure 应用程序产品/服务](plan-azure-application-offer.md)。

解决方案模板计划类型要求使用 [Azure 资源管理器模板（ARM 模板）](../azure-resource-manager/templates/overview.md)来自动部署解决方案基础结构。

## <a name="solution-template-requirements"></a>解决方案模板要求

| 要求 | 详细信息 |
| ------------ | ------------- |
| 计费和计量 | 解决方案模板计划在商业市场中不可处理，但可用于部署通过商业市场计费的付费 VM 产品/服务。 解决方案的 ARM 模板部署的资源是在客户的 Azure 订阅中设置的。 即用即付虚拟机将通过 Microsoft 与客户进行交易并且通过客户的 Azure 订阅进行计费。 <br><br> 对于自带许可 (BYOL) 的计费，尽管 Microsoft 会对客户订阅产生的任何基础结构成本计费，但由你直接与客户处理你的软件许可证费用。 |
| 与 Azure 兼容的虚拟硬盘 (VHD) | 必须基于 Windows 或 Linux 构建 VM。 有关详细信息，请参阅：<ul><li>[创建 Azure VM 技术资产](./azure-vm-create-certification-faq.yml#address-a-vulnerability-or-an-exploit-in-a-vm-offer)（针对 Windows VHD）</li><li>[Azure 支持的 Linux 分发](../virtual-machines/linux/endorsed-distros.md)（针对 Linux VHD）。</li></ul> |
| 客户使用情况归因 | 发布到 Azure 市场的所有解决方案模板都需要启用客户使用情况归因。 有关客户使用情况属性及其启用方式，请参阅 [Azure 合作伙伴客户使用情况属性](azure-partner-customer-usage-attribution.md)。 |
| 使用托管磁盘 | [托管磁盘](../virtual-machines/managed-disks-overview.md)是 Azure 中的基础结构即服务 (IaaS) VM 的持久性磁盘的默认选项。 必须使用解决方案模板中的托管磁盘。<ul><li>若要更新解决方案模板，请遵循[使用 Azure 资源管理器模板中的托管磁盘](../virtual-machines/using-managed-disks-template-deployments.md)中的指南，并使用提供的[示例](https://github.com/Azure/azure-quickstart-templates)。</li><li>若要在 Azure 市场中将 VHD 作为映像发布，请使用 [Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-managed-disks-vhd) 或 [Azure CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd) 将托管磁盘的基础 VHD 导入到存储帐户</ul> |
| 部署包 | 你将需要部署包供客户部署计划。 如果创建的多个计划需要采用相同技术配置，则可以使用相同的计划包。 有关详细信息，请参阅下一部分：部署包。 |
|||

## <a name="deployment-package"></a>部署包

部署包涵盖该计划所需的所有模板文件和任何其他资源，打包为 .zip 文件。

所有 Azure 应用程序必须在 .zip 存档根文件夹中包括这两个文件：

- 资源管理器模板文件，名为 [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)。 此模板定义要部署到客户的 Azure 订阅中的资源。 有关资源管理器模板的示例，请参阅 [Azure 快速入门模板库](https://azure.microsoft.com/resources/templates/)或对应的 [GitHub：Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates)存储库。
- 用于 Azure 应用程序创建体验的用户界面定义，名为 [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md)。 在用户界面中，可以指定让使用者能够提供参数值的元素。

支持的最大文件大小为：

- 压缩 .zip 存档大小最大为 1 Gb
- .zip 存档中任何单个解压缩文件最大为 1 Gb

所有新 Azure 应用程序产品/服务还必须包含一个 [Azure 合作伙伴客户使用情况属性 GUID](azure-partner-customer-usage-attribution.md)。

## <a name="azure-regions"></a>Azure 区域

可以将计划发布到 Azure 公共区域和、Azure 政府区域或两者。 在发布到 [Azure 政府](../azure-government/documentation-government-manage-marketplace-partners.md)之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)中请求试用帐户。

作为发布者，你负责任何符合性控制、安全措施和最佳做法。 Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。

有关商业市场支持的国家和地区列表，请参阅[地理可用性和货币支持](marketplace-geo-availability-currencies.md)。

Azure 政府服务处理受特定政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要了解这些计划的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到计划上的列表，也可以是你自己网站上关于这些认证的符合性说明的链接。 这些链接仅对 Azure 政府客户可见。

## <a name="choose-who-can-see-your-plan"></a>选择计划开放对象

可以将每个计划设为对所有人可见（公用）或仅特定受众可见（专用）。 最多可以创建 100 个计划，其中最多可以有 45 个设为专用。 你可能想创建专用计划为特定客户提供不同的定价选项或技术配置。

你可以使用 Azure 订阅 ID 授予专用计划的访问权限，可选择包括分配的每个订阅 ID 的相关说明。 最多可以手动添加 10 个订阅 ID，使用 .CSV 文件最多可添加 10,000 个订阅 ID。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

> [!NOTE]
> 如果发布专用计划，以后仍可将其设为公用。 但发布公用计划后无法改设为专用。

对于解决方案模板计划，还可以选择在 Azure 市场中隐藏计划。 如果计划只是通过另一个解决方案模板或托管应用程序间接部署的，则可能需要执行此操作。

> [!NOTE]
> 通过云解决方案提供商计划 (CSP) 经销商建立的 Azure 订阅不支持专用计划。

有关详细信息，请参阅 [Microsoft 商用市场中的专用产品/服务](private-offers.md)。

## <a name="next-steps"></a>后续步骤

- [创建 Azure 应用程序产品/服务](azure-app-offer-setup.md)