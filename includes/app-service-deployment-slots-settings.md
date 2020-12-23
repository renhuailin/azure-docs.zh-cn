---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 7458f6868d7fbee72b55ad002148691a113c269d
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531896"
---
从另一个部署槽克隆配置时，可以编辑克隆的配置。 某些配置元素在交换时遵循内容（不特定于槽），而其他配置元素会在交换之后保留在同一个槽（特定于槽）。 以下列表显示交换槽时会更改的设置。

**已交换的设置**：

* 常规设置 - 例如 Framework 版本、32/64 位、Web 套接字
* 应用设置（可以配置为停在槽中）
* 连接字符串（可以配置为停在槽中）
* 处理程序映射
* 公用证书
* WebJobs 内容
* 混合连接 *
* 服务终结点 *
* Azure 内容分发网络 *

标有星号 (*) 的功能计划取消交换。 

**不交换的设置**：

* 发布终结点
* 自定义域名
* 非公共证书和 TLS/SSL 设置
* 缩放设置
* Web 作业计划程序
* IP 限制
* Always On
* 诊断设置
* 跨域资源共享 (CORS)
* 虚拟网络集成

> [!NOTE]
> 若要使这些设置可交换，请 `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` 在应用的每个槽中添加应用设置，并将其值设置为 `0` 或 `false` 。 这些设置要么都是可交换的，要么根本不是。 不能仅使某些设置可交换，而不是其他设置。

> 应用于不交换的设置的某些应用设置也不交换。 例如，由于诊断设置不会交换，因此相关的应用设置（如 `WEBSITE_HTTPLOGGING_RETENTION_DAYS` 和 `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS`）也不会交换，即使它们未显示为槽设置也是如此。
>
