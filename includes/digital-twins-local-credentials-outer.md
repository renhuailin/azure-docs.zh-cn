---
author: baanders
description: 有关在 Azure 数字孪生示例中为 DefaultAzureCredential 设置本地身份验证的 include 文件 - 带简介
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: eacabf431d396fa042bf4dd60d3b32c688df46bf
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110065111"
---
### <a name="set-up-local-azure-credentials"></a>设置本地 Azure 凭据

当你在本地计算机上运行示例时，此示例使用 [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet&preserve-view=true)（属于 `Azure.Identity` 库的一部分）对用户进行 Azure 数字孪生实例验证。 若要详细了解客户端应用可向 Azure 数字孪生进行验证的不同方法，请参阅操作说明：编写应用身份验证码。

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]