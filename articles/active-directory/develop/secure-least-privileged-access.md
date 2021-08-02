---
title: Azure AD 上最小特权访问的最佳做法 - Microsoft 标识平台
description: 了解一组关于最小特权的最佳做法和一般指南。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/26/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: db2eb2dcda894b997f485795ab62d983966a7f2f
ms.sourcegitcommit: eb20dcc97827ef255cb4ab2131a39b8cebe21258
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/03/2021
ms.locfileid: "111372743"
---
# <a name="best-practices-for-least-privileged-access-for-applications"></a>应用程序最小特权访问的最佳做法

最小特权原则是一个信息安全概念，它强调这样一个理念：应向用户和应用程序授予执行任务所需的最小访问权限。 了解最小特权原则可帮助你为客户构建值得信赖的应用程序。

最小特权的采用不仅是一种好的安全做法。 该概念可帮助你保持数据的完整性和安全性。 它还通过防止应用程序访问必要数据以外的数据来保护数据隐私并降低风险。 从更广泛的层面来看，采用最小特权原则是组织可以通过[零信任](https://www.microsoft.com/security/business/zero-trust)实现主动安全性的方式之一。

本文介绍了一组最佳做法，可通过这些最佳做法，采用最小特权原则，让最终用户更安全地使用你的应用程序。 你将了解最小特权的以下几个方面：
- 如何将同意与权限配合使用
- 应用被授予超出所需的特权或最小特权意味着什么
- 开发人员如何运用最小特权
- 组织如何运用最小特权

## <a name="using-consent-to-control-access-permissions-to-data"></a>使用同意来控制对数据的访问权限

访问受保护数据需要最终用户的[同意](../develop/application-consent-experience.md#consent-and-permissions)。 每当你的用户设备中运行的应用程序请求访问受保护数据时，该应用都应征求用户的同意，然后再授予对受保护数据的访问权限。 在应用程序可以继续执行操作之前，最终用户需要同意（或拒绝）所请求的权限。 作为应用程序开发人员，最佳做法是使用最小特权来请求访问权限。

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="Azure 门户屏幕截图显示应用注册的“API 权限”窗格。":::

## <a name="overprivileged-and-least-privileged-applications"></a>特权超过需要和特权最小的应用程序

特权超过需要的应用程序可能具有以下特征之一：
- **未使用的权限**：当应用程序在进行 API 调用时未能用上授予它的全部权限，就会出现未使用的权限。 例如，在 [MS Graph](/graph/overview) 中，某个应用可能只读取 OneDrive 文件（使用“Files.Read.All”权限），但却同时获得了了“Calendars.Read”权限，但该应用并未与任何日历 API 集成 。
- **可缩减的权限**：当授予的权限可由其他可完成所需 API 调用的更小的权限替代时，应用便具有可缩减的权限。 例如，某个应用只读取用户个人资料但获得了“User.ReadWrite.All”权限，就可能会认为该应用权限过高。 在这种情况下，应改为向该应用授予“User.Read.All”权限，这是可满足其需求的最小权限。

被视为最小特权的应用程序应具有：
- **恰好足够的权限**：仅提供应用程序、服务或系统的最终用户执行所需任务时所需的一组最小权限。

## <a name="approaching-least-privilege-as-an-application-developer"></a>应用程序开发人员处理最小特权

作为开发人员，你有责任为客户数据的安全做出贡献。 开发应用程序时，你需要采用最小特权原则。 建议按照以下步骤操作以防止应用程序被过度授权：
- 充分了解应用程序需执行的 API 调用所需的权限
- 了解应用使用 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)进行每个 API 调用时所需的最小权限
- 查找最低到最高的相应[权限](/graph/permissions-reference)
- 在应用执行的 API 调用具有重叠权限的情况下，删除任何重复的权限集
- 通过在权限列表中选择最小特权权限，仅将最小特权权限集应用到应用程序

## <a name="approaching-least-privilege-as-an-organization"></a>作为一个组织处理最低权限

组织通常不愿修改现有应用程序，因为这可能会影响业务运营，但如果已授予的权限超过所需、需要撤销时，操作起来会比较麻烦。 作为组织，定期检查和审查你的权限是一种很好的做法。 建议按照以下步骤使应用程序保持正常运行：
- 评估从应用程序发出的 API 调用
- 通过 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)和 [Microsoft Graph](/graph/overview) 文档了解所需权限和最小特权权限
- 审核向用户或应用程序授予的权限
- 使用最小特权权限集更新应用程序
- 定期进行权限审查以确保授予的所有权限仍为相关状态

## <a name="next-steps"></a>后续步骤

- 有关 Azure Active Directory 中同意和权限的详细信息，请参阅[了解 Azure AD 应用程序同意体验](../develop/application-consent-experience.md)。
- 有关 Microsoft 标识中的权限和同意的详细信息，请参阅 [Microsoft 标识平台中的权限和同意](../develop/v2-permissions-and-consent.md)。
- 有关零信任的详细信息，请参阅[零信任部署中心](/security/zero-trust/)。
