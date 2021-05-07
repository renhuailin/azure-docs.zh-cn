---
title: 使用 Azure CLI - Azure AD 查看托管标识的服务主体
description: 使用 Azure CLI 查看托管标识的服务主体的分步说明。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce3a35562bdef34f44ef1093a3196ea7afb0bd9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92892037"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-azure-cli"></a>使用 Azure CLI 查看托管标识的服务主体

Azure 资源的托管标识在 Azure Active Directory 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文将介绍如何使用 Azure CLI 查看托管标识的服务主体。

如果没有 Azure 帐户，请在继续前[注册免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

- 如果你不熟悉 Azure 资源托管标识，请参阅[什么是 Azure 资源托管标识？](overview.md)。

- 在[虚拟机](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity)或[应用程序](../../app-service/overview-managed-identity.md#add-a-system-assigned-identity)上启用系统分配的标识。

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="view-the-service-principal"></a>查看服务主体

以下命令演示如何查看启用了托管标识的 VM 或应用程序的服务主体。 将 `<Azure resource name>` 替换为自己的值。

```azurecli-interactive
az ad sp list --display-name <Azure resource name>
```

## <a name="next-steps"></a>后续步骤

有关使用 Azure CLI 管理 Azure AD 服务主体的详细信息，请参阅 [az ad sp](/cli/azure/ad/sp)。
