---
title: 将属性同步到 Azure Active Directory 以进行映射
description: 通过 Azure Active Directory 和 SaaS 应用来配置用户预配时，请使用目录扩展功能添加默认情况下不同步的源属性。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/24/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 12679b5e8affe7691cc3bf40d2e4f2f9860d074f
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822542"
---
# <a name="syncing-extension-attributes-for-azure-active-directory-application-provisioning"></a>为 Azure Active Directory 应用程序预配同步扩展属性

将用户帐户从 Azure AD 预配到 [SaaS 应用](../saas-apps/tutorial-list.md)时，Azure Active Directory (Azure AD) 必须包含创建用户配置文件所需的所有数据（属性）。 在为用户预配自定义属性映射时，你可能会发现要映射的属性没有显示在“源属性”列表中。 本文介绍了如何添加缺少的属性。

对于仅在 Azure AD 中的用户，你可以[使用 PowerShell 或 Microsoft Graph 创建架构扩展](#create-an-extension-attribute-on-a-cloud-only-user)。

对于本地 Active Directory 中的用户，你必须将用户同步到 Azure AD。 你可以使用 [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) 同步用户和属性。 Azure AD Connect 自动将某些属性（但不是所有属性）同步到 Azure AD。 另外，使用 Azure AD Graph API 时，可能不会公开默认情况下同步的某些属性（例如 SAMAccountName）。 在这些情况下，可以[使用 Azure AD Connect 目录扩展功能将属性同步到 Azure AD](#create-an-extension-attribute-using-azure-ad-connect)。 这样，该属性就会显示给 Azure AD Graph API 和 Azure AD 预配服务。

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>为纯云用户创建扩展属性
你可以使用 Microsoft Graph 和 PowerShell 为 Azure AD 中的用户扩展用户架构。 大多数情况下会自动发现这些扩展属性。

当你的服务主体超过 1000 个时，你可能会发现源属性列表中缺少扩展。 如果你创建的属性未自动显示，请验证是否已创建该属性，然后手动将其添加到你的架构。 若要验证是否已创建该属性，请使用 Microsoft Graph 和 [Graph 浏览器](/graph/graph-explorer/graph-explorer-overview)。 若要手动将其添加到架构，请参阅[编辑受支持属性的列表](customize-application-attributes.md#editing-the-list-of-supported-attributes)。

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>使用 Microsoft Graph 为纯云用户创建扩展属性
你可以使用 [Microsoft Graph](/graph/overview) 扩展 Azure AD 用户的架构。 

首先，列出你的租户中的应用，获取你正在处理的应用的 ID。 若要了解详细信息，请参阅[列出 extensionProperties](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true)。

```json
GET https://graph.microsoft.com/v1.0/applications
```

接下来，创建扩展属性。 将下面的 ID 属性替换为在上一步骤中检索到的 ID。 你需要使用“ID”属性而不是“appId”属性。 若要了解详细信息，请参阅[创建 extensionProperty]/graph/api/application-post-extensionproperty.md?view=graph-rest-1.0&tabs=http&preserve-view=true)。

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

上一请求创建了一个 `extension_appID_extensionName` 格式的扩展属性。 现在，你可以使用此扩展属性更新用户。 若要了解详细信息，请参阅[更新用户](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true)。
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
最后，验证用户的属性。 若要了解详细信息，请参阅[获取用户](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true)。

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>使用 PowerShell 为纯云用户创建扩展属性
使用 PowerShell 创建自定义扩展，并为用户分配值。 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>使用 Azure AD Connect 创建扩展属性

1. 打开 Azure AD Connect 向导，选择“任务”，然后选择“自定义同步选项”。

   ![Azure Active Directory Connect 向导的“其他任务”页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. 以 Azure AD 全局管理员身份登录。 

3. 在“可选功能”页上，选择“目录扩展属性同步” 。
 
   ![Azure Active Directory Connect 向导的“可选功能”页](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. 选择要扩展到 Azure AD 的属性。
   > [!NOTE]
   > “可用属性”下的搜索区分大小写。

   ![显示“目录扩展”选择页的屏幕截图](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. 完成 Azure AD Connect 向导，并允许运行完全同步周期。 周期完成时，架构被扩展，且新值在本地 AD 和 Azure AD 之间同步。
 
6. 在 Azure 门户中，在[编辑用户属性映射](customize-application-attributes.md)时，源属性列表现在将包含 `<attributename> (extension_<appID>_<attributename>)` 格式的已添加属性。 选择该属性并将其映射到用于预配的目标应用程序。

   ![Azure Active Directory Connect 向导的“目录扩展”选择页](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> 目前，不支持预配本地 AD 中的参考属性（如 managedby 或 DN/DistinguishedName）的功能 。 可以在 [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory) 上请求此功能。 


## <a name="next-steps"></a>后续步骤

* [定义谁在预配范围中](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
