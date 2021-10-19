---
title: 适用于 DNS 的 Azure Defender - 优点和功能
description: 了解适用于 DNS 的 Azure Defender 的优点和功能
author: memildin
ms.author: memildin
ms.date: 10/11/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: ad2b761a747401646c48dcbf32385b8e5ec153c1
ms.sourcegitcommit: ee5d9cdaf691f578f2e390101bf5350859d85c67
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129740220"
---
# <a name="introduction-to-azure-defender-for-dns"></a>适用于 DNS 的 Azure Defender 简介

对于使用 Azure DNS 的[Azure 提供的名称解析](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution)功能的资源，Azure Defender for DNS 提供了额外的一层保护。 

在 Azure DNS 中，Defender for DNS 会监视来自这些资源的查询并检测可疑活动，而无需对资源使用任何其他代理。

## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|用于 DNS 的 Azure Defender 按[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)中显示的定价计费|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 中国世纪互联<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 政府|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-dns"></a>适用于 DNS 的 Azure Defender 有哪些优点？

Azure Defender for DNS 会检测可疑活动和异常活动，例如：

- 使用 DNS 隧道从 Azure 资源中泄漏数据
- 与命令和控制服务器通信的恶意软件
- DNS 攻击 - 与恶意 DNS 解析器进行通信 
- 与用于恶意活动（如钓鱼和加密挖掘）的域通信

[警报参考页](alerts-reference.md#alerts-dns)上提供了由适用于 DNS 的 Azure Defender 提供的警报的完整列表。

## <a name="dependencies"></a>依赖项

适用于 DNS 的 Azure Defender 不使用任何代理。 

若要保护 DNS 层，请按照[启用 Azure Defender](enable-azure-defender.md) 中的说明为每个订阅启用适用于 DNS 的 Azure Defender。


## <a name="next-steps"></a>后续步骤

本文介绍了适用于 DNS 的 Azure Defender。 

> [!div class="nextstepaction"]
> [启用 Azure Defender](enable-azure-defender.md)

如需相关材料，请参阅以下文章： 

- 安全警报可能是由安全中心生成的，也可能是由安全中心从其他安全产品接收的。 若要将所有这些警报导出到 Azure Sentinel、任何第三方 SIEM 或任何其他外部工具，请按照[将警报导出到 SIEM](continuous-export.md) 中的说明操作。
