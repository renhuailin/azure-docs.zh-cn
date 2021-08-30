---
title: 在 Azure Stack Edge 设备上部署 Fusion Core
description: 了解如何部署来自 Microsoft Azure 和 Metaswitch Networks 的云解决方案，这些解决方案可帮助网络与时俱进，降低成本，以及创建新的业务模型和收入流。
author: djrmetaswitch
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: drichards
ms.openlocfilehash: 93e7511cbe8202f15dd055467c253044fa617e74
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286913"
---
# <a name="deploy-fusion-core-on-an-azure-stack-edge-device"></a>在 Azure Stack Edge 设备上部署 Fusion Core

本文概要介绍在 Azure Stack Edge 设备上部署 Fusion Core 的过程。

## <a name="collect-required-azure-resources"></a>收集所需的 Azure 资源

必须满足以下条件：

- 充分部署的带 GPU 的 Azure Stack Edge Pro。 设备的端口必须按以下方式连接。

  - 端口 5 - LAN
  - 端口 6 - WAN
  - 一个连接到管理网络的端口。 可以选择 1 到 4 中的任一端口。 必须已启用所选的计算端口，如[教程：为具有 GPU 的 Azure Stack Edge Pro 配置网络](../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)的“启用计算网络”步骤中所述。
- 具有活动订阅和以下访问权限的 Azure 帐户。

  - Azure 网络功能管理器服务。 该服务的资源提供程序命名空间为 Microsoft.HybridNetwork。 有关详细信息，请参阅[什么是 Azure 网络功能管理器？](../network-function-manager/overview.md)。
  - Fusion Core - 5G 数据包核心托管应用程序。 必须通过访问 [https://azuremarketplace.microsoft.com/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/metaswitch.fusioncore_0-1-0?tab=Overview) 并使用“联系我”按钮来请求访问权限。 我们的销售代表将提供有关如何评估或购买 Fusion Core 的信息。 你选择了其中一个选项后，我们将提供对托管应用程序的访问权限。
  - 订阅范围内的内置“读取者”角色。 如果组织无法做到这一点，请联系你的 Metaswitch 支持代表。
- 一个已配置的“Azure 网络功能管理器 - 设备”对象，它表示 Azure Stack Edge 设备。

## <a name="deploy-fusion-core"></a>部署 Fusion Core

下图显示了部署 Fusion Core 的过程，包括要创建的对象以及安装后用于持续管理的方法。

:::image type="content" source="./media/deploy-metaswitch-solution/fusion-core-on-azure-stack-edge-deployment-process.png" alt-text="Fusion Core 部署过程":::  

从 Azure 市场选择适用于 ASE 的 Fusion Core 后，将在 Azure 订阅中的所选资源组中部署 Fusion Core 托管应用程序的命名实例。

Fusion Core 托管应用程序会创建受管理资源组，其中包含要安装在 Azure Stack Edge (ASE) 设备上的数据包核心网络功能和中继资源。

- 数据包核心网络功能资源可定义 Fusion Core 基本 VM 版本及其配置参数。 默认情况下，它在 Azure 门户中处于隐藏状态。
- 中继资源包含许多混合连接，这些连接可为安装和远程访问监视界面提供必要的连接。

然后，Fusion Core 将自动部署到 ASE 设备上，如下所示。

1. ASE 设备下载并启动 Fusion Core 基本 VM。
1. Metaswitch Service Management 应用程序将 Fusion Core 安装在 Fusion Core 基本 VM 内的 Kubernetes 群集上，并对其进行预配。

除了协调 Fusion Core 的安装之外，Metaswitch Service Management 应用程序还使 Metaswitch 支持代表能够监视系统运行状况和访问诊断信息。


## <a name="next-steps"></a>后续步骤
- 有关在 Azure Stack Edge 设备上部署 Fusion Core 的分步说明，请[下载专用 5G Edge Fusion Core 解决方案指南](https://go.microsoft.com/fwlink/?linkid=2165096)。
- 详细了解[专用 5G Edge Fusion Core](metaswitch-fusion-core-overview.md)