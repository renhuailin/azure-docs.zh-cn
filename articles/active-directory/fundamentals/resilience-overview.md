---
title: 使用 Azure Active Directory 构建可复原的标识和访问管理
description: 为架构师、IT 管理员和开发人员提供有关如何为其标识系统的中断提供复原能力的指南。
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
ms.openlocfilehash: 7dfd51b0ed43badbc6a4882f619cb718952b0e85
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919334"
---
# <a name="building-resilience-into-identity-and-access-management-with-azure-active-directory"></a>使用 Azure Active Directory 在标识和访问管理中构建复原能力

 (IAM) 的标识和访问管理是一种流程、策略和技术的框架，可帮助管理标识及其访问的内容。 它包括许多组件，这些组件支持用户的身份验证和授权以及系统中的其他帐户。

IAM 复原功能能够经受系统组件中断，并在对业务、用户、客户和操作影响最小的情况下进行恢复。 降低依赖项、复杂性和单点故障，同时确保全面的错误处理会提高恢复能力。

中断可能来自 IAM 系统的任何组件。 为了构建复原的 IAM 系统，会发生中断并对其进行规划。 

规划 IAM 解决方案的复原能力时，请考虑以下元素： 

* 依赖于 IAM 系统的应用程序。

* 身份验证呼叫使用的公共基础结构，包括电信公司、Internet 服务提供商和公钥提供商。

* 云和本地标识提供者。

* 依赖于 IAM 的其他服务以及连接这些服务的 Api。

* 系统中的任何其他本地组件。

任何源、识别和规划紧急情况都非常重要。 但是，添加更多的标识系统及其产生的依赖项和复杂性可能会降低复原能力，而不会增加复原能力。

若要在系统中生成更多的复原能力，请查看以下文章：

* [在 IAM 基础结构中构建复原能力](resilience-in-infrastructure.md)

* [在应用程序中构建 IAM 复原能力](resilience-app-development-overview.md)

* [在 CIAM 系统中构建复原能力](resilience-b2c.md)
