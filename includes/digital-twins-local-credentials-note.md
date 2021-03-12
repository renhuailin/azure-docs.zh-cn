---
author: baanders
description: 用于 Azure 数字孪生示例中的 DefaultAzureCredential 的 include 文件 - 备注
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: bec2681c33108417aab1a33932c4f5f4d2ed730f
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/08/2021
ms.locfileid: "102473635"
---
>[!NOTE]
> 当你在本地计算机上运行示例时，此示例使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)（属于 `Azure.Identity` 库的一部分）对用户进行 Azure 数字孪生实例验证。 借助此类型的身份验证，该示例将在本地环境中搜索凭据，例如 [Azure CLI](/cli/azure/install-azure-cli) 或 Visual Studio/Visual Studio Code 中的登录名。
>
> 若要详细了解如何使用 `DefaultAzureCredential` 和其他验证选项，请查看[如何：编写应用验证码](../articles/digital-twins/how-to-authenticate-client.md)。