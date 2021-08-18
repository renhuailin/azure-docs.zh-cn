---
title: 计划虚拟机产品/服务 - Microsoft 商业市场
description: 本文介绍向 Azure 市场发布虚拟机产品/服务的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: 4278a3b0e022015a1e12542cac7699590150b5e4
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602578"
---
# <a name="plan-a-virtual-machine-offer"></a>规划虚拟机产品/服务

本文介绍向商业市场发布虚拟机 (VM) 产品/服务的不同选项和要求。 VM 产品/服务是通过 Azure 市场部署并计费的可交易产品/服务。

在开始之前，先[在合作伙伴中心创建一个商业市场帐户](create-account.md)，并确保你的帐户已在商业市场计划中注册。

### <a name="technical-fundamentals"></a>技术基础知识

设计、生成和测试产品/服务的过程需要一些时间，并需要在 Azure 平台和生成产品/服务相关技术方面有一定的专业知识。 你的工程团队应拥有 [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、[Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 [Azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的工作知识，并熟悉 [Azure 应用程序的设计和体系结构](https://azure.microsoft.com/solutions/architecture/)。 请参阅以下附加技术资源：

- 教程
  - [Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

- 示例
  - [适用于 Linux VM 的 Azure CLI 示例](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [适用于 Linux VM 的 Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/virtual-machine)
  - [适用于 Windows VM 的 Azure CLI 示例](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
  - [适用于 Windows VM 的 Azure PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick)

## <a name="technical-requirements"></a>技术要求

VM 产品/服务具有以下技术要求：

- 必须准备一个操作系统虚拟硬盘 (VHD)。 数据磁盘 VHD 为可选。 下文对此进行了更详细的说明。
- 客户可随时取消你的产品/服务。
- 必须至少为你的产品/服务创建一个计划。 你的计划基于所选的[授权选项](#licensing-options)定价。
   > [!IMPORTANT]
   > 计划中的每个 VM 映像必须具有相同的数据磁盘数。

VM 包含两个组件：

- **操作系统 VHD** - 包含操作系统和使用你的产品/服务部署的解决方案。 准备 VHD 的过程根据 VM 是基于 Linux、基于 Windows 还是自定义 VM 而有所不同。
- **数据磁盘 VHD**（可选）- 适用于 VM 的专用、持久存储。 请不要使用操作系统 VHD（例如 C: 驱动器）来存储持久性信息。 
    - 最多可以包含 16 个数据磁盘。
    - 每个数据磁盘使用一个 VHD，即使磁盘为空。

    > [!NOTE]
    > 无论使用何种操作系统，都仅添加解决方案所需的最少数据磁盘数。 客户无法在部署时删除映像中包含的磁盘，但始终可以在部署期间或之后添加磁盘。

有关准备技术资产的详细说明，请参阅[使用已批准的基础映像创建虚拟机](azure-vm-create-using-approved-base.md)或[使用自己的映像创建虚拟机](azure-vm-create-using-own-image.md)。

## <a name="preview-audience"></a>预览版受众

[!INCLUDE [Test drives section](includes/preview-audience.md)]

## <a name="plans-and-pricing"></a>规划和定价

VM 产品/服务至少需要一个计划。 计划定义解决方案范围和限制以及关联定价。 可以为你的产品/服务创建多个计划，从而为你的客户提供不同的技术和定价选项以及免费试用版。 有关计划（包括定价模型、免费试用版和专用计划）的一般指导，请参阅[适用于商业市场产品/服务的计划和定价](plans-pricing.md)。 

使用即用即付或自带许可 (BYOL) 许可模型为 VM 启用完整商务功能。 Microsoft 托管商业事务并代你向客户计费。 可获得的好处是能够利用客户与 Microsoft 之间达成的首选付费关系（包括任何企业协议）。 有关详细信息，请参阅[商业市场事务处理功能](./marketplace-commercial-transaction-capabilities-and-considerations.md)。

> [!NOTE]
> 目前可针对 VM 的 Azure 使用情况，而非软件许可费用，使用与企业协议关联的 Azure 预付款（以前称为货币承诺付款）。

### <a name="licensing-options"></a>许可选项

准备发布新产品/服务时，你需要决定选择哪个许可选项。 这将会确定稍后在合作伙伴中心创建产品/服务时需要提供的其他信息。

以下是适用于 VM 产品/服务的可用许可选项：

| 许可选项 | 事务处理 |
| --- | --- |
| 免费试用 | 为客户提供为期一个月、三个月或六个月的免费试用版。 |
| 体验版 | 此选项可让你的客户评估 VM，无需额外付费。 客户无需是现有 Azure 客户，即可参与试用体验。 有关详细信息，请参阅[什么是体验版？](./what-is-test-drive.md) |
| BYOL | “自带许可”选项可让你的客户将现有软件许可证带到 Azure。\* |
| 基于使用情况 | 此选项也称为即用即付，可让客户按小时付费。 |
| 互动演示  | 通过互动演示，为客户提供引导式解决方案体验。 优点在于，无需对复杂解决方案进行复杂的设置，就能提供试用体验。 |
|

\*发布者负责支持软件许可证交易的各个方面，包括但不限于：订单、履行、计量、计费、开具发票、付款和费用收集。

以下示例显示了 Azure 市场中具有基于使用情况的定价的 VM 产品/服务。

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="示例 VM 产品/服务屏幕。":::

### <a name="private-plans"></a>专用计划

通过将映像和定价发布为专用计划，可将 VM 的发现和部署限制为特定的一组客户。 专用计划能够为关系最密切的客户提供专属优惠，并提供自定义软件和条款。 这些自定义条款可在一系列场景中提供助力，包括使用特殊定价和条款的现场导向型交易以及对限制性版本软件的早期访问。 专用计划可让你向有限的一组客户提供特定的定价或产品。

有关详细信息，请参阅[商业市场产品/服务的计划和定价](plans-pricing.md)和 [Microsoft 商业市场中的专用产品/服务](private-offers.md)。

## <a name="test-drive"></a>体验版

可以选择为 VM 启用体验版。 体验版允许客户在固定的小时数内访问预配置环境。 可以为任何发布选项启用体验版，但此功能还有其他要求。 若要详细了解体验版，请参阅[什么是体验版？](what-is-test-drive.md)。 有关配置不同类型的体验版的信息，请参阅[体验版技术配置](test-drive-technical-configuration.md)。

[!INCLUDE [Test drives section](includes/test-drives.md)]

## <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Customer leads section](includes/customer-leads.md)]

## <a name="legal-contracts"></a>法律协定

[!INCLUDE [Legal contracts section](includes/legal-contracts-intro.md)]
[!INCLUDE [Legal section for Standard contracts](includes/legal-contracts-standard.md)]

## <a name="cloud-solution-providers"></a>云解决方案提供商

在合作伙伴中心创建产品/服务时，会显示“通过 CSP 转售”选项卡。此选项允许参加 Microsoft 云解决方案提供商 (CSP) 计划的合作伙伴将 VM 作为捆绑产品/服务的一部分进行转售。 所有自带许可 (BYOL) 计划将自动选择加入该计划。 还可选择加入到非 BYOL 计划。 有关详细信息，请参阅[云解决方案提供商](cloud-solution-providers.md)。 

> [!NOTE]
> 现已可选择加入云解决方案提供商 (CSP) 合作伙伴渠道。 若要详细了解如何通过 Microsoft CSP 合作伙伴渠道来推广产品/服务，请参阅 [**云解决方案提供商**](./cloud-solution-providers.md)。

## <a name="next-steps"></a>后续步骤

- [在 Azure 市场中创建虚拟机产品/服务](azure-vm-create.md)
- [使用已批准的基础映像创建虚拟机](azure-vm-create-using-approved-base.md)或[使用自己的映像创建虚拟机](azure-vm-create-using-own-image.md)。
- [套餐列出最佳做法](gtm-offer-listing-best-practices.md)
