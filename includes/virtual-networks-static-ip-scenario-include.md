---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 02/14/2020
ms.author: genli
ms.openlocfilehash: 280943bd965c4799ce294321129d1088be9c0caf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015906"
---
## <a name="scenario"></a>方案

为了更好地说明如何为 VM 配置静态 IP 地址，本文档使用这一方案：

![虚拟网络方案：前端和后端子网，前端子网具有静态 IP 地址](./media/virtual-networks-static-ip-scenario-include/static-ip-scenario.png)

在此方案中，在 FrontEnd 子网中创建一个名为 DNS01 的 VM，然后将它设置为使用静态 IP 地址 192.168.1.101  。
