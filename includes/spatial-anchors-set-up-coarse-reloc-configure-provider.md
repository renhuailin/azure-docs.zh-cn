---
author: msftradford
ms.author: parkerra
ms.date: 01/28/2021
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 386c2f8a7cc32cf65381d9d62e2e6940754e3606
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214104"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>配置传感器指纹提供程序

首先，我们将创建和配置传感器指纹提供程序。 传感器指纹提供程序将负责读取设备上特定于平台的传感器，并将其读数转换为云空间定位点会话使用的通用表示形式。

> [!IMPORTANT]
> [请确保在此检查](../articles/spatial-anchors/concepts/coarse-reloc.md#platform-availability)你要启用的传感器在你的平台上是否可用。