---
title: 包含文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 07/13/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 766e135cbccf86884786c2b888fcb1ea9d98ecc7
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/13/2021
ms.locfileid: "113688613"
---
若要在应用服务中从应用程序代码内部访问生成的控制台日志，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令，打开诊断日志记录：

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --docker-container-logging filesystem --level Verbose
```

`--level` 的可能值为：`Error`、`Warning`、`Info` 和 `Verbose`。 每个后续级别包括上一个级别。 例如：`Error` 仅包含错误消息，`Verbose` 则包含所有消息。

启用诊断日志记录功能以后，请运行以下命令来查看日志流：

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

如果没有立即看到控制台日志，请在 30 秒后重新查看。

> [!NOTE]
> 也可通过浏览器在 `https://<app-name>.scm.azurewebsites.net/api/logs/docker` 中检查日志文件。

若要随时停止日志流式处理，请键入 `Ctrl`+`C`。
