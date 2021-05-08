---
title: 在使用 Azure Active Directory B2C 的客户标识和访问管理中构建复原能力 | Microsoft Docs
description: 在使用 Azure Active Directory B2C 的客户标识和访问管理中构建复原能力的方法
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
ms.openlocfilehash: b40918db03c260f899c36d306c892b787cc6371c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98724885"
---
# <a name="build-resilience-in-your-customer-identity-and-access-management-with-azure-active-directory-b2c"></a>在使用 Azure Active Directory B2C 的客户标识和访问管理中构建复原能力

[Azure Active Directory (AD) B2C](../../active-directory-b2c/overview.md) 是一个客户标识和访问管理 (CIAM) 平台，旨在帮助你成功发布面向客户的关键应用程序。 我们有很多内置功能来实现[复原能力](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/)，它们旨在帮助我们的服务根据你的需求进行缩放，并提高在遇到潜在服务中断情况时的复原能力。 此外，在发布任务关键型应用程序时，有必要考虑到你的应用程序中的各种设计和配置元素，还要考虑如何在 Azure AD B2C 中配置应用程序，确保你在响应服务中断或故障情况时获得可复原的行为。 在本文中，我们将介绍一些可帮助你提高复原能力的最佳做法。

可复原的服务是指遇到中断也能继续正常运行的服务。 你可通过以下方式，帮助提高服务中的复原能力：

- 了解所有组件

- 消除单点故障

- 隔离故障组件来限制其影响

- 通过快速故障转移机制和恢复路径提供冗余

开发应用程序时，建议考虑如何使用解决方案的标识组件来[提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)。 本文尝试介绍 Azure AD B2C 应用程序特定的复原能力的强化。 建议按 CIAM 函数进行分组。

![图像显示 CIAM 组件](media/resilience-b2c/high-level-components.png) 在后续部分，我们将指导你在以下方面构建复原能力：

- [最终用户体验](resilient-end-user-experience.md)：为身份验证流启用回退计划，缓解因 Azure AD B2C 身份验证服务中断而造成的潜在影响。

- [与外部进程的接口](resilient-external-processes.md)：通过从错误中恢复，在应用程序和接口中构建复原能力。  

- [开发人员最佳做法](resilience-b2c-developer-best-practices.md)：避免因常见自定义策略问题而导致服务不畅，提高在与声明验证程序互动、第三方应用程序和 REST API 等方面的错误处理能力。

- [监视和分析](resilience-with-monitoring-alerting.md)：通过监视关键指标来评估服务的运行状况，通过警报来检测故障和性能中断问题。

- [在身份验证基础结构中构建复原能力](resilience-in-infrastructure.md)

- [提高应用程序中身份验证和授权的复原能力](resilience-app-development-overview.md)

观看此视频，了解如何使用 Azure AD B2C 构建可复原且可缩放的流。
>[!Video https://www.youtube.com/embed/8f_Ozpw9yTs]
