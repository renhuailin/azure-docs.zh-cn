---
title: VMware HCX 网段
description: VMware HCX 需要四个网络。
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: e9b37c125db82a95c137ede8d642888fba8b6c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "103622288"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

可通过不同的方式在本地配置 VMware HCX 网段。 下面概述了支持试点或小规模生产用例的简单配置。  设计数百甚至数千个工作负载时，可能需要根据迁移需求更改此配置。  

在准备 VMware HCX 部署以支持试点或小规模生产用例时，请确定以下几点：

- **管理网络：** 在本地部署 VMware HCX 时，需要定义一个管理网络。  通常，该网络是本地 VMware 群集使用的同一管理网络。  最起码需要在此网段中确定 **两个** 用于 VMware HCX 的 IP。 所需的 IP 数目可能更多，具体取决于不包括试点或小规模用例在内的部署规模。

> [!NOTE]
   > 建议的方法是创建一个 /26 网络，因为最多可以使用 10 个服务网格和 60 个扩展器（每使用一个服务网格，扩展器数目需 -1）。 可以使用 Azure VMware 解决方案私有云通过每个网络扩展器扩增 **八个** 网络。
   >
   
- **vMotion 网络：** 在本地部署 VMware HCX 时，需要定义一个 vMotion 网络。  通常，该网络是本地 VMware 群集用于 vMotion 的同一网络。  最起码需要在此网段中确定 **两个** 用于 VMware HCX 的 IP。 所需的 IP 数目可能更多，具体取决于不包括试点或小规模用例在内的部署规模。

   必须在分布式虚拟交换机或 vSwitch0 上公开 vMotion 网络， 否则请修改环境以适应配置。

   > [!NOTE]
   > 许多 VMware 环境对 vMotion 使用非路由网段，这不会造成问题。

- **上行网络：** 在本地部署 VMware HCX 时，需要定义一个上行网络。 使用定义为上行网络的管理网络。
   
- **复制网络：** 在本地部署 VMware HCX 时，需要定义一个复制网络。 使用定义为复制网络的管理网络。  如果本地群集主机使用专用的复制 VMkernel 网络，请在此网段中保留 **两个** IP 地址，并将复制 VMkernel 网络用作复制网络。
