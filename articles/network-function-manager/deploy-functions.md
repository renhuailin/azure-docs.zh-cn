---
title: 教程：在 Azure Stack Edge 上部署网络功能
titleSuffix: Azure Network Function Manager
description: 本教程介绍如何将一个网络功能部署为一个托管应用程序。
author: cherylmc
ms.service: network-function-manager
ms.topic: tutorial
ms.date: 06/16/2021
ms.author: cherylmc
ms.openlocfilehash: af82a3b51da76e181c2e5856458d553051b0c409
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128995"
---
# <a name="tutorial-deploy-network-functions-on-azure-stack-edge-preview"></a>教程：在 Azure Stack Edge 上部署网络功能（预览）

本教程介绍如何使用 Azure 市场在 Azure Stack Edge 上部署网络功能。 通过使用网络功能服务器，Azure 托管应用程序可以在 Azure Stack Edge 上进行简单的部署。

> [!div class="checklist"]
> * 验证[先决条件](overview.md#prereq)
> * 创建网络功能
> * 验证网络功能详细信息

## <a name="prerequisites"></a>必备条件

* 你已为网络功能管理器创建设备资源。 如果尚未完成这些步骤，请参阅[如何创建设备资源](create-device.md)。
* 在设备的“概述”选项卡上，验证是否存在以下值：
  * 预配状态 = 成功
  * 设备状态 = 已注册

## <a name="create-a-network-function"></a><a name="create"></a>创建网络功能

1. 登录到 [Azure 预览门户](https://aka.ms/AzureNetworkFunctionManager)。
1. 导航到要在其中部署网络功能的“设备”资源，然后选择“+ 创建网络功能”。

   :::image type="content" source="./media/deploy-functions/create-network-function.png" alt-text="“+ 创建网络功能”的屏幕截图。" lightbox="./media/deploy-functions/create-network-function.png":::
1. 从下拉列表中选择要使用的“供应商 SKU”，然后单击“创建”。

   :::image type="content" source="./media/deploy-functions/select.png" alt-text="供应商 SKU 的屏幕截图。" lightbox="./media/deploy-functions/select.png":::
1. 根据选定的 SKU，你将被重定向到网络功能托管应用程序的市场门户。
 
   在 Azure Stack Edge 上部署网络功能时，每个网络功能合作伙伴都有不同的要求。 此外，在部署网络功能之前，一些网络功能（例如移动数据包核心和 SD-WAN Edge）可能需要你来配置管理、LAN 和 WAN 端口，并对这些端口分配 IP 地址。 有关所需属性和 Azure Stack Edge 设备网络配置的信息，请咨询你的合作伙伴。
   
   > [!IMPORTANT]
   > 对于支持用于管理、LAN 和 WAN 虚拟网络接口的静态 IP 地址的所有网络功能，请确保没有使用为特定端口分配的 IP 地址范围中的前四个 IP 地址。 这些 IP 地址是 Azure Stack Edge 服务的保留 IP 地址。
   >

1. 按照市场门户中的步骤操作，部署合作伙伴托管的应用程序。 成功预配托管应用程序后，可以转到资源组查看托管应用。 若要检查网络功能资源的供应商预配状态是否为“已预配”，请转到托管资源组。 这可以确认网络功能的部署已成功，并且可以通过网络功能合作伙伴管理门户预配所需的其他配置。 有关使用网络功能管理器执行初始部署后的管理体验，请咨询你的合作伙伴。

### <a name="example"></a>示例

1. 在市场中查找“Fusion Core - 5G 数据包核心”，然后单击“创建”开始创建网络功能。

   :::image type="content" source="./media/deploy-functions/metaswitch.png" alt-text="Metaswitch 页面的屏幕截图。" lightbox="./media/deploy-functions/metaswitch.png":::
1. 配置基本设置。

   :::image type="content" source="./media/deploy-functions/basics-blade.png" alt-text="“基本信息”设置的屏幕截图。" lightbox="./media/deploy-functions/basics-blade.png":::
1. 应用托管标识。 有关详细信息，请参阅[托管标识](overview.md#managed-identity)。

   :::image type="content" source="./media/deploy-functions/managed-identity.png" alt-text="托管标识的屏幕截图。" lightbox="./media/deploy-functions/managed-identity.png":::
1. 输入 Fusion Core VM 的管理、LAN 和 WAN 接口的 IP 地址信息。

   :::image type="content" source="./media/deploy-functions/ip-settings.png" alt-text="Fusion Core VM 的管理、LAN 和 WAN 接口的屏幕截图。" lightbox="./media/deploy-functions/ip-settings.png":::
1. 输入“5G”和“测试 UE”配置的可选设置。

   :::image type="content" source="./media/deploy-functions/5g-settings-blade.png" alt-text="5G 的屏幕截图。" lightbox="./media/deploy-functions/5g-settings-blade.png":::

   :::image type="content" source="./media/deploy-functions/test-blade.png" alt-text="测试 UE 配置的屏幕截图。" lightbox="./media/deploy-functions/test-blade.png":::
1. 通过验证后，同意条款和条件。 然后单击“创建”，开始在客户资源组中创建 Fusion Core 托管应用程序，在托管资源组中创建网络功能资源。 必须选中托管资源组视图中的“显示隐藏的类型”框才能看到网络功能资源。

   :::image type="content" source="./media/deploy-functions/managed-app-customer.png" alt-text="客户资源组中托管应用的屏幕截图。" lightbox="./media/deploy-functions/managed-app-customer.png":::

   :::image type="content" source="./media/deploy-functions/managed-resource-group.png" alt-text="托管资源组中网络功能的屏幕截图。" lightbox="./media/deploy-functions/managed-resource-group.png":::

## <a name="next-steps"></a>后续步骤

导航到供应商门户，完成网络功能的配置。
