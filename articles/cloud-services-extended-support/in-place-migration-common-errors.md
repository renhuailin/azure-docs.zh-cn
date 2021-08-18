---
title: 迁移到 Azure 云服务（外延支持）时的常见错误和已知问题
description: 从云服务（经典）迁移到云服务（外延支持）时的常见错误概述
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: cdfb7dd5d09d6925de23be70df18037165776a18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751114"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>迁移到 Azure 云服务（外延支持）时的常见错误和已知问题

本文介绍从云服务（经典）迁移到云服务（外延支持）时可能遇到的已知问题和常见错误。 

## <a name="known-issues"></a>已知问题
以下已知问题将得到解决。

| 已知问题 | 缓解措施 | 
|---|---|
| 成功提交后，角色实例按升级域 (UD) 重启。 | 重启操作采用与每月来宾操作系统推出相同的方法。 不要对具有单个角色实例或受重启影响的云服务提交迁移。| 
| 刷新浏览器后，Azure 门户无法读取迁移状态。 | 重新运行验证和准备操作，以恢复到原始迁移状态。 | 
| 证书在密钥保管库中显示为机密资源。 | 在迁移后重新上传证书作为证书资源可简化云服务（外延支持）的更新操作。 | 
| 在迁移过程中，部署标签未保存为标记。 | 在迁移后手动创建标记可维护这些信息。
| 资源组名称全大写。 | 无影响。 尚未提供解决方案。 |
| 云服务（外延支持）锁上的锁名称不正确。 | 无影响。 尚未提供解决方案。 | 
| 云服务（外延支持）门户边栏选项卡上的 IP 地址名称不正确。 | 无影响。 尚未提供解决方案。 | 
| 对已迁移的云服务执行更新操作后，为虚拟 IP 地址显示的 DNS 名称无效。 | 无影响。 尚未提供解决方案。 | 
| 成功准备后，不允许将新的云服务链接（外延支持）部署链接为可交换云服务。 | 不要将新的云服务作为可交换云服务链接到已准备的云服务。 | 
| 需要更新错误消息。 | 无影响。 | 

## <a name="common-migration-errors"></a>常见的迁移错误
常见的迁移错误和缓解步骤。 

| 错误消息 | 详细信息 | 
|---|---|
| 命名空间 `Microsoft.Compute` 中未发现用于 API 版本“2020-10-01-preview”的资源类型。 | 为云服务功能标志[注册订阅](in-place-migration-overview.md#setup-access-for-migration)以访问公开预览版。 | 
| 服务器遇到内部错误。 重试请求。 | 重试该操作，或者使用 [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) 或联系支持人员。 | 
| 服务器尝试为云服务分配网络资源时遇到意外错误。 重试请求。 | 重试该操作，或者使用 [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) 或联系支持人员。 | 
| 云服务 cloud-service-name 中的部署 deployment-name 必须位于要迁移的虚拟网络中。 | 部署不在虚拟网络中。 有关更多详细信息，请参阅[此文档](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network)。 | 
| 不支持迁移云服务 cloud-service-name 中的部署 deployment-name，因为该部署位于区域 region-name。 允许的区域：[可用区域列表]。 | 尚不支持迁移区域。 | 
| 无法迁移云服务 cloud-service-name 中的部署 deployment-name，因为没有子网与角色 role-name 关联。 将所有角色与子网相关联，然后重试迁移云服务。 | 在迁移之前，通过将云服务(经典)部署置于子网中来对其进行更新。 |  
| 无法迁移云服务 cloud-service-name 中的部署 deployment-name，因为该部署所需的至少一项功能未在 Azure 资源管理器中的订阅上注册。 请注册所需的所有功能，以迁移该部署。 缺少的功能：[缺少的功能列表]。 | 联系支持人员以获取已注册的功能标志。 | 
| 无法迁移部署，因为该部署的云服务已有两个槽位被占用。 仅支持为云服务中只有一个部署的部署迁移云服务。 请删除云服务中的其他部署以继续迁移该部署。 | 有关更多详细信息，请参阅[不支持的方案](in-place-migration-technical-details.md#unsupported-configurations--migration-scenarios)列表。 | 
| 托管服务 cloud-service-name 中的部署 deployment-name 处于中间状态 state。 不允许迁移。 | 正在创建、删除或更新部署。 等待操作完成，然后重试。 | 
| 托管服务 cloud-service-name 中的部署 deployment-name 具有保留的 IP，但没有保留的 IP 名称。 若要解决此问题，请更新保留的 IP 名称或与 Microsoft Azure 服务台联系。 | 更新云服务部署。 | 
| 托管服务 cloud-service-name 中的部署 deployment-name 具有保留的 IP reserved-ip-name，但该保留的 IP 上没有终结点。 若要解决此问题，请向保留的 IP 添加至少一个终结点。 | 向保留的 IP 添加终结点。 | 
| 正在提交托管服务 {1} 中的部署 {0} 的迁移，成功完成之前无法更改。  | 等待或重试操作。 | 
| 正在中止托管服务 {1} 中的部署 {0} 的迁移，成功完成之前无法更改。 | 等待或重试操作。 |
| 托管服务 {1} 中的部署 {0} 的一个或多个虚拟机正在进行更新操作。 在上一个操作成功完成之前，无法迁移该部署。 请稍后重试。 | 等待操作完成。 | 
| 不支持迁移托管服务 {1} 中的部署 {0}，因为该部署使用以下尚不支持迁移的功能：非 VNet 部署。| 部署不在虚拟网络中。 有关更多详细信息，请参阅[此文档](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network)。 | 
| 虚拟网络名称不能为 NULL 或为空。 | 在 REST 请求正文中提供虚拟网络名称 | 
| 子网名称不能为 NULL 或为空。 | 在 REST 请求正文中提供子网名称。 | 
| DestinationVirtualNetwork 必须设置为以下值之一：Default、New 或 Existing。 | 在 REST 请求正文中提供 DestinationVirtualNetwork 属性。 | 
| 未实现“默认 VNet 目标”选项。 | REST 请求正文中的 DestinationVirtualNetwork 属性不支持值“Default”。 | 
| 无法迁移部署 {0}，因为 CSPKG 不可用。 | 升级部署，然后重试。 | 
| ID 为“{0}”的子网与托管服务“{2}”中的部署“{1}”位于不同的位置。 子网的位置是“{3}”，而托管服务的位置是“{4}”。  请指定与部署位于同一个位置的子网。 | 在迁移之前，更新云服务，使子网和云服务位于同一个位置。 | 
| 正在中止托管服务 {1} 中的部署 {0} 的迁移，成功完成之前无法更改。 | 等待中止操作完成，或重试中止。 否则使用 [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) 或联系支持人员。 | 
| 托管服务 {1} 中的部署 {0} 尚未准备好进行迁移。 | 在运行提交操作之前，对云服务运行准备操作。 | 
| UnknownExceptionInEndExecute: Contract.Assert 失败：rgName 为 NULL 或为空: EndExecute 出现异常，该异常不是 RdfeException。 |   使用 [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) 或联系支持人员。 | 
| UnknownExceptionInEndExecute: 任务已取消: EndExecute 出现异常，该异常不是 RdfeException。 | 使用 [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) 或联系支持人员。 | 
| XrpVirtualNetworkMigrationError: 虚拟网络迁移失败。 | 使用 [Microsoft Q&A](/answers/topics/azure-cloud-services-extended-support.html) 或联系支持人员。 | 
| 托管服务 {1} 中的部署 {0} 属于虚拟网络 {2}。 请迁移虚拟网络 {2} 以迁移托管服务 {1}。 | 请参阅[虚拟网络迁移](in-place-migration-technical-details.md#virtual-network-migration)。 | 
| Azure 资源管理器中的资源 resource-name 的当前配额不足，无法完成迁移。 当前配额为 {0}，需要额外的配额为 {1}。 请提交支持请求以增加配额，然后在配额已增加后重试迁移。    | 通过相应的通道请求增加配额： <br>[为网络资源增加配额](../azure-portal/supportability/networking-quota-requests.md) <br>[为计算资源增加配额](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>后续步骤
有关迁移要求的详细信息，请参阅[迁移到 Azure 云服务（外延支持）的技术详细信息](in-place-migration-technical-details.md)