---
title: 配置 PremiumV3 层
description: 了解如何通过扩展到新的 PremiumV3 定价层提高 Azure 应用服务中的 Web、移动和 API 应用的性能。
keywords: 应用服务, azure 应用服务, 缩放, 可缩放, 应用服务计划, 应用服务成本
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: c187a1521762c0b69d42c81f464694729d3c02f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "100594219"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>为 Azure 应用服务配置 PremiumV3 层

与现有定价层相比，新的 PremiumV3 定价层提供更快的处理器和 SSD 存储，以及四倍的内存-内核比（是 PremiumV2 层的两倍） 。 借助性能优势，可以通过在更少的实例上运行应用来节省资金。 本文介绍如何在 PremiumV3 层中创建应用或者将应用纵向扩展到 PremiumV3 层 。

## <a name="prerequisites"></a>先决条件

若要将应用纵向扩展到 PremiumV3，需要有一个 Azure 应用服务应用，该应用在低于 PremiumV3 的定价层中运行，并且必须在支持 PremiumV3 的应用服务部署中运行 。

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 可用性

PremiumV3 层适用于本机应用和容器应用（包括 Windows 容器和 Linux 容器）。

> [!NOTE]
> 在“高级容器”层中运行的任何 Windows 容器在预览期将继续按原样运行，但“高级容器”层将继续保持预览版状态 。 PremiumV3 层将正式取代“高级容器”层 。 

PremiumV3 已在某些 Azure 区域推出，我们会持续添加更多的推出区域。 若要查看它在你所在的区域是否可用，请在 [Azure Cloud Shell](../cloud-shell/overview.md) 中运行以下 Azure CLI 命令：

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>在 PremiumV3 层中创建应用

应用服务应用的定价层是在运行它的[应用服务计划](overview-hosting-plans.md)中定义的。 应用服务计划可以独立创建，也可以在创建应用的过程中创建。

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中配置应用服务计划时，选择“定价层”。  

选择“生产”，选择“P1V3”、“P2V3”或“P3V3”，然后单击“应用”    。

![屏幕截图，显示应用的建议定价层。](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> 如果未看到“P1V3”、“P2V3”和“P3V3”作为选项列出，或者这些选项灰显，则可能表示 PremiumV3 在包含应用服务计划的基础应用服务部署中不可用   。 有关更多详细信息，请参阅[从不受支持的资源组和区域组合纵向扩展](#unsupported)。

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>将现有应用纵向扩展到 PremiumV3 层

在将现有应用扩展到 PremiumV3 层之前，请确保 PremiumV3 可用 。 有关信息，请参阅 [PremiumV3 可用性](#availability)。 如果 PremiumV2 不可用，请参阅[从不受支持的资源组和区域组合纵向扩展](#unsupported)。

根据托管环境，纵向扩展可能需要执行额外的步骤。 

在 <a href="https://portal.azure.com" target="_blank">Azure 门户</a>中，打开你的应用服务应用页面。

在应用服务应用页面的左侧导航栏中，选择“纵向扩展(应用服务计划)”。

![屏幕截图，显示如何纵向扩展应用服务计划。](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

选择“生产”，选择“P1V3”、“P2V3”或“P3V3”，然后单击“应用”    。

![屏幕截图，显示应用的建议定价层。](media/app-service-configure-premium-tier/scale-up-tier-select.png)

如果操作成功完成，则应用的概述页会显示它目前处于 PremiumV3 层中。

![显示应用概览页上的 PremiumV3 定价层的屏幕截图。](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>如果遇到错误

如果基础应用服务部署不支持 PremiumV3，某些应用服务计划无法纵向扩展到 PremiumV3 层。 有关更多详细信息，请参阅[从不受支持的资源组和区域组合纵向扩展](#unsupported)。

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>从不受支持的资源组和区域组合纵向扩展

如果应用在不支持 PremiumV3 的应用服务部署中运行，或者在目前不支持 PremiumV3 的区域中运行，则需要重新部署应用才能利用 PremiumV3  。  可以使用两个选项：

- 使用新应用服务计划在新资源组中创建应用。 创建应用服务计划时，请选择一个 PremiumV3 层。 此步骤确保将应用服务计划部署到支持 PremiumV3 的部署单元。 然后，将应用程序代码重新部署到新建的应用。 即使你出于节省成本的目的将应用服务计划降低至了某个更低的层，以后也始终可以重新提高至 PremiumV3，因为部署单元支持该层。
- 如果应用已在现有的“高级”层中运行，则你可以将应用以及所有应用设置、连接字符串和部署配置克隆到使用 PremiumV3 的新应用服务计划中的新资源组 。

    ![屏幕截图，显示如何克隆应用。](media/app-service-configure-premium-tier/clone-app.png)

    在“克隆应用”页中，可以在所需的区域中创建使用 PremiumV3 的应用服务计划，并指定要克隆的应用设置和配置 。

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>从“高级容器”迁移到 PremiumV3 SKU

如果你的某个应用正在使用预览版“高级容器”SKU，而你想要迁移到新的 PremiumV3 SKU，则需要重新部署该应用才能利用 PremiumV3。 为此，请参阅[从不受支持的资源组和区域组合纵向扩展](#scale-up-from-an-unsupported-resource-group-and-region-combination)中的第一个选项

## <a name="automate-with-scripts"></a>使用脚本自动化

可以使用 [Azure CLI](/cli/azure/install-azure-cli) 或 [Azure PowerShell](/powershell/azure/) 通过脚本在 PremiumV3 层中自动创建应用。

### <a name="azure-cli"></a>Azure CLI

以下命令在 P1V3 中创建应用服务计划。 可以在 Cloud Shell 中运行此命令。 `--sku` 的选项为 P1V3、P2V3 和 P3V3 。

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

以下命令在 P1V3 中创建应用服务计划。 用于 `-WorkerSize` 的选项有 _Small_、_Medium_ 和 _Large_。

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>更多资源

[在 Azure 中纵向扩展应用](manage-scale-up.md)
[手动或自动缩放实例计数](../azure-monitor/autoscale/autoscale-get-started.md)
