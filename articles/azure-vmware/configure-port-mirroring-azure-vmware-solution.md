---
title: 为 Azure VMware 解决方案配置端口镜像
description: 了解如何配置端口镜像来监视网络流量，其中包括将每个数据包的副本从一个网络交换机端口转发到另一个端口。
ms.topic: how-to
ms.custom: contperf-fy22q1
ms.date: 07/16/2021
ms.openlocfilehash: 3f1b205cc75911132abeed83d1e52e3566919551
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122322734"
---
# <a name="configure-port-mirroring-in-the-azure-portal"></a>在 Azure 门户中配置端口镜像

部署 Azure VMware 解决方案后，可从 Azure 门户配置端口镜像。 端口镜像将协议分析器置于接收镜像数据的端口上。 它会分析来自某个源、某个虚拟机 (VM) 或一组 VM 的流量，然后将流量发送到定义的目标。 

在此操作指南中，你将配置端口镜像来监视网络流量，其中包括将每个数据包的副本从一个网络交换机端口转发到另一个端口。 

## <a name="prerequisites"></a>先决条件

有权访问 vCenter 和 NSX-T Manager 界面的 Azure VMware 解决方案私有云。 有关详细信息，请参阅[配置网络](tutorial-configure-networking.md)教程。

## <a name="create-the-vms-or-vm-groups"></a>创建 VM 或 VM 组

你将创建源和目标 VM 或 VM 组。 源组包含一个或多个有镜像流量的 VM。

1. 在 Azure VMware 解决方案私有云的“工作负荷网络”下，选择“端口镜像” > “VM 组” > “添加”。

   :::image type="content" source="media/networking/add-port-mirroring-vm-groups.png" alt-text="显示如何为端口镜像创建 VM 组的屏幕截图。":::

1. 为新 VM 组提供名称，从列表中选择 VM，然后单击“确定”。

1. 重复上述步骤来创建目标 VM 组。

   >[!NOTE]
   >在创建端口镜像配置文件之前，请确保同时创建了源和目标 VM 组。

## <a name="create-a-port-mirroring-profile"></a>创建端口镜像配置文件。

你将创建为源和目标 VM 组定义流量方向的端口镜像配置文件。

1. 选择“端口镜像” > “端口镜像” > “添加”，然后提供以下信息  ：

   :::image type="content" source="media/networking/add-port-mirroring-profile.png" alt-text="显示端口镜像配置文件所需信息的屏幕截图。":::

   - “端口镜像名称” - 配置文件的描述性名称。

   - “方向” - 从入口、出口或双向中进行选择。

   - “源” - 选择源 VM 组。

   - “目标” - 选择目标 VM 组。

   - “说明” - 输入端口镜像的说明。

1. 选择“确定”，以完成配置文件。 

   配置文件和 VM 组在 Azure VMware 解决方案控制台中可见。
