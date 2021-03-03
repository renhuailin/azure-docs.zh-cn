---
title: 使用通信服务中的托管标识
titleSuffix: An Azure Communication Services quickstart
description: 通过托管标识，你可以从 Azure Vm 中运行的应用程序、函数应用和其他资源授权 Azure 通信服务访问。
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657602"
---
# <a name="use-managed-identities"></a>使用托管标识
通过使用托管标识开始使用 Azure 通信服务。 通信服务标识和 SMS 客户端库支持 Azure Active Directory (通过 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)Azure AD) 身份验证。

本快速入门介绍如何从支持托管标识的 Azure 环境中为标识和 SMS 客户端库授予访问权限。 还介绍了如何在开发环境中测试代码。

## <a name="prerequisites"></a>先决条件

 - 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free)
 - 活动的通信服务资源和连接字符串。 [创建通信服务资源](./create-communication-resource.md?pivots=platform-azp&tabs=windows)。

## <a name="setting-up"></a>设置

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>在虚拟机或应用服务上启用托管标识

应在你授权的 Azure 资源上启用托管标识。 若要了解如何为 Azure 资源启用托管标识，请参阅下述文章之一：

- [Azure 门户](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 模板](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure 资源管理器客户端库](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [应用服务](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>将 Azure 角色分配到 Azure 门户

1. 导航到 Azure 门户。
1. 导航到 Azure 通信服务资源。
1. 导航到访问控制 (IAM) 菜单-> + 添加-> 添加角色分配。
1. 选择 "参与者" 角色 (这是唯一受支持的角色) 。
1. 选择 "用户分配的托管标识" (或 "系统分配的托管标识" ) 然后选择所需的标识。 保存所选内容。

![托管标识角色](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>通过 PowerShell 分配 Azure 角色

若要使用 PowerShell 分配角色和权限，请参阅 [使用 Azure PowerShell 添加或删除 Azure 角色分配](../../../articles/role-based-access-control/role-assignments-powershell.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end