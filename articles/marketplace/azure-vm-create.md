---
title: 在 Azure 市场中创建虚拟机产品/服务。
description: 了解如何在 Microsoft 商业市场中创建虚拟机产品/服务。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 03/10/2021
ms.openlocfilehash: ffc09daa15e742ca2b5b8a2fa9323e33fe317c60
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200392"
---
# <a name="how-to-create-a-virtual-machine-offer-on-azure-marketplace"></a>如何在 Azure 市场中创建虚拟机产品/服务

本文介绍如何在 [Azure 市场](https://azuremarketplace.microsoft.com/)中创建 Azure 虚拟机产品/服务。 它介绍基于 Windows 和基于 Linux 的虚拟机（包含操作系统、虚拟硬盘 (VHD) 以及多达 16 个数据磁盘）。

在开始之前，请[在合作伙伴中心创建商业市场帐户](partner-center-portal/create-account.md)。 确保帐户已注册加入商业市场计划。

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

## <a name="enable-a-test-drive-optional"></a>启用体验版（可选）

体验版可让潜在客户在固定的小时数内访问预配置的环境，是向他们展示你的产品/服务的极佳方式。 提供体验版可以提高潜在客户转变为实际客户的比率，并带来非常适合建立合作关系的潜在顾客。 若要详细了解体验版，请参阅[什么是体验版？](./what-is-test-drive.md)。

> [!TIP]
> 体验版不同于免费试用版。 你可以提供体验版、免费试用版，或提供此两者。 这两个版本都让客户使用你的解决方案固定的一段时间。 但是，体验版还为产品在真实的实现场景中所展示的重要功能和优势提供自我引导式的动手实践教导。

若要启用体验版，请选中“启用体验版”复选框。 稍后你将配置体验版。 使用体验版时，需要配置 CRM（参阅下一部分）。

## <a name="configure-customer-leads-management"></a>配置潜在客户管理

将产品/服务发布到合作伙伴中心的商业市场时，将其连接到客户关系管理 (CRM) 系统。 这样，只要有人表示有兴趣购买或使用产品，你就会收到客户的联系信息。 如果你要启用体验版（参阅上一部分），则必须连接到 CRM。 否则，连接到 CRM 是可选的选项。

1. 在“潜在客户”下，选择“连接”链接。 
1. 在“连接详细信息”对话框中，选择潜在顾客目标。
1. 填写显示的字段。 有关详细步骤，请参阅以下文章：

   - [将你的产品/服务配置为将潜在顾客发送到 Azure 表](./partner-center-portal/commercial-marketplace-lead-management-instructions-azure-table.md#configure-your-offer-to-send-leads-to-the-azure-table)
   - [配置产品/服务以将潜在顾客发送到 Dynamics 365 Customer Engagement](./partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics.md#configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement)（以前称为 Dynamics CRM Online）
   - [配置产品/服务以将潜在顾客发送到 HTTPS 终结点](./partner-center-portal/commercial-marketplace-lead-management-instructions-https.md#configure-your-offer-to-send-leads-to-the-https-endpoint)
   - [配置你的产品/服务以将潜在顾客发送到 Marketo](./partner-center-portal/commercial-marketplace-lead-management-instructions-marketo.md#configure-your-offer-to-send-leads-to-marketo)
   - [配置你的产品/服务以将潜在顾客发送到 Salesforce](./partner-center-portal/commercial-marketplace-lead-management-instructions-salesforce.md#configure-your-offer-to-send-leads-to-salesforce)

1. 若要验证你提供的配置，请选择“验证”链接。
1. 选择“连接”。

选择“保存草稿”，然后转到左侧导航菜单中的下一个选项卡“属性”。 

## <a name="next-steps"></a>后续步骤

- [如何配置虚拟机产品/服务属性](azure-vm-create-properties.md)
- [套餐列出最佳做法](gtm-offer-listing-best-practices.md)