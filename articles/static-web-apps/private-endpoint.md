---
title: 在 Azure Static Web Apps 中配置专用终结点
description: 了解为 Azure Static Web Apps 配置专用终结点访问
services: static-web-apps
author: burkeholland
ms.author: buhollan
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 7/28/2021
ms.openlocfilehash: 8d6f5e019852200068d4db342ef4ab533d8779b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778194"
---
# <a name="configure-private-endpoint-in-azure-static-web-apps"></a>在 Azure Static Web Apps 中配置专用终结点

可以使用专用终结点（也称为专用链接）限制对静态 Web 应用的访问，以便只能从专用网络进行访问。

> [!NOTE]
> Static Web Apps 中的专用终结点支持目前处于预览状态。

## <a name="how-it-works"></a>工作原理

Azure 虚拟网络 (VNet) 是一种类似于传统数据中心的网络，不过 VNet 中的资源在 Microsoft 主干网络上安全地相互通信。

通过使用专用终结点配置 Static Web Apps，可使用 VNet 中的专用 IP 地址。 创建此链接后，静态 Web 应用将集成到 VNet。 因此，你的静态 Web 应用不再适用于公共 Internet，只能从 Azure VNet 中的计算机进行访问。

> [!NOTE]
> 如果将应用程序置于专用终结点后面，则意味着只能在 VNet 所在的区域使用应用。 因此，你的应用程序在多点登录中不再可用。

> [!WARNING]
> 目前，专用终结点仅保护生产环境。 即将推出的服务更新中将添加对过渡环境的支持。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。
  - [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一个 [Azure VNet](../virtual-network/quick-create-portal.md)。
- 使用 [Azure Static Web Apps](./get-started-portal.md) 部署的应用程序，该应用使用标准托管计划。

## <a name="create-a-private-endpoint"></a>创建专用终结点

在本部分中，将为静态 Web 应用创建专用终结点。

> [!IMPORTANT]
> 静态 Web 应用必须部署在标准托管计划上才能使用专用终结点。 可以从侧菜单的“托管计划”选项中更改托管计划。

1. 在门户中，打开静态 Web 应用。

1. 从侧菜单中选择“专用终结点”选项。

1. 单击 **“添加”** 按钮。

1. 在“添加专用终结点”对话框中，输入以下信息：

   | 设置                         | 值                         |
   | ------------------------------- | ----------------------------- |
   | 名称                            | 输入“myPrivateEndpoint”。  |
   | 订阅                    | 选择订阅。     |
   | 虚拟网络                 | 选择你的虚拟网络。  |
   | 子网                          | 选择子网。           |
   | 与专用 DNS 区域集成 | 保留默认值“是”。 |

   :::image type="content" source="media/create-private-link-dialog.png" alt-text="./media/create-private-link-dialog.png":::

1. 选择“确定”  。

## <a name="testing-your-private-endpoint"></a>测试专用终结点

由于你的应用程序不再公开可用，因此访问它的唯一方法是从虚拟网络内部访问。 若要进行测试，请在虚拟网络中设置一个虚拟机，然后导航到你的网站。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解专用终结点](../private-link/private-endpoint-overview.md)
