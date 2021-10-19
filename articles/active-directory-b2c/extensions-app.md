---
title: Azure Active Directory B2C 中的扩展应用程序 | Microsoft Docs
description: 还原 b2c-extensions-app。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 044ea1f160fb2e8e5f119a9046ef6989440e346c
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728544"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C：扩展应用

创建 Azure AD B2C 目录后，将在新目录内部自动创建一个名为 `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` 的应用。 此应用称为 b2c-extensions-app  ，可在“应用注册”  中显示。 Azure AD B2C 服务使用它来存储用户和自定义属性的相关信息。 如果删除了此应用，Azure AD B2C 将无法正常工作，你的生产环境也会受到影响。

> [!IMPORTANT]
> 请勿删除 b2c-extensions-app，除非你计划立刻删除你的租户。 如果此应用被删除 30 天以上，用户信息将永久性丢失。

## <a name="verifying-that-the-extensions-app-is-present"></a>验证此扩展应用是否存在

要验证此 b2c-extensions-app 是否存在：

1. 在 Azure AD B2C 租户内，单击左侧导航菜单中的“所有服务”  。
1. 搜索并打开“应用注册”  。
1. 查找以“b2c-extensions-app”  开头的应用

## <a name="recover-the-extensions-app"></a>恢复此扩展应用

如果将此 b2c-extensions-app 意外删除，则你有 30 天的时间可以恢复它。 可以使用 Graph API 还原此应用：

1. 浏览到 [https://developer.microsoft.com/en-us/graph/graph-explorer](https://developer.microsoft.com/en-us/graph/graph-explorer)。
1. 以要还原的已删除应用所在的 Azure AD B2C 目录的全局管理员身份登录到此网站。 此全局管理员必须具有类似于以下形式的电子邮件地址：`username@{yourTenant}.onmicrosoft.com`。
1. 对 URL `https://graph.microsoft.com/beta/directory/deleteditems/microsoft.graph.application` 发出 HTTP GET。 此操作将列出过去 30 天内删除的所有应用程序。
1. 在列表中找到名称以“b2c-extensions-app”开头的应用程序，并复制其 `objectid` 属性值。
1. 对 URL `https://graph.microsoft.com/beta/directory/deleteditems/{id}/restore` 发出 HTTP POST。 将此 URL 的 `{id}` 部分替换为之前步骤中的 `objectid`。

现在应该可以在 Azure 门户中[查看还原的应用](#verifying-that-the-extensions-app-is-present)。

> [!NOTE]
> 只能还原最近 30 天内删除的应用程序。 如果已超过 30 天，数据将永久性丢失。 若要获取更多帮助，请提交支持票证。
