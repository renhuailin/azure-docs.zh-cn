---
title: 更新服务主体凭据
description: 更新服务主体的凭据
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 01d8aa850c5f816026561ca64541724bd8c3b832
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738586"
---
# <a name="update-service-principal-credentials"></a>更新服务主体凭据

当服务主体凭据更改时，你需要更新数据控制器中的机密。

例如，如果你使用服务主体租户 ID、客户端 ID 和客户端机密的一组特定值部署了数据控制器，然后更改了其中的一个或多个值，则需要更新数据控制器中的机密。  下面是有关更新租户 ID、客户端 ID 或客户端机密的说明。 


## <a name="background"></a>背景

已根据[创建服务主体](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)中所述创建了服务主体。 

## <a name="steps"></a>步骤

1. 在默认编辑器中访问服务主体机密。

   ```console
   kubectl edit secret/upload-service-principal-secret -n <name of namespace>
   ```

   例如，若要在 `arc` 命名空间中编辑数据控制器的服务主体机密，请运行以下命令：

   ```console
   kubectl edit secret/upload-service-principal-secret -n arc
   ```

   `kubecl edit` 命令在默认编辑器中打开凭据 .yml 文件。 


1. 编辑服务主体机密。 

   在默认编辑器中，将 data 节中的值替换为已更新的凭据信息。

   例如：

   ```console
   # Please edit the object below. Lines beginning with a '#' will be ignored,
   # and an empty file will abort the edit. If an error occurs while saving this file will be
   # reopened with the relevant failures.
   #
   apiVersion: v1
   data:
     authority: aHR0cHM6Ly9sb2dpbi5taWNyb3NvZnRvbmxpbmUuY29t
     clientId: NDNiNDcwYrFTGWYzOC00ODhkLTk0ZDYtNTc0MTdkN2YxM2Uw
     clientSecret: VFA2RH125XU2MF9+VVhXenZTZVdLdECXFlNKZi00Lm9NSw==
     tenantId: NzJmOTg4YmYtODZmMRFVBGTJLSATkxYWItMmQ3Y2QwMTFkYjQ3
   kind: Secret
   metadata:
     creationTimestamp: "2020-12-02T05:02:04Z"
     name: upload-service-principal-secret
     namespace: arc
     resourceVersion: "7235659"
     selfLink: /api/v1/namespaces/arc/secrets/upload-service-principal-secret
     uid: 7fb693ff-6caa-4a31-b83e-9bf22be4c112
   type: Opaque
   ```

   根据需要编辑 `clientID`、`clientSecret` 和/或 `tenantID` 的值。 

> [!NOTE]
>值需要采用 base64 编码。 不要编辑任何其他属性。

如果为 `clientId`、`clientSecret` 或 `tenantID` 提供了错误的值，则 `control-xxxx` Pod/控制器容器日志中会出现如下所示的错误消息：

```output
YYYY-MM-DD HH:MM:SS.mmmm | ERROR | [AzureUpload] Upload task exception: A configuration issue is preventing authentication - check the error message from the server for details.You can modify the configuration in the application registration portal. See https://aka.ms/msal-net-invalid-client for details.  Original exception: AADSTS7000215: Invalid client secret is provided.
```



## <a name="next-steps"></a>后续步骤

[检索用于连接到 Arc 数据控制器的用户名和密码](retrieve-the-username-password-for-data-controller.md)

[创建服务主体](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)
