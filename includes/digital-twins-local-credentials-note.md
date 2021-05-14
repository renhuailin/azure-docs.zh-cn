---
author: baanders
description: 用于 Azure 数字孪生示例中的 DefaultAzureCredential 的 include 文件 - 备注
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 462098536ae4598c7875a1b8291e477f6b4a8d7d
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108778357"
---
>[!NOTE]
> 当你在本地计算机上运行示例时，此示例使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential)（属于 `Azure.Identity` 库的一部分）对用户进行 Azure 数字孪生实例验证。 借助此类型的身份验证，该示例将在本地环境中搜索凭据，例如 [Azure CLI](/cli/azure/install-azure-cli) 或 Visual Studio/Visual Studio Code 中的登录名。
>
> 若要详细了解如何使用 `DefaultAzureCredential` 和其他验证选项，请查看如何：编写应用验证码。