---
title: Azure 托管 HSM 访问控制
description: 管理 Azure 托管 HSM 和密钥的访问权限。 介绍托管 HSM 的身份验证和授权模型，讲解如何保护 HSM。
services: key-vault
author: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.openlocfilehash: 453e6ba2d7bb8cd4021c1b5a47faf60ef7ac1b37
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471282"
---
# <a name="managed-hsm-access-control"></a>托管 HSM 访问控制

> [!NOTE]
> Key Vault 资源提供程序支持两种资源类型：保管库和托管 HSM 。 本文中所述的访问控制仅适用于托管 HSM。 若要详细了解托管 HSM 的访问控制，请查看[使用 Azure 基于角色的访问控制提供对 Key Vault 密钥、证书和机密的访问权限](../general/rbac-guide.md)。

Azure Key Vault 托管 HSM 是一项保护加密密钥的云服务。 因为此数据是敏感数据和业务关键数据，所以需要保护对托管 HSM 的访问，只允许得到授权的应用程序和用户进行访问。 本文简要介绍了托管 HSM 访问控制模型。 其中介绍了身份验证和授权，以及如何保护对托管 HSM 的访问。

## <a name="access-control-model"></a>访问控制模型

通过以下两个接口来控制对托管 HSM 的访问：管理平面和数据平面 。 管理平面用于管理 HSM 本身。 此平面中的操作包括创建和删除托管 HSM 以及检索托管 HSM 属性。 数据平面用于处理托管 HSM 中存储的数据（即 HSM 支持的加密密钥）。 可添加、删除、修改和使用密钥来执行加密操作，管理角色分配以控制对密钥的访问权限，创建完整的 HSM 备份，还原完整备份以及从数据平面接口管理安全域。

若要在任一平面中访问托管 HSM，所有调用方都必须具有适当的身份验证和授权。 身份验证可确定调用方的身份。 授权可确定调用方能够执行的操作。 调用方可以是 Azure Active Directory 中定义的任何一种[安全主体](../../role-based-access-control/overview.md#security-principal) - 用户、组、服务主体或托管标识。

对于身份验证，这两个平面都使用 Azure Active Directory。 对于授权，它们使用不同的系统，如下所示
- 管理平面使用 Azure 基于角色的访问控制 (Azure RBAC)，这是基于 Azure 资源管理器构建的授权系统 
- 数据平面使用托管 HSM 级别的 RBAC（托管 HSM 本地 RBAC），这是在托管 HSM 级别实施并强制执行的授权系统。

创建托管 HSM 时，请求者还会提供数据平面管理员列表（所有[安全主体](../../role-based-access-control/overview.md#security-principal)都受支持）。 只有这些管理员能够访问托管 HSM 数据平面来执行关键操作并管理数据平面角色分配（托管 HSM 本地 RBAC）。

这两个平面的权限模型使用相同的语法，但它们是在不同的级别强制执行的，角色分配也使用不同的范围。 管理平面 Azure RBAC 由 Azure 资源管理器强制执行，而数据平面托管 HSM 本地 RBAC 由托管 HSM 本身强制执行。

> [!IMPORTANT]
> 如果向安全主体管理平面授予对托管 HSM 的访问权限，并不会向它们授予对数据平台的任何访问权限来访问密钥或数据平面角色分配（托管 HSM 本地 RBAC）。 这种隔离是为了防止特权无意扩展，从而影响到对托管 HSM 中存储的密钥的访问。

例如，订阅管理员（他们对订阅中的所有资源具有“参与者”权限）可删除其订阅中的托管 HSM，但如果他们没有通过托管 HSM 本地 RBAC 专门授予的数据平面访问权限，则无法访问密钥，也无法管理托管 HSM 中的角色分配来向其自己或其他人授予对数据平面的访问权限。

## <a name="azure-active-directory-authentication"></a>Azure Active Directory 身份验证

在 Azure 订阅中创建托管 HSM 时，它会自动与该订阅的 Azure Active Directory 租户相关联。 这两个平面中的所有调用方都必须在此租户中注册并进行身份验证，然后才能访问该托管 HSM。

在调用任一平面之前，应用程序会使用 Azure Active Directory 进行身份验证。 应用程序可根据应用程序类型使用任何[支持的身份验证方法](../../active-directory/develop/authentication-vs-authorization.md)。 应用程序通过获取平面中资源的令牌来获取访问权限。 资源是管理平面或数据平面中基于 Azure 环境的终结点。 应用程序使用该令牌并向托管 HSM 终结点发送 REST API 请求。 若要了解详细信息，请查看[整个身份验证流](../../active-directory/develop/v2-oauth2-auth-code-flow.md)。

对这两种平面使用单一身份验证机制具有多个优点：

- 组织可集中控制对其组织中的所有托管 HSM 的访问。
- 离职的用户会立即失去对组织中所有托管 HSM 的访问权限。
- 组织可通过 Azure Active Directory 中的选项自定义身份验证（例如启用多重身份验证来提高安全性）。

## <a name="resource-endpoints"></a>资源终结点

安全主体通过终结点访问平面。 两个平面的访问控制独立运行。 若要向应用程序授予访问权限来使用托管 HSM 中的密钥，请使用托管 HSM 本地 RBAC 授予数据平面访问权限。 若要向用户授予访问托管 HSM 资源的权限来创建、读取、删除、移动托管 HSM 并编辑其他属性和标记，请使用 Azure RBAC。

下表显示了用于管理平面和数据平面的终结点。

| 访问&nbsp;平面 | 访问终结点 | 操作 | 访问控制机制 |
| --- | --- | --- | --- |
| 管理平面 | **全球：**<br> management.azure.com:443<br> | 创建、读取、更新、删除和移动托管 HSM<br>设置托管 HSM 标记 | Azure RBAC |
| 数据平面 | **全球：**<br> &lt;hsm-name&gt;.managedhsm.azure.net:443<br> | **密钥**：解密、加密、<br> 解包、包装、验证、签名、获取、列出、更新、创建、导入、删除、备份、还原、清除<br/><br/> **数据平面角色管理（托管 HSM 本地 RBAC）** _：列出角色定义、分配角色、删除角色分配、定义自定义角色<br/><br/>_ *备份/还原 **：备份、还原、检查状态备份/还原操作<br/><br/>** 安全域**：下载和上传安全域 | 托管 HSM 本地 RBAC |
|||||

## <a name="management-plane-and-azure-rbac"></a>管理平面和 Azure RBAC

在管理平面中，使用 Azure RBAC 对调用方可执行的操作进行授权。 在 Azure RBAC 模型中，每个 Azure 订阅都有一个 Azure Active Directory 实例。 可以从此目录向用户、组和应用程序授予访问权限。 授予访问权限以管理 Azure 订阅中使用 Azure 资源管理器部署模型的资源。 若要授予访问权限，请使用 [Azure 门户](https://portal.azure.com/)、[Azure CLI](/cli/azure/install-classic-cli)、[Azure PowerShell](/powershell/azureps-cmdlets-docs) 或 [Azure 资源管理器 REST API](/rest/api/authorization/roleassignments)。

可在资源组中创建密钥保管库，并使用 Azure Active Directory 管理访问权限。 授予用户或组管理资源组中的密钥保管库的权限。 可通过分配适当的 Azure 角色在特定范围级别授予访问权限。 若要授予用户管理密钥保管库的访问权限，请为特定范围的用户分配预定义的 `key vault Contributor` 角色。 可以将以下范围级别分配给 Azure 角色：

- **管理组**：在订阅级别分配的 Azure 角色适用于该管理组的所有订阅。
- **订阅**：在订阅级别分配的 Azure 角色适用于该订阅中的所有资源组和资源。
- **资源组**：在资源组级别分配的 Azure 角色适用于该资源组中的所有资源。
- **特定资源**：为特定资源分配的 Azure 角色适用于该资源。 在这种情况下，资源是特定的密钥保管库。

有多种预定义角色。 如果预定义角色不符合需求，可以定义自己的角色。 有关详细信息，请参阅 [Azure RBAC：内置角色](../../role-based-access-control/built-in-roles.md)。

## <a name="data-plane-and-managed-hsm-local-rbac"></a>数据平面和托管 HSM 本地 RBAC

可通过分配角色向安全主体授予访问权限来执行特定密钥操作。 对于每个角色分配，都需要指定应用该分配的角色和范围。 对于托管 HSM 本地 RBAC，有两个范围可用。

- **“/”或“/keys”** ：HSM 级别范围。 在此范围内分配了角色的安全主体可对托管 HSM 中的所有对象（密钥）执行该角色中定义的操作。
- **“/keys/&lt;key-name&gt;”** ：密钥级别范围。 在此范围内分配了角色的安全主体可以仅对指定密钥的所有版本执行此角色中定义的操作。

## <a name="next-steps"></a>后续步骤

- 有关面向管理员的入门教程，请参阅[什么是托管 HSM？](overview.md)
- 有关角色管理教程，请查看[托管 HSM 本地 RBAC](role-management.md)
- 若要详细了解托管 HSM 日志记录的使用情况日志记录，请参阅[托管 HSM 日志记录](logging.md)