---
title: 使用 Azure 门户在灵活规模集中创建虚拟机
description: 了解如何在 Azure 门户中在灵活业务流程模式下创建虚拟机规模集。
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 5d25e46ae62962e3f94d4a3f9222f91ebfa1d430
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823442"
---
# <a name="preview-create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>预览：使用 Azure 门户在灵活规模集中创建虚拟机

适用于：:heavy_check_mark: 灵活规模集

本文逐步介绍如何使用 Azure 门户在灵活业务流程模式下创建虚拟机规模集。 有关灵活规模集的详细信息，请参阅[虚拟机规模集的灵活业务流程模式](flexible-virtual-machine-scale-sets.md)。 


> [!IMPORTANT]
> 采用灵活业务流程模式的虚拟机规模集目前以公共预览版提供。 需要执行一个选用过程才能使用下述公共预览版功能。
> 此预览版不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。


> [!CAUTION]
> 业务流程模式是在创建规模集时定义的，以后无法更改或更新。


## <a name="register-for-flexible-orchestration-mode"></a>注册灵活业务流程模式

在以灵活业务流程模式部署虚拟机规模集之前，必须先注册订阅以获取预览版功能。 功能注册最多可能需要 15 分钟。

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 转到你的订阅。
1. 选择订阅的名称，导航到你要在灵活业务流程模式下为其创建规模集的订阅的详细信息页。
1. 在菜单中的“设置”下，选择“预览功能” 。
1. 选择要启用的四个业务流程协调程序功能：VMOrchestratorSingleFD、VMOrchestratorMultiFD、VMScaleSetFlexPreview 和 SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview   。
1. 选择“注册”  。

为订阅注册功能后，通过将更改传播到计算资源提供程序来完成选用过程。 

1. 在菜单中的“设置”下，选择“资源提供程序” 。
1. 选择 `Microsoft.compute`。
1. 选择“重新注册”。


## <a name="get-started-with-flexible-orchestration-mode"></a>开始使用灵活业务流程模式

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>通过 Azure 门户以灵活业务流程模式创建虚拟机规模集。

1. 通过 https://portal.azure.com 登录到 Azure 门户。
1. 在搜索栏中，搜索并选择“虚拟机规模集”。
1. 在“虚拟机规模集”页上选择“创建”。 
1. 在“创建虚拟机规模集”页上查看“业务流程”部分。 
1. 对于“业务流程模式”，请选择“灵活”选项。 
1. 设置“容错域计数”。
1. 完成规模集的创建。 有关如何创建规模集的详细信息，请参阅 [在 Azure 门户中创建规模集](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set)。


### <a name="next-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>接下来，将虚拟机添加到处于灵活业务流程模式的规模集。

1. 在搜索栏中，搜索并选择“虚拟机”。
1. 在“虚拟机”页上选择“添加”。 
1. 在“基本信息”选项卡中查看“实例详细信息”部分。 
1. 通过在“可用性选项”中选择规模集，将 VM 添加到处于灵活业务流程模式的规模集。 可将虚拟机添加到同一地理区域、局部区域和资源组中的规模集。
1. 转到“网络”选项卡并明确定义出站连接。

    > [!IMPORTANT]
    > 具有灵活业务流程的虚拟机规模集需要明确定义出站连接。 有关详细信息，请参阅[显式出站网络连接](flexible-virtual-machine-scale-sets.md#explicit-network-outbound-connectivity-required)。

1. 完成虚拟机的创建。


## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [了解如何使用 Azure CLI 创建灵活的缩放。](flexible-virtual-machine-scale-sets-cli.md)