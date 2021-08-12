---
title: 使用 SDK 在 VM 上配置托管标识 - Azure AD
description: 分步说明如何使用 Azure SDK 在 Azure VM 上配置和使用 Azure 资源托管标识。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0102ea602cb6e6778940c77aec444d412fa4ed6d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114452637"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>使用 Azure SDK 为 VM 配置具有 Azure 资源托管标识

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Azure 资源的托管标识在 Azure Active Directory (AD) 中为 Azure 服务提供了一个自动托管标识。 此标识可用于通过支持 Azure AD 身份验证的任何服务的身份验证，这样就无需在代码中插入凭据了。 

本文介绍如何使用 Azure SDK 为 Azure VM 启用和删除 Azure 资源托管标识。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDK 包含 Azure 资源托管标识支持 

Azure 通过一系列 [Azure SDK](https://azure.microsoft.com/downloads) 支持多个编程平台。 其中多个平台已更新为支持 Azure 资源托管标识，并提供相应的示例来演示用法。 由于添加了其他支持，此列表已更新：

| SDK 中 IsInRole 中的声明 | 示例 |
| --- | ------ | 
| .NET   | [从具有已启用的 Azure 资源托管标识的已启用的 VM 中管理资源](https://github.com/Azure-Samples/aad-dotnet-manage-resources-from-vm-with-msi) |
| Java   | [从具有 Azure 资源托管标识的已启用的 VM 中管理存储](https://github.com/Azure-Samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group)|
| Node.js| [创建启用了系统分配托管标识的 VM](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [创建启用了系统分配托管标识的 VM](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [创建启用了系统分配托管标识的 Azure VM](https://github.com/Azure-Samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>后续步骤

- 请参阅“为 Azure VM 配置标识”下的相关文章，了解还可以如何使用 Azure 门户、PowerShell、CLI 和资源模板完成此操作  。
