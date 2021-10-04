---
title: 合作伙伴中心的商业市场提交 API 概述
description: 概要了解商业市场提交 API。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.author: mingshen
author: mingshen-ms
ms.date: 09/22/2021
ms.openlocfilehash: 246f57b09e075baaa17296e0c81c2f756e0f42ac
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129056586"
---
# <a name="commercial-marketplace-submission-api-overview"></a>商业市场提交 API 概述

使用 API 以编程方式查询、发布产品/服务以及为其创建提交。 如果你的帐户管理多个产品/服务，而你想要自动化和优化这些产品/服务的提交过程，那么 API 非常有用。

## <a name="types-of-apis"></a>API 的类型

有两组提交 API 可用：

- 合作伙伴中心提交 API - 一组通用于要通过合作伙伴中心发布的个人使用产品和商用产品的 API。 新功能会不断添加到这组 API。 若要详细了解如何与此 API 集成，请参阅[合作伙伴中心提交 API 加入](submission-api-onboard.md)。 
- 旧版云合作伙伴门户 API - 从弃用的云合作伙伴门户转来的 API；它已集成到合作伙伴中心，可以在合作伙伴中心继续工作。 这组 API 仅处于维护模式；在合作伙伴中心引入的新功能可能不受支持，它应该仅用于在转换到合作伙伴中心之前已经集成的现有产品。 若要详细了解如何继续使用云合作伙伴门户 API，请参阅[云合作伙伴门户 API 参考](cloud-partner-portal-api-overview.md)。

请参阅下表，了解每种产品/服务类型支持的提交 API。

| 产品/服务类型 | 旧版云合作伙伴门户 API 支持 | 合作伙伴中心提交 API 支持 |
| --- | :---: | :---: |
| Azure 应用程序 |  | &#x2714; |
| Azure 容器 | &#x2714; |  |
| Azure 虚拟机 | &#x2714; |  |
| 咨询服务 | &#x2714; |  |
| Dynamics 365 |  | &#x2714; |
| IoT Edge 模块 | &#x2714; |  |
| 托管服务 | &#x2714; |  |
| Power BI 应用 | &#x2714; |  |
| 软件即服务 |  | &#x2714; |
|

Microsoft 365 Office 加载项、Microsoft 365 SharePoint 解决方案、Microsoft 365 Teams 应用和 Power BI 视觉对象不支持提交 API。

## <a name="next-steps"></a>后续步骤

- 根据需要访问适用于你的产品/服务类型的 API 链接
