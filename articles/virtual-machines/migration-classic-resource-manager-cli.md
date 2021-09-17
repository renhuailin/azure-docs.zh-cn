---
title: 使用 Azure CLI 将 VM 迁移到资源管理器
description: 本文逐步讲解如何使用 Azure CLI 完成从经典部署模型到 Azure Resource Manager 部署模型的平台支持的资源迁移。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurecli
ms.openlocfilehash: 468c1edfd5be093b092be810d73d4c110a5f90be
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690367"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-cli"></a>使用 Azure CLI 将 IaaS 资源从经典部署模型迁移到 Azure Resource Manager 部署模型

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM

> [!IMPORTANT]
> 目前，大约有 90% 的 IaaS VM 在使用 [Azure 资源管理器](https://azure.microsoft.com/features/resource-manager/)。 自 2020 年 2 月 28 日起，经典 VM 已弃用，并将于 2023 年 3 月 1 日完全停用。 [详细了解]( https://aka.ms/classicvmretirement)此弃用以及[它对你的影响](classic-vm-deprecation.md#how-does-this-affect-me)。

以下步骤演示如何使用 Azure 命令行接口 (CLI) 命令将基础结构即服务 (IaaS) 资源从经典部署模型迁移到 Azure Resource Manager 部署模型。 本文中的操作需要 [Azure 经典 CLI](/cli/azure/install-classic-cli)。 由于 Azure CLI 仅适用于 Azure 资源管理器资源，因此它不能用于此迁移。

> [!NOTE]
> 此处描述的所有操作都是幂等的。 如果遇到功能不受支持或配置错误以外的问题，建议重试准备、中止或提交操作。 然后平台将重试操作。
> 
> 

<br>
下面是一个流程图，用于确定在迁移过程中需要执行步骤的顺序

![Screenshot that shows the migration steps](./media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-prepare-for-migration"></a>步骤 1：准备迁移
下面是建议在将 IaaS 资源从经典部署模型迁移到 Resource Manager 部署模型时遵循的一些最佳实践：

* 请参阅[不受支持的配置或功能的列表](migration-classic-resource-manager-overview.md)。 如果虚拟机使用不受支持的配置或功能，建议等到我们宣布支持该功能/配置时再进行迁移。 或者，可以删除该功能或移出该配置，以利迁移进行（如果这样做符合要求）。
* 如果通过自动化脚本来部署目前的基础结构和应用程序，则可尝试使用这些脚本进行迁移，以便创建类似的测试性设置。 也可以使用 Azure 门户设置示例环境。

> [!IMPORTANT]
> 目前不支持通过应用程序网关从经典部署模型迁移到 Resource Manager 部署模型。 如果要迁移带应用程序网关的经典虚拟网络，请先删除该网关，然后运行准备操作来移动网络。 完成迁移后，在 Azure Resource Manager 中重新连接该网关。 
>
>无法自动迁移其他订阅中连接到 ExpressRoute 线路的 ExpressRoute 网关。 此类情况下，请删除 ExpressRoute 网关、迁移虚拟网络并重新创建网关。 有关详细信息，请参阅[将 ExpressRoute 线路和关联的虚拟网络从经典部署模型迁移到资源管理器部署模型](../expressroute/expressroute-migration-classic-resource-manager.md)。
> 
> 

## <a name="step-2-set-your-subscription-and-register-the-provider"></a>步骤 2：设置订阅并注册提供程序
对于迁移方案，需要针对经典部署模型和 Resource Manager 部署模型设置环境。 [安装 Azure CLI](/cli/azure/install-classic-cli) 并[选择订阅](/cli/azure/authenticate-azure-cli)。

登录到帐户。

```azurecli
azure login
```

使用以下命令选择 Azure 订阅。

```azurecli
azure account set "<azure-subscription-name>"
```

> [!NOTE]
> 注册是一次性步骤，但必须在尝试迁移之前完成。 如果不注册，会看到以下错误消息 
> 
> *BadRequest:Subscription is not registered for migration.* （BadRequest：订阅尚未注册迁移。） 
> 
> 

使用以下命令向迁移资源提供程序注册。 请注意，在某些情况下，此命令会超时。但是，注册会成功。

```azurecli
azure provider register Microsoft.ClassicInfrastructureMigrate
```

请等五分钟让注册完成。 可以使用以下命令来检查审批状态。 请确保在继续操作之前，RegistrationState 为 `Registered` 。

```azurecli
azure provider show Microsoft.ClassicInfrastructureMigrate
```

现在请将 CLI 切换到 `asm` 模式。

```azurecli
azure config mode asm
```

## <a name="step-3-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>步骤 3：确保在当前部署或 VNET 的 Azure 区域中有足够的 Azure 资源管理器虚拟机 vCPU
要执行此步骤，需要切换到 `arm` 模式。 使用以下命令执行此操作。

```azurecli
azure config mode arm
```

可以使用以下 CLI 命令检查 Azure 资源管理器中目前的 vCPU 数量。 若要了解有关 vCPU 配额的详细信息，请参阅[限制和 Azure 资源管理器](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits)。

```azurecli
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

验证完此步骤后，可以切换回 `asm` 模式。

```azurecli
azure config mode asm
```

## <a name="step-4-option-1---migrate-virtual-machines-in-a-cloud-service"></a>步骤 4：选项 1 - 迁移云服务中的虚拟机
使用以下命令获取云服务列表，并选取要迁移的云服务。 请注意，如果云服务中的 VM 在虚拟网络中或者具有 Web/辅助角色，会收到错误消息。

```azurecli
azure service list
```

运行以下命令，从详细输出中获取云服务的部署名称。 在大多数情况下，部署名称与云服务名称相同。

```azurecli
azure service show <serviceName> -vv
```

首先，使用以下命令验证用户是否可以迁移云服务：

```shell
azure service deployment validate-migration <serviceName> <deploymentName> new "" "" ""
```

准备迁移云服务中的虚拟机。 可以从两个选项中进行选择。

如果想要将 VM 迁移到平台所创建的虚拟网络上，请使用以下命令。

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""
```

如果想要迁移到 Resource Manager 部署模型中的现有虚拟网络，请使用以下命令。

```azurecli
azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> <subnetName> <vnetName>
```

准备操作成功后，可以查看详细输出，以获取 VM 的迁移状态，并确保其处于 `Prepared` 状态。

```azurecli
azure vm show <vmName> -vv
```

使用 CLI 或 Azure 门户查看准备好的资源的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令。

```azurecli
azure service deployment abort-migration <serviceName> <deploymentName>
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源。

```azurecli
azure service deployment commit-migration <serviceName> <deploymentName>
```

## <a name="step-4-option-2----migrate-virtual-machines-in-a-virtual-network"></a>步骤 4：选项 2 - 迁移虚拟网络中的虚拟机
选取要迁移的虚拟网络。 请注意，如果虚拟网络包含的 Web/辅助角色或 VM 的配置不受支持，会收到验证错误消息。

使用以下命令获取订阅中的所有虚拟网络。

```azurecli
azure network vnet list
```

输出将如下所示：

![命令行屏幕截图，其中整个虚拟网络名称已突出显示。](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

在上面的示例中，**virtualNetworkName** 是完整名称 **"Group classicubuntu16 classicubuntu16"** 。

首先，请使用以下命令验证用户是否可以迁移虚拟网络：

```shell
azure network vnet validate-migration <virtualNetworkName>
```

使用以下命令来准备要迁移的所选虚拟网络。

```azurecli
azure network vnet prepare-migration <virtualNetworkName>
```

使用 CLI 或 Azure 门户查看准备好的虚拟机的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令。

```azurecli
azure network vnet abort-migration <virtualNetworkName>
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源。

```azurecli
azure network vnet commit-migration <virtualNetworkName>
```

## <a name="step-5-migrate-a-storage-account"></a>步骤 5：迁移存储帐户
完成虚拟机迁移之后，建议迁移存储帐户。

使用以下命令来准备要迁移的存储帐户

```azurecli
azure storage account prepare-migration <storageAccountName>
```

使用 CLI 或 Azure 门户查看准备就绪的存储帐户的配置。 如果尚未做好迁移准备，因此想要回到旧的状态，请使用以下命令。

```azurecli
azure storage account abort-migration <storageAccountName>
```

如果准备好的配置看起来没问题，则可继续进行，使用以下命令提交资源。

```azurecli
azure storage account commit-migration <storageAccountName>
```

## <a name="next-steps"></a>后续步骤

* [平台支持的从经典部署模型到 Azure Resource Manager 部署模型的 IaaS 资源迁移概述](migration-classic-resource-manager-overview.md)
* [有关平台支持的从经典部署模型到 Azure Resource Manager 部署模型的迁移的技术深入探讨](migration-classic-resource-manager-deep-dive.md)
* [规划从经典部署模型到 Azure Resource Manager 的 IaaS 资源迁移](migration-classic-resource-manager-plan.md)
* [使用 PowerShell 将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器](migration-classic-resource-manager-ps.md)
* [用于帮助将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的社区工具](migration-classic-resource-manager-community-tools.md)
* [查看最常见的迁移错误](migration-classic-resource-manager-errors.md)
* [查看有关将 IaaS 资源从经典部署模型迁移到 Azure 资源管理器部署模型的最常见问题](migration-classic-resource-manager-faq.yml)
