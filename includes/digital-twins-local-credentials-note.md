---
author: baanders
description: Azure 数字孪生示例中的 DefaultAzureCredential 包含文件-注意
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8aa3cbb2a037e49a694192c9852eeae0215c089c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211770"
---
>[!NOTE]
> 当你在本地计算机上运行示例时，此示例使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet)（属于 `Azure.Identity` 库的一部分）对用户进行 Azure 数字孪生实例验证。 使用这种类型的身份验证，该示例将在本地环境中搜索 Azure 凭据，例如本地 [Azure CLI](/cli/azure/install-azure-cli) 或 Visual Studio/Visual Studio Code 中的登录。
>
> 有关使用 `DefaultAzureCredential` 和其他身份验证选项的详细信息，请参阅 [*操作方法：编写应用身份验证代码*](../articles/digital-twins/how-to-authenticate-client.md)。