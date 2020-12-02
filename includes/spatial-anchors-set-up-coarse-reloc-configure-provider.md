---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 00ae753b0be2631b13a148a4f34a16793000848d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999721"
---
## <a name="configure-the-sensor-fingerprint-provider"></a>配置传感器指纹提供程序

首先，我们将创建和配置传感器指纹提供程序。 传感器指纹提供程序将负责读取设备上特定于平台的传感器，并将其读数转换为云空间定位点会话使用的通用表示形式。