---
title: include 文件
description: include 文件
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 07/05/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: a2f5264db1f95bcea524a87a61735cf730af23ba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645333"
---
## <a name="enable-event-grid-resource-provider"></a>启用事件网格资源提供程序

如果以前未在 Azure 订阅中使用过事件网格，则可能需要注册事件网格资源提供程序。 运行下面的命令：

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

完成注册可能需要一些时间。 若要检查状态，请运行：

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.EventGrid
```

当 `RegistrationStatus` 为 `Registered` 后，即可继续。
