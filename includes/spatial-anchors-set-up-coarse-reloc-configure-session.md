---
author: msftradford
ms.author: parkerra
ms.date: 11/20/2020
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 41c0a6292bf7369a30fbb1e4bd474e70ffaedcb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999720"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>配置云空间定位点会话

接下来，我们将负责配置云空间定位点会话。 在第一行，我们在会话上设置了传感器提供程序。 从现在开始，我们在会话期间创建的所有定位点都将与一组传感器读数相关联。 接下来，我们会实例化近设备定位条件，并将其初始化以符合应用程序的要求。 最后，通过从近设备条件创建观察程序来指示会话在查找定位点时使用传感器数据。