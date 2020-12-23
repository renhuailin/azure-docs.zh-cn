---
author: baanders
description: Azure 数字孪生示例中的 DefaultAzureCredential 包含文件-注意
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494467"
---
>[!NOTE]
> 此 [示例使用](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) 库)  (的一部分 `Azure.Identity` ，在本地计算机上运行时，使用 Azure 数字孪生实例对用户进行身份验证。 使用这种类型的身份验证，该示例将在本地环境中搜索 Azure 凭据，例如本地 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) 或 Visual Studio/Visual Studio Code 中的登录。
>
> 有关使用 `DefaultAzureCredential` 和其他身份验证选项的详细信息，请参阅 [*操作方法：编写应用身份验证代码*](../articles/digital-twins/how-to-authenticate-client.md)。