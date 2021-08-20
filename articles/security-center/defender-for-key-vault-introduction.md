---
title: 用于 Key Vault 的 Azure Defender - 优点和功能
description: 了解用于 Key Vault 的 Azure Defender 的优点和功能。
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: overview
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 51908deb8e24ce7106b937450683309046440ae7
ms.sourcegitcommit: 0fd913b67ba3535b5085ba38831badc5a9e3b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2021
ms.locfileid: "113487302"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>用于 Key Vault 的 Azure Defender 简介

Azure 密钥保管库是一种云服务，用于保护加密密钥和机密（例如证书、连接字符串和密码）。 

启用适用于 Key Vault 的 Azure Defender，这是针对 Azure Key Vault 的 Azure 原生高级威胁防护，提供额外的安全情报层。 

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|用于 Key Vault 的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中所示的定价计费|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/no-icon.png":::国家/地区/主权（US Gov，Azure 中国）|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>适用于 Key Vault 的 Azure Defender 有哪些优点？

Azure Defender 会检测以非寻常和可能有害方式访问或恶意利用密钥保管库帐户的企图。 借助此保护层，无需成为安全专家，也无需管理第三方安全监视系统，即可应对威胁。  

当发生异常活动时，Azure Defender 会显示警报，并选择性地通过电子邮件将其发送给组织中的相关成员。 这些警报包含可疑活动的详细信息，以及有关如何调查和修正威胁的建议。 

## <a name="azure-defender-for-key-vault-alerts"></a>适用于 Key Vault 的 Azure Defender 警报
当收到来自适用于 Key Vault 的 Azure Defender 的警报时，建议按[响应适用于 Key Vault 的 Azure Defender](defender-for-key-vault-usage.md) 中的说明，调查和响应警报。 适用于 Key Vault 的 Azure Defender 旨在为应用程序和凭据提供保护，因此即使你熟悉触发警报的应用程序或用户，也请基于每个警报检查相关情况。

警报显示在 Key Vault 的“安全”页、Azure Defender 仪表板和安全中心的“警报”页中。

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Azure Key Vault 的“安全”页":::


> [!TIP]
> 可以按照[验证 Azure 安全中心中的 Azure Key Vault 威胁检测](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)中的说明，模拟适用于 Key Vault 的 Azure Defender 警报。


## <a name="next-steps"></a>后续步骤

本文介绍了适用于 Key Vault 的 Azure Defender。

如需相关材料，请参阅以下文章： 

- [Key Vault 安全警报](alerts-reference.md#alerts-azurekv) - 所有 Azure 安全中心警报的引用表的 Key Vault 部分
- [连续导出安全中心数据](continuous-export.md)
- [禁止来自 Azure Defender 的警报](alerts-suppression-rules.md)