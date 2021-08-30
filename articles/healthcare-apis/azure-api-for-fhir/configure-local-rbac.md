---
title: 为 Azure API for FHIR 配置本地基于角色的访问控制（本地 RBAC）
description: 本文介绍如何配置 Azure API for FHIR 配置以将外部 Azure Active Directory 租户用于数据平面
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: zxue
ms.openlocfilehash: 1b081f230f562a333bbc014fb4583cc37a556d50
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778245"
---
# <a name="configure-local-rbac-for-fhir"></a>为 FHIR 配置本地 RBAC 

本文介绍如何将 Azure API for FHIR 配置为使用外部的辅助 Azure Active Directory 租户来管理数据平面访问。 仅在不可能使用与订阅相关联的 Azure Active Directory 租户时才使用此模式。

> [!NOTE]
> 如果你的 FHIR 服务数据平面配置为使用与订阅相关联的主要 Azure Active Directory 租户，请[使用 Azure RBAC 分配数据平面角色](configure-azure-rbac.md)。

## <a name="add-service-principal"></a>添加服务主体

本地 RBAC 允许将外部 Azure Active Directory 租户用于 FHIR 服务器。 为了允许本地 RBAC 系统检查此租户中的组成员身份，Azure API for FHIR 必须在租户中有一个服务主体。 此服务主体将会在与已部署 Azure API for FHIR 的订阅绑定的租户中自动创建，但如果你的租户没有与之绑定的订阅，租户管理员将需要使用以下命令之一创建此服务主体：

使用 `Az` PowerShell 模块：

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

或者可使用 `AzureAd` PowerShell 模块：

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

也可以使用 Azure CLI：

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>配置本地 RBAC

可在“身份验证”边栏选项卡中将 Azure API for FHIR 配置为使用外部或辅助 Azure Active Directory 租户。

![本地 RBAC 分配](media/rbac/local-rbac-guids.png).

在“颁发机构”框中，输入有效的 Azure Active Directory 租户。 验证租户后，应激活“允许的对象 ID”框，然后可以输入一系列标识对象 ID。 这些 ID 可以是以下各项的标识对象 ID：

* Azure Active Directory 用户。
* 一个 Azure Active Directory 服务主体。
* Azure Active Directory 安全组。

有关更多详细信息，请阅读有关如何[查找标识对象 ID](find-identity-object-ids.md) 的文章。

输入所需的对象 ID 后，单击“保存”并等待更改被保存，然后再尝试使用分配的用户、服务主体或组访问数据平面。

## <a name="caching-behavior"></a>缓存行为

Azure API for FHIR 将决策最多缓存 5 分钟。 如果通过将用户添加到允许的对象 ID 列表来授予用户对 FHIR 服务器的访问权限，或者将用户从列表中删除，则传播权限更改最多需要五分钟。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用外部（辅助）Azure Active Directory 租户来分配 FHIR 数据平面访问权限。 接下来，了解 Azure API for FHIR 的其他设置：
 
>[!div class="nextstepaction"]
>[Azure API for FHIR 的其他设置](azure-api-for-fhir-additional-settings.md)
