---
title: 如何在不同区域之间迁移 Azure API 管理
description: 了解如何将 API 管理实例从一个区域迁移到另一个区域。
services: api-management
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 08/20/2021
ms.author: danlep
ms.custom: subject-moving-resources
ms.openlocfilehash: 1007241c09e22d1fb35f6cc12733c04946af88f7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591779"
---
# <a name="how-to-move-azure-api-management-across-regions"></a>如何跨区域移动 Azure API 管理

本文介绍了如何将 API 管理实例移动到其他 Azure 区域。 你可能出于多种原因，将实例移动到其他区域。 例如：

* 找到更接近 API 使用者情况的实例
* 仅部署特定区域可用的功能
* 满足内部策略和治理要求

如要将 API 管理实例从一个 Azure 区域移动到另一个区域，请使用该服务的[备份与还原](api-management-howto-disaster-recovery-backup-restore.md)操作。 你可以使用不同的 API 管理实例名称或现有名称。 

> [!NOTE]
> API 管理还支持[多区域部署](api-management-howto-deploy-multi-region.md)，该部署可跨多个 Azure 区域分布单个 Azure API 管理服务。 此跨区域部署有助于减少地理分布式 API 使用者所感知的请求延迟，并且还能改善当其中一个区域处于离线状态时的服务可用性。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="considerations"></a>注意事项

* 在源和目标区域选择相同的 API 管理定价层。 
* 在不同云类型之间迁移时，备份和还原将不起作用。 请为该方案导出资源[作为模板](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)。 然后，改编目标 Azure 区域的已导出模板并重新创建资源。 

## <a name="prerequisites"></a>必备条件

* 查看 API 管理[备份与还原](api-management-howto-disaster-recovery-backup-restore.md)操作的要求及限制。 
* 查看[不用备份的内容](api-management-howto-disaster-recovery-backup-restore.md#what-is-not-backed-up)。 记录在移动实例后需要手动重新创建的设置和数据。
* 在源区域创建[存储帐户](../storage/common/storage-account-create.md?tabs=azure-portal)。 你将使用此帐户备份源实例。 

## <a name="prepare-and-move"></a>准备并移动

### <a name="option-1-use-a-different-api-management-instance-name"></a>选项 1：使用另一个 API 管理实例名称

1. 在目标区域创建新的 API 管理实例，所用定价层与源 API 管理实例相同。 为新实例使用不同的名称。
1. 将现有 API 管理实例[备份](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service)到存储帐户。 
1. 将源实例的备份[还原](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service)到新的 API 管理实例。
1. 如果有一个指向源区域 API 管理实例的自定义域，请更改自定义域 CNAME，使之指向新的 API 管理实例。 

### <a name="option-2-use-the-same-api-management-instance-name"></a>选项 2：使用同一个 API 管理实例名称

> [!WARNING]
> 此选项会删除原始 API 管理实例，在迁移期间还会造成故障。 请确保在删除源实例之前进行有效备份。

1. 将现有 API 管理实例[备份](api-management-howto-disaster-recovery-backup-restore.md#-back-up-an-api-management-service)到存储帐户。 
1. 删除源区域中的 API 管理实例。 
1. 在目标区域创建新的 API 管理实例，所用名称与源区域相同。
1. 将源实例的备份[还原](api-management-howto-disaster-recovery-backup-restore.md#-restore-an-api-management-service)到目标区域中的新 API 管理实例。  

## <a name="verify"></a>验证

1. 在访问目标区域中的 API 管理实例之前，请确保成功完成还原操作。
1. 配置在还原操作过程中不会自动移动的设置。 示例：虚拟网络配置、托管标识、开发人员门户内容以及自定义域和自定义 CA 证书。
1. 访问目标区域中的 API 管理终结点。 例如，测试 API，或访问开发人员门户。

## <a name="clean-up-source-resources"></a>清理源资源

如果使用选项 1 移动了 API 管理实例，则在成功还原和配置目标实例之后，你可以删除源实例。

## <a name="next-steps"></a>后续步骤

* 有关备份和还原功能的详细信息，请参阅[如何实现灾难恢复](api-management-howto-disaster-recovery-backup-restore.md)。
* 有关迁移 Azure 资源的信息，请参阅 [Azure 跨区域迁移指南](https://github.com/Azure/Azure-Migration-Guidance)。
* [优化并节省云支出](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)。
