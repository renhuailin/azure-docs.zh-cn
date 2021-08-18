---
title: 检索用于连接到 Arc 数据控制器的用户名和密码
description: 检索用于连接到 Arc 数据控制器的用户名和密码
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 89ba0139409a1ceef37a773403c0b7623f6c6f14
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738600"
---
# <a name="retrieve-the-user-name-and-password-to-connect-to-the-arc-data-controller"></a>检索用于连接到 Arc 数据控制器的用户名和密码

有时，你可能需要检索数据控制器的用户名和密码。 遇到这种情况时，需使用以下命令。 

如果你是群集的 Kubernetes 管理员， 那么你便拥有了运行命令以从 Kubernetes 机密存储中检索 Azure Arc 保存的信息的特权。

> [!NOTE]
>  如果你在创建数据控制器的命名空间中使用了不同的名称，请务必更改以下命令中的 `-n arc` 参数，以使用创建了数据控制器的命名空间的名称。


## <a name="linux"></a>Linux

运行以下命令可检索用户名：

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}" | base64 -d
```

运行以下命令可检索密码：

```console
kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}" | base64 -d
```

## <a name="powershell"></a>PowerShell

运行以下命令可检索用户名：

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['username']}")))
```

运行以下命令可检索密码：

```console
[Text.Encoding]::Utf8.GetString([Convert]::FromBase64String((kubectl -n arc get secret controller-login-secret -o=jsonpath="{.data['password']}")))
```

## <a name="next-steps"></a>后续步骤

尝试其他[方案](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory-domain-services/scenarios.md)
