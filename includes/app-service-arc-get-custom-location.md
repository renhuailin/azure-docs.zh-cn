---
title: 包含文件
description: 包含文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: a20a4a4b3c618a03ba7b5f788395bb541d2c143d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371802"
---
从群集管理员处获取有关自定义位置的以下信息（请参阅[创建自定义位置](../articles/app-service/manage-create-arc-environment.md#create-a-custom-location)）。

```azurecli-interactive
customLocationGroup="<resource-group-containing-custom-location>"
customLocationName=<name-of-custom-location>
```

获取自定义位置 ID，供下一步使用。

```azurecli-interactive
customLocationId=$(az customlocation show \
    --resource-group $customLocationGroup \
    --name $customLocationName \
    --query id \
    --output tsv)
```