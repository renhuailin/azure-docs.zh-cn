---
title: 规划虚拟机产品/服务-Microsoft 商业应用商店
description: 本文介绍了向 Azure Marketplace 发布虚拟机产品/服务的要求。
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/19/2020
ms.openlocfilehash: d9e26b6388223ae252a5e85e6e9fb9783a3b8429
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600405"
---
# <a name="how-to-plan-a-virtual-machine-offer"></a>如何规划虚拟机产品/服务

本文介绍将虚拟机 (VM) 产品/服务发布到商业应用商店的不同选项和要求。 VM 产品/服务是通过 Azure Marketplace 部署并计费的事务产品。

在开始之前，请 [在合作伙伴中心创建一个商用 marketplace 帐户](./partner-center-portal/create-account.md) ，并确保你的帐户已注册到商业市场计划中。

### <a name="technical-fundamentals"></a>技术基础知识

设计、构建和测试产品/服务的过程需要一定的时间，并需要在 Azure 平台和技术方面的专业知识来构建你的产品/服务。 你的工程团队应对 [Azure 虚拟机](https://azure.microsoft.com/services/virtual-machines/)、 [Azure 存储](https://azure.microsoft.com/services/?filter=storage#storage)和 [azure 网络](https://azure.microsoft.com/services/?filter=networking#networking)的工作知识，并熟练使用 [azure 应用程序的设计和体系结构](https://azure.microsoft.com/solutions/architecture/)。 请参阅以下其他技术资源： 

- 教程
  - [Linux VM](../virtual-machines/linux/tutorial-manage-vm.md)
  - [Windows VM](../virtual-machines/windows/tutorial-manage-vm.md)

- 示例
  - [适用于 Linux VM 的 Azure CLI 示例](../virtual-machines/linux/cli-samples.md)
  - [适用于 Linux VM 的 Azure PowerShell](../virtual-machines/linux/powershell-samples.md)
  - [适用于 Windows VM 的 Azure CLI 示例](../virtual-machines/windows/cli-samples.md)
  - [适用于 Windows VM 的 Azure PowerShell](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-quick.md)

## <a name="technical-requirements"></a>技术要求

VM 产品/服务具有以下技术要求：

- 必须 (VHD) 准备一个操作系统虚拟硬盘。 数据磁盘 Vhd 是可选的。 下面更详细地说明了这一点。
- 客户可随时取消你的产品/服务。
- 您必须为您的产品/服务创建至少一个计划。 你的计划基于你选择的 [授权选项](#licensing-options) 而定价。
   > [!IMPORTANT]
   > 计划中的每个 VM 映像必须具有相同的数据磁盘数。

VM 包含两个组件：

- **操作 VHD** –包含与你的产品/服务一起部署的操作系统和解决方案。 准备 VHD 的过程会有所不同，具体取决于它是基于 Linux、Windows 还是基于自定义的 VM。
- **数据磁盘 vhd** (可选) – VM 的专用持久存储。 请不要使用操作系统 VHD（例如 C: 驱动器）来存储持久性信息。 
    - 最多可以包含16个数据磁盘。
    - 每个数据磁盘使用一个 VHD，即使磁盘为空。

    > [!NOTE]
    > 无论使用何种操作系统，都仅添加解决方案所需的最少数据磁盘数。 客户无法在部署时删除映像中包含的磁盘，但始终可以在部署期间或之后添加磁盘。

有关准备技术资产的详细说明，请参阅 [使用已批准的基准创建虚拟机](azure-vm-create-using-approved-base.md) 或 [使用自己的映像创建虚拟机](azure-vm-create-using-own-image.md)。

## <a name="preview-audience"></a>预览版受众

预览版读者可以在发布 Azure Marketplace 之前访问 VM 产品/服务，以便在发布端到端功能之前对其进行测试。 在 " **预览观众** " 页上，可以定义受限的预览受众。 

> [!NOTE]
> 预览受众不同于私有计划。 私有计划仅适用于所选的特定群体。 这使您可以与特定客户协商自定义计划。 有关详细信息，请参阅下一节：计划。

可以将邀请发送到 Microsoft 帐户 (MSA) 或 Azure Active Directory (Azure AD) 电子邮件地址。 手动添加最多10个电子邮件地址，或使用 .csv 文件最多导入20个电子邮件地址。 如果你的产品/服务已处于活动阶段，你仍可以定义预览受众来测试产品/服务的任何更改或更新。

## <a name="plans-and-pricing"></a>规划和定价

VM 产品/服务需要至少一个计划。 计划定义解决方案范围和限制以及关联的定价。 您可以为您的产品/服务创建多个计划，为您的客户提供不同的技术和许可选项以及免费试用版。 有关计划的一般指导，请参阅 [商业 marketplace 的计划和定价](plans-pricing.md) ，包括定价模型、免费试用版和私有计划。 

Vm 完全启用了商务，使用即用即付或自带许可证 (BYOL) 许可模式。 Microsoft 托管商业事务并代你向客户计费。 可获得的好处是能够利用客户与 Microsoft 之间达成的首选付费关系（包括任何企业协议）。 有关详细信息，请参阅 [商业市场事务处理功能](./marketplace-commercial-transaction-capabilities-and-considerations.md)。

> [!NOTE]
> Azure 预付款 (之前称为货币承诺的) 与企业协议相关联，可用于 VM 的 Azure 使用情况，而不是针对你的软件许可费用。

### <a name="licensing-options"></a>授权选项

准备发布新的 VM 产品/服务时，需要决定选择哪个许可选项。 这会确定稍后在合作伙伴中心创建产品/服务时需要提供的其他信息。

以下是适用于 VM 产品/服务的可用许可选项：

| 许可选项 | 事务处理 |
| --- | --- |
| 免费试用 | 为你的客户提供一个、三个或六个月的免费试用版。 |
| 体验版 | 此选项可让你的客户评估 Vm，而无需额外付费。 他们无需成为现有的 Azure 客户即可使用试用体验。 有关详细信息，请参阅 [什么是测试驱动器？](./what-is-test-drive.md) |
| BYOL | "自带许可" 选项可让你的客户将现有软件许可证带到 Azure。\* |
| 基于使用情况 | 此选项也称为即用即付，此选项可让客户按小时付费。 |
| 互动演示  | 使用交互式演示为客户授予解决方案的引导式体验。 优点在于，无需为复杂解决方案提供复杂的设置，就能提供试用体验。 |
|

\* 作为发布者，你支持软件许可证事务的所有方面，包括 (但不限于) 订单、履行、计量、计费、开票、支付和收集。

以下示例显示了 Azure Marketplace 中的 VM 产品/服务，其中包含基于使用情况的定价。

:::image type="content" source="media/vm/sample-offer-screen.png" alt-text="示例 VM 产品/服务屏幕。":::

### <a name="private-plans"></a>私有计划

可以通过将映像和定价作为私有计划发布，将 VM 的发现和部署限制为一组特定的客户。 专用计划可为你的最接近客户创建专用产品/服务的功能，并提供自定义的软件和条款。 这些自定义条款可在一系列场景中提供助力，包括使用特殊定价和条款的现场导向型交易以及对限制性版本软件的早期访问。 利用私有计划，你可以向一组有限的客户提供具体的定价或产品。

有关详细信息，请参阅[Microsoft 商业应用商店中](private-offers.md)[的商业 marketplace 产品/服务](plans-pricing.md)和专用产品/服务的计划和定价。

## <a name="test-drives"></a>体验版

可以选择为 VM 启用测试驱动器。 测试驱动器允许客户在固定的小时数内访问预配置环境。 可以为任何发布选项启用测试驱动器，但此功能具有其他要求。 若要了解有关测试驱动器的详细信息，请参阅 [什么是测试驱动器？](what-is-test-drive.md)。 有关配置不同种类的测试驱动器的信息，请参阅 [测试驱动器技术配置](test-drive-technical-configuration.md)。

> [!TIP]
> 测试驱动器与 [免费试用版](plans-pricing.md#free-trials)不同。 你可以提供一个测试驱动器和/或免费试用版。 它们都为你的客户提供了一个固定时间段的解决方案。 但在实际实施方案中，一个测试驱动器还包括一项针对您的产品关键功能的动手自学教程。

## <a name="customer-leads"></a>潜在顾客

您必须将产品/服务连接到客户关系管理 (CRM) 系统以收集客户信息。 系统将要求客户允许共享其信息。 这些客户详细信息以及提供程序的名称、ID 和在线商店，将被发送到已配置的 CRM 系统。 商业应用商店支持各种 CRM 系统，以及使用 Azure 表或使用电源自动配置 HTTPS 终结点的选项。

你可以在创建产品/服务或之后随时添加或修改 CRM 连接。 有关详细指南，请参阅 [商业 marketplace 产品/服务的客户领导](partner-center-portal/commercial-marketplace-get-customer-leads.md)。

## <a name="legal-contracts"></a>法律协定

为了简化客户的采购过程并降低软件供应商的法律复杂性，Microsoft 提供了一个标准合同，你可以将其用于商业应用商店中的产品/服务。 当你在标准合同下提供软件时，客户只需阅读并接受一次，你就不必创建自定义条款和条件。

如果选择使用标准协定，则可以选择将通用修正术语和最多10个自定义改正添加到标准协定。 你还可以使用自己的条款和条件，而不是标准协定。 你将在 " **属性** " 页中管理这些详细信息。 有关详细信息，请参阅 [Microsoft 商用 marketplace 的标准约定](standard-contract.md)。

> [!NOTE]
> 使用商业应用商店的标准合同发布产品/服务后，不能使用自己的自定义条款和条件。 它是“或”方案。 你可以在标准合同或你自己的条款和条件下提供解决方案。 如果要修改标准约定的条款，可以通过标准协定修正来实现。

## <a name="cloud-solution-providers"></a>云解决方案提供商

在合作伙伴中心创建产品/服务时，你将看到 " **转售通过 csp** " 选项卡。此选项允许属于 Microsoft 云解决方案提供商的合作伙伴 (CSP) 程序将 VM 转售为捆绑产品/服务的一部分。 所有自带许可 (BYOL) 计划将自动选择加入该计划。 还可选择加入到非 BYOL 计划。 有关详细信息，请参阅 [云解决方案提供商计划](cloud-solution-providers.md) 。 

> [!NOTE]
> 云解决方案提供商 (CSP) 合作伙伴渠道选择加入现已推出。 有关通过 Microsoft CSP 合作伙伴渠道营销产品/服务的详细信息，请参阅 [**云解决方案提供商**](./cloud-solution-providers.md)。

## <a name="next-steps"></a>后续步骤

- [在 Azure Marketplace 中创建虚拟机产品/服务](azure-vm-create.md)
- [使用已批准的基准创建虚拟机](azure-vm-create-using-approved-base.md) ，或 [使用自己的映像创建虚拟机](azure-vm-create-using-own-image.md)。
- [套餐列出最佳做法](gtm-offer-listing-best-practices.md)