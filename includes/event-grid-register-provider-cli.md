---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 08/17/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: cd778da5fd53cb8744a9f267384fcc8e11941582
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645344"
---
## <a name="enable-the-event-grid-resource-provider"></a>启用事件网格资源提供程序

如果以前未在 Azure 订阅中使用过事件网格，则可能需要注册事件网格资源提供程序。 运行以下命令，注册提供程序：

```azurecli-interactive
az provider register --namespace Microsoft.EventGrid
```

完成注册可能需要一些时间。 若要检查状态，请运行：

```azurecli-interactive
az provider show --namespace Microsoft.EventGrid --query "registrationState"
```

当 `registrationState` 为 `Registered` 后，即可继续。
