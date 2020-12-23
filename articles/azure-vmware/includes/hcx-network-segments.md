---
title: VMware HCX 网段
description: VMware HCX 需要四个网络。
ms.topic: include
ms.date: 11/23/2020
ms.openlocfilehash: 48894c532c97b70cde1473fb8b81f406ded70343
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999616"
---
<!-- Used in avs-production-ready-deployment.md and tutorial-deploy-vmware-hcx.md -->

VMware HCX 需要四个网络：

- **管理网络：** 通常，它与 vSphere 群集上使用的管理网络相同。 至少在此网段上为 VMware HCX 标识两个 IP。 根据你的部署，可能需要更多数量。

   > [!NOTE]
   > 建议方法是创建一个 /26 网络。 在 /26 网络上，最多可以使用 10 个服务网格和 60 个网络扩展程序控件（每个服务网格 1 个网络扩展程序控件）。 可以使用 Azure VMware 解决方案私有云为每个网络扩展程序控件增加八个网络。
   >
   
- **vMotion 网络：** 通常，它与 vSphere 群集上用于 vMotion 的网络相同。  至少在此网段上为 VMware HCX 标识两个 IP。 根据你的部署，可能需要更多数量。  

   必须在分布式虚拟交换机或 vSwitch0 上公开 vMotion 网络， 否则请修改环境。

   > [!NOTE]
   > 此网络可以是（未路由的）专用网络。

- **上行网络：** 需要为 VMware HCX 上行创建一个新网络，并通过端口组将其扩展到 vSphere 群集。 至少在此网段上为 VMware HCX 标识两个 IP。 根据你的部署，可能需要更多数量。  

   > [!NOTE]
   > 建议方法是创建一个 /26 网络。 在 /26 网络上，最多可以使用 10 个服务网格和 60 个网络扩展程序控件（每个服务网格 1 个网络扩展程序控件）。 可以使用 Azure VMware 解决方案私有云为每个网络扩展程序控件增加八个网络。
   >
   
- **复制网络：** 此为可选项。 需要为 VMware HCX 复制创建一个新网络，并通过端口组将该网络扩展到 vSphere 群集。 至少在此网段上为 VMware HCX 标识两个 IP。 根据你的部署，可能需要更多数量。

   > [!NOTE]
   > 仅当本地群集主机使用专用的复制 VMkernel 网络时，才可以进行此配置。  如果本地群集没有定义专用的复制 VMkernel 网络，则无需创建此网络。
