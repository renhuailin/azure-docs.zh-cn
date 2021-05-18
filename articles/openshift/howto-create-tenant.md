---
title: 为 Azure Red Hat OpenShift 创建 Azure AD 租户
description: 下面介绍了如何创建一个 Azure Active Directory (Azure AD) 租户来托管 Microsoft Azure Red Hat OpenShift 群集。
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: b98f02adeb850f16127658c7d02d44754512e216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100635003"
---
# <a name="create-an-azure-ad-tenant-for-azure-red-hat-openshift"></a>为 Azure Red Hat OpenShift 创建 Azure AD 租户

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月 30 日停用。 对新建 Azure Red Hat OpenShift 3.11 群集的支持会持续到 2020 年 11 月 30 日。 在停用之后，剩余的 Azure Red Hat OpenShift 3.11 群集将会关闭，以防出现安全漏洞。
> 
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:arofeedback@microsoft.com)。

Microsoft Azure Red Hat OpenShift 需要一个在其中创建你的群集的 [Azure Active Directory (Azure AD)](../active-directory/develop/quickstart-create-new-tenant.md) 租户。 租户是 Azure AD 专用实例，组织或应用开发人员通过注册 Azure、Microsoft Intune 或 Microsoft 365 与 Microsoft 建立关系时会收到该实例。 每个 Azure AD 租户都与其他 Azure AD 租户不同并单独存在，而且具有自己的工作和学校标识以及应用注册。

如果尚没有 Azure AD 租户，可按照这些说明创建一个。

## <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

创建租户：

1. 使用你希望与 Azure Red Hat OpenShift 群集关联的帐户登录 [Azure 门户](https://portal.azure.com/)。
2. 打开[“Azure Active Directory”边栏选项卡](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)，创建一个新的租户（也称为新的 Azure Active Directory）。
3. 提供一个组织名称。
4. 提供一个初始域名。 这会向其追加 onmicrosoft.com。 可在此处重用“组织名称”的值。
5. 选择要创建租户的国家或地区。
6. 单击“创建”。
7. 创建 Azure AD 租户后，选择“单击此处以管理新目录”链接。 新租户名称应显示在 Azure 门户的右上角：  

    ![门户的屏幕截图，其中在右上角显示了租户名称][tenantcallout]  

8. 记下租户 ID，以便之后可以指定在何处创建 Azure Red Hat OpenShift 群集。 在门户中，你现在应可看到新租户的 Azure Active Directory 概述边栏选项卡。 选择“属性”并复制“目录 ID”的值 。 在[创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程中，我们将引用此值作为 `TENANT`。

[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="resources"></a>资源

有关 [Azure AD 租户](../active-directory/index.yml)的详细信息，请参阅 [Azure Active Directory 文档](../active-directory/develop/quickstart-create-new-tenant.md)。

## <a name="next-steps"></a>后续步骤

了解如何创建服务主体、生成客户端密码和身份验证回叫 URL 并创建新的 Active Directory 用户以在 Azure Red Hat OpenShift 群集上测试应用。

[创建 Azure AD 应用对象和用户](howto-aad-app-configuration.md)