---
title: 操作指南：使用 Microsoft 标识平台还原或删除最近删除的应用程序 | Azure
titleSuffix: Microsoft identity platform
description: 本操作指南将介绍如何还原或永久删除注册到 Microsoft 标识平台且最近删除的应用程序。
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 3/22/2021
ms.author: arcrowe
ms.custom: aaddev
ms.openlocfilehash: dbe3e16bf56c5480fbe4aff8dad78bbbb5591f67
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079691"
---
# <a name="restore-or-remove-a-recently-deleted-application-with-the-microsoft-identity-platform"></a>使用 Microsoft 标识平台还原或删除最近删除的应用程序
删除应用注册后，应用将保持挂起状态 30 天。 在此 30 天期间，可以还原应用注册及其所有属性。 30 天期限过后，将无法还原应用注册，并且可能会自动启动永久删除进程。  此功能仅适用于与目录关联的应用程序，  不适用于个人 Microsoft 帐户中的应用程序，这些应用程序无法还原。

可在 Azure 门户中使用 Azure Active Directory (Azure AD) 下的应用注册体验，查看已删除的应用程序、还原已删除的应用程序或永久删除应用程序。

请注意，你本人和 Microsoft 客户支持人员均不能还原永久删除的应用程序或已删除超过 30 天的应用程序。

> [!IMPORTANT]
> 已删除的应用程序门户 UI 功能[!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

## <a name="required-permissions"></a>所需的权限
必须具有以下角色之一才能永久删除应用程序。

- 全局管理员
- 应用程序管理员
- 云应用程序管理员
- 混合标识管理员
- 应用程序所有者

必须具有以下角色之一才能还原应用程序。

- 全局管理员
- 应用程序所有者

### <a name="view-your-deleted-applications"></a>查看已删除的应用程序
你可以看到所有应用程序均处于软删除状态。  只能还原过去 30 天内删除的应用程序。

#### <a name="to-view-your-restorable-applications"></a>若要查看可还原的应用程序
1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 搜索并选择“Azure Active Directory”，选择“应用注册”，然后选择“已删除的应用程序(预览)”选项卡  。

查看应用程序列表。 只有过去 30 天内删除的应用程序可以还原。 如果使用“应用注册搜索预览”，可按“删除日期”列筛选以仅查看这些应用程序。

## <a name="restore-a-recently-deleted-application"></a>还原最近删除的应用程序

从组织中删除应用注册时，该应用将处于挂起状态，并且其配置会保留。 还原应用注册时，其配置也会还原。  但是，如果应用程序的主租户的企业应用程序中有任何组织特定的设置，这些设置不会还原。  

这是因为组织特定的设置存储在名为服务主体的单独对象上。  服务主体中保留的设置包括有关特定组织的权限同意以及用户和组分配；在还原应用时，这些配置不会还原。 有关详细信息，请参阅[应用程序和服务主体对象](app-objects-and-service-principals.md)。 


### <a name="to-restore-an-application"></a>若要还原应用程序
1. 在“已删除的应用程序(预览)”选项卡上，搜索并选择一个过去 30 天内删除的应用程序。
2. 选择“还原应用注册”。

## <a name="permanently-delete-an-application"></a>永久删除应用程序
可手动从组织中永久删除应用程序。 你本人、其他管理员或 Microsoft 客户支持人员不能还原已永久删除的应用程序。

### <a name="to-permanently-delete-an-application"></a>若要永久删除应用程序

1. 在“已删除的应用程序(预览)”选项卡上，搜索并选择一个可用应用程序。
2. 选择“永久删除”  。
3. 阅读警告文本，然后选择“是”。

## <a name="next-steps"></a>后续步骤
还原或永久删除应用后，可以：

- [添加应用程序](quickstart-register-app.md)。
- 详细了解 Microsoft 标识平台中的[应用程序和服务主体对象](app-objects-and-service-principals.md)。
