---
title: API 先决条件 - Azure 市场
description: 使用云合作伙伴门户 API 的先决条件。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/23/2020
ms.openlocfilehash: f5fc77b65f6a83f4f7ca8ed8b8c9294b95307735
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107306"
---
# <a name="api-prerequisites"></a>API 先决条件

> [!NOTE]
> 云合作伙伴门户 API 已与合作伙伴中心集成，并将继续在其中运行。 本次转换引入的变更很少。 请查阅[云合作伙伴门户 API 参考](cloud-partner-portal-api-overview.md)中列出的更改，以确保代码在转换到合作伙伴中心后能够继续正常运行。 只对在转换到合作伙伴中心之前已经集成的现有产品使用 CPP API；新产品应使用合作伙伴中心提交 API。

若要使用云合作伙伴门户 API，需要两个必需的编程资产：服务主体和 Azure Active Directory (Azure AD) 访问令牌。

## <a name="create-service-principal-in-azure-active-directory-tenant"></a>在 Azure Active Directory 租户中创建服务主体

首先，需要在你的 Azure AD 租户中创建一个服务主体。 将在云合作伙伴门户中为此租户分配其自己的一组权限。 你的代码将使用此租户（而不是你的个人凭据）来调用 API。 有关创建服务主体的完整说明，请参阅[操作说明：使用门户创建可访问资源的 Azure AD 应用程序和服务主体](../active-directory/develop/howto-create-service-principal-portal.md)。

## <a name="add-service-principal-to-your-account"></a>将服务主体添加到帐户中

至此，你已经在租户中创建了服务主体，现在可以将其作为用户添加到你的合作伙伴中心门户帐户中。 就像用户一样，服务主体可以成为门户的所有者或参与者。 有关详细信息，请参阅下面的“后续步骤”。

## <a name="next-steps"></a>后续步骤

请参阅[管理 Azure AD 应用程序](manage-aad-apps.md)。
