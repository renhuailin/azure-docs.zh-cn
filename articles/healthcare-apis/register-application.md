---
title: 在 Azure 医疗保健 API Azure Active Directory注册客户端应用程序
description: 如何在门户中注册客户端Azure AD以及如何向 Azure 医疗保健 API 添加机密和 API 权限
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: b1ac54f71c9c49af5bb8656bdbe05d639b8bca25
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278041"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>在 Azure Active Directory

> [!IMPORTANT]
> Azure Healthcare APIs 目前为预览版。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

本文将了解如何在 Azure Active Directory (Azure AD) 中注册客户端应用程序，以便访问医疗保健 API。 有关向应用程序注册[应用程序的信息，Microsoft 标识平台。](../active-directory/develop/quickstart-register-app.md)

## <a name="register-a-new-application"></a>注册新应用程序

1. 在 [Azure 门户](https://portal.azure.com)中，选择“Azure Active Directory”  。
2. 选择“应用注册”。
[![新应用注册窗口的屏幕截图。 ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. 选择“新注册”。
4. 对于"支持的帐户类型"，请选择"**仅此组织目录中的帐户"。** 保留其他选项。
[![新注册帐户选项的屏幕截图。 ](media/register-application-two.png) ](media/register-application-two.png#lightbox)
5. 选择“注册”。

## <a name="application-id-client-id"></a>应用程序 ID (客户端 ID) 

注册新应用程序后，可以从"概述"菜单 (") " (") "租户 ID"找到应用程序。 记下这些值，供稍后使用。

[![客户端 ID 概述面板的屏幕截图。 ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![客户端 ID 的屏幕截图 ](media/register-application-four.png) ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>身份验证设置：机密与公共

单击" **身份验证** "查看设置。 "允许公共客户端 **流"的默认值** 为"否"。

如果保留此默认值，则应用程序注册 **是机密客户端** 应用程序，需要证书或机密。

[![机密客户端应用程序的屏幕截图。 ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

如果将默认值更改为"是"，则应用程序注册是公共客户端应用程序，不需要证书或机密。 当你想要在移动应用或不想存储任何机密的 JavaScript 应用中使用客户端应用程序时，"是"值很有用。

对于需要重定向 URL 的工具，请选择 **"添加平台** "以配置平台。

[![添加平台的屏幕截图。 ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

对于 Postman，请选择 **"移动和桌面应用程序"。** 在" https://www.getpostman.com/oauth2/callback 自定义重定向 **URI"部分中输入** ""。 选择" **配置** "按钮以保存设置。

[![配置其他服务的屏幕截图。 ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>证书&机密

选择 **"证书&机密**"，然后选择"**新建客户端机密"。** 在 **"过期时间"字段中** 选择 **"建议** 6 个月"。 此新机密的有效期为六个月。 还可以选择不同的值，例如：
 
* 03 个月
* 12 个月
* 24 个月
* 自定义开始日期和结束日期。

>[!NOTE]
>保存机密值（而不是机密 ID）非常重要。

[![证书和机密的屏幕截图。 ](media/register-application-six.png) ](media/register-application-six.png#lightbox)

（可选）可以将证书 (公钥) ，并使用证书 ID（与证书关联的 GUID 值）。 出于测试目的，可以使用 PowerShell 命令行等工具创建自签名证书，然后从证书存储 `New-SelfSignedCertificate` 中导出证书。

## <a name="api-permissions"></a>API 权限

医疗保健 API 的权限通过 RBAC 进行管理。 有关更多详细信息，请访问[为 FHIR 服务配置 Azure RBAC。](./fhir/configure-azure-rbac-for-fhir.md)

>[!NOTE]
>在grant_type Postman client_credentials Rest Client 等工具为 FHIR 服务提供访问令牌时，请使用以下方法。 有关更多详细信息，请访问使用[Postman](use-postman.md)访问和使用 中 REST 客户端扩展访问医疗保健[Visual Studio Code。](using-rest-client.md)

应用程序注册现已完成。

## <a name="next-steps"></a>后续步骤

本文介绍了如何在客户端应用程序中注册客户端Azure AD。 此外，还了解了如何向 Azure 医疗保健 API 添加机密和 API 权限。 有关 Azure 医疗保健 API 的信息，请参阅

>[!div class="nextstepaction"]
>[Azure 医疗保健 API 概述](healthcare-apis-overview.md)
