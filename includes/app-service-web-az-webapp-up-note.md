---
title: include 文件
description: include 文件
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/14/2021
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: afaacb79906c07893d835d654b00815ea97199d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577312"
---
> [!NOTE]
> `az webapp up` 命令执行以下操作：
>
>- 创建一个默认的[资源组](/cli/azure/group#az_group_create)。
>
>- 创建一个默认的[应用服务计划](/cli/azure/appservice/plan#az_appservice_plan_create)。
>
>- 使用指定名称[创建应用](/cli/azure/webapp#az_webapp_create)。
>
>- 对当前工作目录中的所有文件进行 [zip 部署](../articles/app-service/deploy-zip.md#deploy-a-zip-package)，并[启用生成自动化](../articles/app-service/deploy-zip.md#enable-build-automation-for-zip-deploy)。
>
>- 将参数本地缓存在 .azure/config 文件中，使得以后使用 `az webapp up` 或其他 Azure CLI 命令部署时无需再次指定它们。 默认情况下，自动使用缓存的值。
>