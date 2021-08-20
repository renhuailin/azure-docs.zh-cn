---
author: cherylmc
ms.author: cherylmc
ms.date: 06/29/2021
ms.service: bastion
ms.topic: include
ms.openlocfilehash: f0585ca5bc2a76cb7f969f8a599ee98152b872cf
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108220"
---
使用 Azure Bastion 连接到一个 VM 时，无需 VM 的公共 IP 地址。 如果不将公共 IP 地址用于任何其他内容，可以取消与 VM 的关联。 若要解除 VM 的公共 IP 地址关联，请使用以下步骤：

1. 导航到你的虚拟机，然后选择“网络”。 选择“NIC 公共 IP”，打开“公共 IP 地址”页。

   :::image type="content" source="./media/bastion-remove-ip/networking.png" alt-text="“网络”页的屏幕截图。":::

1. 在 VM 的“公共 IP 地址”页上，选择“解除关联” 。

   :::image type="content" source="./media/bastion-remove-ip/disassociate.png" alt-text="VM 的公共 IP 地址的屏幕截图。":::

1. 选择“是”，将 IP 地址与网络接口解除关联。

   :::image type="content" source="./media/bastion-remove-ip/disassociate-yes.png" alt-text="解除公共 IP 地址关联的屏幕截图。":::

1. 解除 IP 地址关联后，可删除公共 IP 地址资源。 若要删除公共 IP 地址资源，请导航到相应资源组，找到要删除的 IP 地址资源。 然后，选择“删除”以删除资源。

   :::image type="content" source="./media/bastion-remove-ip/delete-resource.png" alt-text="删除公共 IP 地址资源的屏幕截图。":::