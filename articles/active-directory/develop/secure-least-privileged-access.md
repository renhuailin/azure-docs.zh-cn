---
title: 使用最小特权原则增强应用安全性
titleSuffix: Microsoft identity platform
description: 了解最小特权原则如何帮助增强应用程序、其数据以及可用于实现最小特权访问的 Microsoft 标识平台功能的安全性。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: yuhko, saumadan, marsma
ms.openlocfilehash: a7e504bfd13d89ba417067faf88cf17cf12e1d0b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124786477"
---
# <a name="enhance-security-with-the-principle-of-least-privilege"></a>使用最小特权原则增强安全性

根据信息安全性最小特权原则，应仅向用户和应用程序授予对执行其作业所需的数据和操作的访问权限。

按照此处的指南来帮助减少应用程序的攻击面，以及应在集成了 Microsoft 标识平台的应用程序中出现的安全漏洞的影响（*冲击半径*）。

## <a name="recommendations-at-a-glance"></a>建议概览

- 通过撤消未使用的权限和可缩小的权限来阻止特权超过需要的应用程序。
- 使用标识平台的许可框架，要求有人同意应用访问受保护的数据的请求。
- 在开发的所有阶段中，生成应用程序时始终应牢记最小特权原则。
- 定期审核已部署的应用程序来标识特权超过需要的应用。

## <a name="whats-an-overprivileged-application"></a>什么是特权超过需要的应用程序？

授予了未使用的权限或可缩小的权限的任何应用程序都被视为“特权超过需要”。 未使用的权限和可缩小的权限可能会提供对应用或其用户执行作业不需要的数据或操作的未经授权或意外访问。

:::row:::
   :::column span="":::
      ### <a name="unused-permissions"></a>未使用的权限

        未使用的权限是已向应用程序授予，但在按预期使用时该权限公开的 API 或操作不会被应用调用的权限。

        - 示例：应用程序通过调用 Microsoft Graph API 并利用 [Files.Read](/graph/permissions-reference) 权限来显示存储在已登录用户的 OneDrive 中的文件列表。 但是，该应用还被授予了 [Calendars.Read](/graph/permissions-reference#calendars-permissions) 权限，它不提供日历功能，并且不调用日历 API。

        - 安全风险：未使用的权限带来了水平特权提升安全风险。 在应用程序中利用安全漏洞的实体可以使用未使用的权限来访问应用程序在按预期使用时通常不支持或不允许的 API 或操作。

        - 缓解措施：删除应用程序所执行的 API 调用中不使用的任何权限。
   :::column-end:::
   :::column span="":::
      ### <a name="reducible-permissions"></a>可缩小的权限

        可缩小的权限是具有较小特权的对应项的权限，该权限仍会为应用程序及其用户提供执行必要任务所需的访问权限。

        - 示例：应用程序通过调用 Microsoft Graph API 来显示已登录用户的配置文件信息，但不支持编辑配置文件。 但是，为应用授予了 [User.ReadWrite.All](/graph/permissions-reference#user-permissions) 权限。 User.ReadWrite.All 权限在此处被视为可缩小的权限，因为权限较低的 User.Read.All 权限授予对用户配置文件数据足够的只读访问权限。

        - 安全风险：可缩小的权限带来了垂直特权提升安全风险。 在应用程序中利用安全漏洞的实体可以使用可缩小的权限来对数据进行未经授权的访问或执行该实体的角色通常不允许的操作。

        - 缓解措施：将应用程序中的每个可缩小权限替换为其权限最低的对应项，同时仍启用应用程序的所需功能。
   :::column-end:::
:::row-end:::

通过授予刚好足够的权限来避免未使用的权限和可缩小的权限带来的安全风险：具有应用程序或用户执行必要任务所需的最小权限的权限。

## <a name="use-consent-to-control-access-to-data"></a>使用同意来控制对数据的访问权限

你生成的大多数应用程序都需要访问受保护的数据，并且该数据的所有者需要[同意](application-consent-experience.md#consent-and-permissions)该访问权限。 同意可以通过多种方式授予，其中包括通过可同意 Azure AD 租户中的所有用户的租户管理员或可授予访问权限的应用程序用户

每当你的用户设备中运行的应用程序请求访问受保护数据时，该应用都应征求用户的同意，然后再授予对受保护数据的访问权限。 在应用程序可以继续执行操作之前，最终用户需要同意（或拒绝）所请求的权限。

:::image type="content" source="./media/least-privilege-best-practice/api-permissions.png" alt-text="显示应用注册的 API 权限窗格的 Azure 门户屏幕截图":::

## <a name="least-privilege-during-app-development"></a>应用开发过程中的最小特权

开发人员生成应用程序时，你有责任确保应用及其用户的数据的安全。

在应用程序开发过程中遵守这些准则，以帮助避免生成特权超过需要的应用：

- 充分了解应用程序需执行的 API 调用所需的权限。
- 了解应用使用 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)进行每个 API 调用时所需的最小权限。
- 查找最低到最高的相应[权限](/graph/permissions-reference)。
- 在应用执行的 API 调用具有重叠权限的情况下，删除任何重复的权限集。
- 通过在权限列表中选择最小特权权限，仅将最小特权权限集应用到应用程序。

## <a name="least-privilege-for-deployed-apps"></a>已部署应用的最小特权

组织通常不愿修改正在运行的应用程序来避免影响正常的业务运营。 但是，你的组织应考虑缓解特权超过需要的应用权限导致可能产生或更严重的安全事件的风险，这值得进行计划应用程序更新。

在你的组织中实施这些标准实践，以确保部署的应用不会超出所需的特权，并且不会随着时间的推移而超出所需的特权：

- 评估从应用程序发出的 API 调用。
- 通过 [Graph 浏览器](https://developer.microsoft.com/graph/graph-explorer)和 [Microsoft Graph](/graph/overview) 文档了解所需权限和最小特权权限。
- 审核向用户或应用程序授予的权限。
- 使用最小特权权限集更新应用程序。
- 定期进行权限审查以确保授予的所有权限仍为相关状态。

## <a name="next-steps"></a>后续步骤

**受保护资源访问和同意**

若要详细了解如何配置对受保护资源的访问权限，以及同意访问这些受保护资源的用户体验，请参阅以下文章：

- [Microsoft 标识平台中的权限和许可](../develop/v2-permissions-and-consent.md)
- [了解 Azure AD 应用程序许可体验](../develop/application-consent-experience.md)

零信任 - 考虑将此处所述的最小特权措施作为组织的主动[零信任安全策略](/security/zero-trust/)的一部分。
