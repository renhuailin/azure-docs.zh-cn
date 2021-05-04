---
title: 如何关闭 Microsoft Azure FXT Edge Filer 设备
description: 了解使用群集控制面板软件启动和安全关闭 Azure FXT Edge Filer 节点的过程。
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 01c34304ac0e3e7faa42611758d77893e149a2f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92218725"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>如何安全地关闭 Azure FXT Edge Filer 硬件

尽管可以使用物理电源按钮打开单个节点，但在正常情况下，不应使用它来关闭设备。

将 Azure FXT Edge Filer 节点用作群集的一部分后，应使用群集控制面板软件来关闭硬件。

> [!NOTE]
> 为了避免可能的数据丢失或损坏，请始终使用控制面板软件关闭 Azure FXT Edge Filer。 请勿使用物理电源按钮关机，除非 Microsoft 客户服务和支持部门指示你这样做。
>
> 发生电气故障时，请拔下电源线或使用数据中心的断电机制。

## <a name="shut-down-a-node-from-the-control-panel"></a>从控制面板关闭节点

按照以下说明安全关闭 Azure FXT Edge Filer 节点：

1. 登录到群集控制面板。 （[打开“设置”页](fxt-cluster-create.md#open-the-settings-pages)中的说明）
1. 单击“设置”选项卡，然后加载“群集” > “FXT 节点”页。
1. 在群集节点列表中，找到要关闭的节点。 单击其“操作”列中的“关闭”按钮。
1. 等待几分钟。 该节点会关闭并自行断电。

## <a name="next-steps"></a>后续步骤

* 在[监视 Azure FXT Edge Filer 硬件状态](fxt-monitor.md)中了解状态 LED 和其他指示器。
* 在[连接电源线](fxt-network-power.md#connect-power-cables)中了解有关 Azure FXT Edge Filer 电源的更多信息。
