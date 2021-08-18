---
title: 在 Azure 市场中创建虚拟机产品/服务。
description: 在 Azure 市场中创建虚拟机产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/08/2021
ms.openlocfilehash: 6565fff19d68b42aa102431c6e1afe7bb4b3b4d7
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601064"
---
# <a name="create-a-virtual-machine-offer-on-azure-marketplace"></a>在 Azure 市场中创建虚拟机产品/服务

本文介绍如何在 [Azure 市场](https://azuremarketplace.microsoft.com/)中创建 Azure 虚拟机产品/服务。 它介绍基于 Windows 和基于 Linux 的虚拟机（包含操作系统、虚拟硬盘 (VHD) 以及多达 16 个数据磁盘）。

在开始之前，请[在合作伙伴中心创建商业市场帐户](create-account.md)。 确保帐户已注册加入商业市场计划。

## <a name="before-you-begin"></a>开始之前

查看[规划虚拟机产品/服务](marketplace-virtual-machines.md)（如果尚未这样做）。 其中说明了虚拟机的技术要求，并列出了创建产品/服务时所需的信息和资产。

## <a name="create-a-new-offer"></a>创建新套餐

1. 登录[合作伙伴中心](https://partner.microsoft.com/dashboard/home)。
2. 在左侧窗格中，选择“商业市场” > “概述”。
3. 在“概述”页上，选择“+ 新建产品/服务” > “Azure 虚拟机”。  

    ![显示左窗格菜单选项和“新建产品/服务”按钮的屏幕截图。](./media/create-vm/new-offer-azure-virtual-machine.png)

> [!NOTE]
> 发布产品/服务后，在合作伙伴中心对其进行的任何编辑仅会在重新发布该产品/服务后出现在 Azure 市场中。 确保对其进行更改后始终重新发布产品/服务。

输入“产品/服务 ID”。 这是你帐户中的每个产品/服务的唯一标识符。

- 客户可以在 Azure 市场产品/服务、Azure PowerShell 和 Azure CLI 的 Web 地址中看到此 ID（若有）。
- 只使用小写字母和数字。 该 ID 可以包含连字符和下划线字符，但不能包含空格，并且不得超过 50 个字符。 例如，如果你输入“test-offer-1”，则产品/服务 Web 地址为 `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`。
- 在选择“创建”后，就无法更改产品/服务 ID 了。

输入“产品/服务别名”。 该产品/服务别名是用于合作伙伴中心内产品/服务的名称。

- 此名称不会在 Azure 市场中使用。 它不同于向客户显示的产品/服务名称和其他值。

选择“创建”，以生成产品/服务，并继续操作。 将打开合作伙伴中心的“产品/服务设置”页。

## <a name="test-drive-optional"></a>体验版（可选）

体验版可让潜在客户在固定的小时数内访问预配置的环境，是向他们展示你的产品/服务的极佳方式。 提供体验版可以提高潜在客户转变为实际客户的比率，并带来非常适合建立合作关系的潜在顾客。 若要详细了解体验版，请参阅[什么是体验版？](./what-is-test-drive.md)。

> [!TIP]
> 体验版不同于免费试用版。 你可以提供体验版、免费试用版，或提供此两者。 这两个版本都让客户使用你的解决方案固定的一段时间。 但是，体验版还为产品在真实的实现场景中所展示的重要功能和优势提供自我引导式的动手实践教导。

若要启用体验版，请选中“启用体验版”复选框。 稍后你将配置体验版。 使用体验版时，需要配置 CRM（参阅下一部分）。 若要从产品/服务中删除体验版，请清除此复选框。

## <a name="customer-leads"></a>潜在顾客

[!INCLUDE [Customer leads](includes/customer-leads.md)] 

选择“保存草稿”，然后转到左侧导航菜单中的下一个选项卡“属性”。 

## <a name="next-steps"></a>后续步骤

- [配置虚拟机产品/服务属性](azure-vm-create-properties.md)
- [套餐列出最佳做法](gtm-offer-listing-best-practices.md)
