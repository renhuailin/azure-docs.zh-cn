---
title: 虚拟机概述
titleSuffix: Azure VMware Solution by CloudSimple
description: 了解 CloudSimple 虚拟机及其优点。 可以从 Azure 门户中管理 VMware 虚拟机。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6f9be035978667287b8b88ec1bb64b3882b0f929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "88141970"
---
# <a name="cloudsimple-virtual-machines-overview"></a>CloudSimple 虚拟机概述

通过 CloudSimple，可以从 Azure 门户管理 VMware 虚拟机 (VM)。  你的vSphere 群集中的群集或资源池由 Azure 通过将其映射到你的订阅进行管理。

若要从 Azure 创建 CloudSimple VM，你的私有云 vCenter 上必须存在一个 VM 模板。  模板用于自定义操作系统和应用程序。  可以强制模板 VM 满足企业安全策略要求。  可以使用模板创建 VM，然后使用自助服务模型在 Azure 门户中使用创建的 VM。

## <a name="benefits"></a>优点

Azure 门户中的 CloudSimple 虚拟机为用户提供自助服务机制，以创建和管理 VMware 虚拟机。

* 在私有云 vCenter 上创建 CloudSimple VM
* 管理 VM 属性
  * 添加/移除磁盘
  * 添加/移除 NIC
* CloudSimple VM 的电源操作
  * 开机和关机
  * 重置 VM
* 删除 VM

## <a name="next-steps"></a>后续步骤

* 了解如何[使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* 了解如何 [映射你的 Azure 订阅](azure-subscription-mapping.md)
