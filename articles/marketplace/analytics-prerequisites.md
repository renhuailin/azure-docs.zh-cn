---
title: 以编程方式访问分析数据的先决条件
description: 了解在能以编程方式访问商业市场分析数据之前需要满足的要求。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: smannepalle
ms.author: smannepalle
ms.reviewer: sroy
ms.date: 3/08/2021
ms.openlocfilehash: a3c27a8e91ef1f202fbc757eb1a21eeddfc1d3d8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748280"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>以编程方式访问分析数据的先决条件

需要满足以下要求，然后才能以编程方式访问商业市场分析数据。

## <a name="commercial-marketplace-enrollment"></a>商业市场注册

若要以编程方式访问商业市场分析数据，需要在商业市场计划中注册，并拥有一个合作伙伴中心帐户。 若要了解如何操作，请参阅[在合作伙伴中心创建商业市场帐户](create-account.md)。

## <a name="create-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

普通用户凭据不能用于以编程方式访问商业市场分析数据。 需要创建 Azure Active Directory (Azure AD) 应用程序，以及用于访问分析 API 的机密。 若要了解如何创建 Azure AD 应用程序和机密，请参阅[快速入门：将应用程序注册到 Microsoft 标识平台](../active-directory/develop/quickstart-register-app.md)。

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>将 Azure AD 应用程序关联到合作伙伴中心租户

在 Azure 门户中创建的 Azure AD 应用程序需要关联到合作伙伴中心帐户。 步骤如下：

1. 登录[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165507)。
1. 在右上角选择齿轮图标，然后选择“帐户设置”。
1. 在“帐户设置”菜单中，选择“用户管理” 。
1. 选择“Azure AD 应用程序”，然后选择“+创建 Azure AD 应用程序” 。
1. 选择在 Azure 门户中创建的 Azure AD 应用程序，然后选择“下一步”。
1. 选择“管理器(Windows)”复选框，然后选择“添加” 。

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="说明“创建 Azure AD 应用程序”页面，其中角色选择复选框。":::

## <a name="generate-an-azure-ad-token"></a>生成 Azure AD 令牌

需要使用应用程序（客户端）ID 生成 Azure AD 令牌。 此 ID 有助于唯一地标识 Microsoft 标识平台中的客户端应用程序以及上一步中的客户端密码。 有关生成 Azure AD 令牌的步骤，请参阅[使用客户端凭据（共享机密或证书）进行服务到服务调用](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)。

> [!NOTE]
> 令牌的有效期为 1 小时。

## <a name="next-steps"></a>后续步骤

- [以编程方式访问的范例](analytics-programmatic-access.md)