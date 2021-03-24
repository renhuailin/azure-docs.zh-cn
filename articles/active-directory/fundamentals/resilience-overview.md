---
title: 使用 Azure Active Directory 构建可复原的标识和访问管理
description: 适用于架构师、IT 管理员和开发人员的有关如何构建复原能力以应对标识系统中断。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39021fecb79f6b8e7536daad592378ed998760a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96454344"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>使用 Azure Active Directory 构建标识和访问管理的复原能力

标识和访问管理 (IAM) 是一种流程、策略和技术的框架，可帮助管理标识和其访问的内容。 它包括许多组件，这些组件支持用户及系统中其他帐户的身份验证和授权。

IAM 复原能力指能经受系统组件中断并在对业务、用户、客户和操作影响最小的情况下恢复的能力。 减少依赖项、复杂性和单点故障，同时确保全面的错误处理，这可以提高复原能力。

中断可能来自 IAM 系统的任何组件。 若要构建具有复原能力的 IAM 系统，应假设会发生中断并进行相应规划。 

规划 IAM 解决方案的复原能力时，应考虑以下元素： 

* 你的依赖于 IAM 系统的应用程序。

* 身份验证调用操作所使用的公共基础结构，包括电信公司、Internet 服务提供商和公钥提供商。

* 云和本地标识提供商。

* 依赖于 IAM 的其他服务以及用于连接这些服务的 API。

* 系统中的任何其他本地组件。

无论是什么源，识别意外情况并进行相应规划都非常重要。 但是，增加更多标识系统以及因其产生的依赖项和复杂性可能会降低而不是增加复原能力。

若要在系统中构建更高的复原能力，请查看以下文章：

* [在 IAM 基础结构中构建复原能力](resilience-in-infrastructure.md)

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在客户标识和访问管理 (CIAM) 系统中构建复原能力](resilience-b2c.md)
