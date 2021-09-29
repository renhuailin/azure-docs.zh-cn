---
title: 监视和跟踪 Azure 免费服务使用情况
description: 了解如何在 Azure 门户中检查免费服务使用情况。 除非超出服务限值，否则免费帐户中包含的服务不产生任何费用。
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 09/15/2021
ms.author: banders
ms.openlocfilehash: c6a316d11f4d6d43561daab97e2b7b8d4f197a77
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610420"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>检查 Azure 免费帐户中包含的免费服务的使用情况

我们不会对 Azure 免费帐户中包含的免费服务收费，除非超出这些服务的限值。 为了保持在限值内，可使用 Azure 门户跟踪免费服务使用情况。

## <a name="check-usage-in-the-azure-portal"></a>在 Azure 门户中检查使用情况

1.  登录 [Azure 门户](https://portal.azure.com)。
1.  搜索“订阅”。  
    ![显示在门户中搜索订阅的屏幕截图](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  选择在注册 Azure 免费帐户时创建的订阅。
1.  向下滚动以查找显示免费服务使用情况的表。  
    ![显示免费服务使用情况的屏幕截图](./media/check-free-service-usage/subscription-usage-free-services.png)

该表具有以下列：

* **计量：** 列出耗用服务的度量单位。
* **使用情况/限值：** 当前月份的测定仪使用情况和限值。
* **状态：** 服务的使用状态。 根据使用情况，可能会具有以下状态之一：
  * **未使用：** 未使用测定仪或测定仪的使用情况尚未提交至计费系统。
  * **超出限值的日期\<Date>：** 超出测定仪限值的日期 \<Date>。
  * **不可能超过：** 不太可能超过测定仪的限值。
  * **超出限值的日期\<Date>：** 可能会于 \<Date> 超过测定仪限值。

> [!IMPORTANT]
>
> 免费服务仅适用于注册 Azure 免费帐户时创建的订阅。 如果在订阅概述页中看不到免费服务表，则它们对订阅不可用。

## <a name="need-help-contact-us"></a>需要帮助？ 请联系我们。

如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。

## <a name="next-steps"></a>后续步骤
- [升级 Azure 免费帐户](upgrade-azure-subscription.md)
