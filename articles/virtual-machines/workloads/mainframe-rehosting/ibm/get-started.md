---
title: Azure 上的 IBM 工作负载 | Microsoft Docs
description: 使用大型机模拟器和 Microsoft 合作伙伴提供的其他服务，通过 Microsoft Azure 重新托管你的 IBM z/OS 工作负载。
services: virtual-machines
ms.service: virtual-machines
ms.subservice: workloads
documentationcenter: ''
author: njray
ms.author: edprice
manager: edprice
editor: edprice
ms.topic: conceptual
ms.date: 02/22/2019
tags: ''
keywords: ''
ms.openlocfilehash: 1bc8dcbf05847c4d1ce6dd6a39af7bd3674a0669
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561596"
---
# <a name="ibm-workloads-on-azure"></a>Azure 上的 IBM 工作负载

许多基于 z/OS 的 IBM 大型机工作负载都可以在 Azure 中进行复制，而不会丢失任何功能，用户甚至不会注意到其基础系统的变化。 在 Azure 上重新托管应用程序可为你提供所需的类似大型机的功能，以及云的弹性、可用性和潜在成本节约。

Azure 支持与现有的 IBM 大型机环境集成，使你能够迁移有意义的应用程序，在需要时运行混合解决方案，并随着时间的推移进行迁移。 虽然你可以完全为 Azure 重写现有的基于大型机的程序，但更常见的做法是重新托管它们。 重写会增加迁移项目的成本和复杂性，同时也会耗费时间。 通过重新托管，你可以：

- 将应用程序移动到基于云的模拟器。

- 将数据库迁移到基于云的数据库。

- 使用代码转换引擎替换模块和代码。

另外，Azure 市场还推出了 IBM 软件（包括 WebSphere 和 MQ）。 借助 IBM 软件的许可证，你可以利用 Azure 提供的按需基础结构缩放来快速启动虚拟机。

可以利用广阔的合作伙伴生态系统将 IBM 大型机系统迁移到 Azure。 大多数人在开始重写或替换应用程序的分阶段部署之前，应尽可能遵循实用的重用方法。 如需获得更多合作伙伴的指导和帮助，请访问 [Azure 大型机迁移中心](https://azure.microsoft.com/migration/mainframe/)。

后续步骤

- [大型机迁移：谬论和事实](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/myths-and-facts)
- [在 Azure 上安装 IBM zD&T 开发/测试环境](./install-ibm-z-environment.md)
- [在 IBM zD&T v1 中设置应用程序开发人员控制的分发 (ADCD)](./demo.md)
- [Azure 上的 IBM DB2 pureScale](ibm-db2-purescale-azure.md)
