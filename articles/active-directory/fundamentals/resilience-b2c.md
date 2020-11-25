---
title: 使用 Azure AD B2C 在客户标识和访问管理中构建复原能力 |Microsoft Docs
description: 使用 Azure AD B2C 在客户标识和访问管理中构建复原能力的方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda9378a41e0d98276957a6fad75db2c20e6e4e9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919363"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 在客户标识和访问管理中构建复原能力

[Azure Active Directory (AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview) 是一个客户标识和访问管理 (CIAM) 平台，旨在帮助你成功启动关键客户应用程序。 我们提供了许多用于 [复原](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/) 的内置功能，旨在帮助我们的服务满足你的需求，并在面临潜在中断情况时提高恢复能力。 此外，在启动任务关键型应用程序时，请务必考虑应用程序中的各种设计和配置元素，以及如何在 Azure AD B2C 中配置应用程序，以确保获得可复原的行为，以响应中断或故障情况。 本文介绍一些最佳实践，以帮助提高复原能力。

复原服务可继续正常运行，而不会发生中断。 你可以通过以下方式帮助提高你的服务的复原能力：

- 了解所有组件

- 消除单点故障

- 隔离故障组件以限制其影响

- 通过快速故障转移机制和恢复路径提供冗余

开发应用程序时，我们建议考虑如何在应用程序中利用解决方案的标识组件 [提高身份验证和授权的复原能力](resilience-app-development-overview.md) 。 本文尝试解决特定于 Azure AD B2C 应用程序的复原能力的增强功能。 我们的建议按 CIAM 函数分组。

![Image 显示了 ](media/resilience-b2c/high-level-components.png) 后续部分中的 CIAM 组件，我们将指导你在以下方面构建复原能力：

- [最终用户体验](resilient-end-user-experience.md)：为身份验证流启用后备计划，并缓解 Azure AD B2C 身份验证服务中断造成的潜在影响。

- [与外部进程的接口](resilient-external-processes.md)：通过从错误中恢复来在应用程序和接口中生成复原能力。  

- [开发人员最佳做法](resilience-b2c-developer-best-practices.md)：避免脆弱性，因为存在常见的自定义策略问题，并改进与声明验证程序、第三方应用程序和 REST api 的交互等领域的错误处理。

- [监视和分析](resilience-with-monitoring-alerting.md)：通过监视关键指示器并通过警报检测故障和性能中断来评估服务的运行状况。

- 面向 Azure AD B2C 开发人员的复原资源
  - [复原最终用户体验](resilient-end-user-experience.md)
  - [具有外部进程的弹性接口](resilient-external-processes.md)
  - [通过开发人员的最佳做法实现复原](resilience-b2c-developer-best-practices.md)
  - [通过监视和分析进行恢复](resilience-with-monitoring-alerting.md)
- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)
- [在应用程序中提高身份验证和授权的复原能力](resilience-app-development-overview.md)
