---
title: 创建 HCX 网络扩展
description: 了解如何将任何网络从本地环境扩展到 Azure VMware 解决方案。
ms.topic: how-to
ms.date: 09/07/2021
ms.openlocfilehash: 81e984975712e76033102c030093fc94abb2796f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129456188"
---
# <a name="create-a-hcx-network-extension"></a>创建 HCX 网络扩展

这是一个可选步骤，用于将任何网络从本地环境扩展到 Azure VMware 解决方案。

1. 在“服务”下，选择“网络扩展” > “创建网络扩展”  。

   :::image type="content" source="media/tutorial-vmware-hcx/create-network-extension.png" alt-text="显示用于开始创建网络扩展的选项的屏幕截图。" lightbox="media/tutorial-vmware-hcx/create-network-extension.png":::

1. 选择要将其扩展到 Azure VMware 解决方案的每个网络，然后选择“下一步”。

   :::image type="content" source="media/tutorial-vmware-hcx/select-extend-networks.png" alt-text="显示选择网络的屏幕截图。":::

1. 输入要扩展的每个网络的本地网关 IP，然后选择“提交”。

   :::image type="content" source="media/tutorial-vmware-hcx/extend-networks-gateway.png" alt-text="显示网关 IP 地址条目的屏幕截图。":::

   只需几分钟时间即可完成网络扩展。 然后，你会看到状态更改为“扩展完成”。

   :::image type="content" source="media/tutorial-vmware-hcx/extension-complete.png" alt-text="显示“扩展完成”状态的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

配置 HCX 网络扩展之后，还可以了解以下内容：

- [VMware HCX 移动优化网络 (MON) 指南](vmware-hcx-mon-guidance.md)
