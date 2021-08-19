---
ms.openlocfilehash: fa43176a945a01651dc768558d14604d0a15b300
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113726818"
---
以下用于更新代码的步骤适用于所有机密客户端方案：

1. 在源代码中使用 MSAL.NET 命名空间：`using Microsoft.Identity.Client;`。
2. 使用 `ConfidentialClientApplicationBuilder.Create` 实例化 `IConfidentialClientApplication`，而不是实例化 `AuthenticationContext`。
3. MSAL.NET 使用作用域，而不是字符串 `resourceId`。 由于使用 ADAL.NET 的应用程序是预先授权的，因此可以始终使用以下作用域：`new string[] { $"{resourceId}/.default" }`。
4. 将对 `AuthenticationContext.AcquireTokenAsync` 的调用替换为对 `IConfidentialClientApplication.AcquireTokenXXX` 的调用，其中 XXX 取决于你的方案。
