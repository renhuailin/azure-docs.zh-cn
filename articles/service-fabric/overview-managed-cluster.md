---
title: Service Fabric 托管群集
description: Service Fabric 托管群集从 Azure Service Fabric 群集资源模型演变而来，该模型可以简化部署和群集管理。
ms.topic: overview
ms.date: 5/10/2021
ms.openlocfilehash: 12f5c7bd16e8738aaadfb8ca9aeed491bab5b8e0
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866911"
---
# <a name="service-fabric-managed-clusters"></a>Service Fabric 托管群集

Service Fabric 托管群集从 Azure Service Fabric 群集资源模型演变而来，该模型可以简化部署和群集管理体验。

传统 Service Fabric 群集的 Azure 资源模型 (ARM) 模板要求你定义群集资源和许多支持资源，所有这些资源在部署后群集的整个生命周期中必须正确地“进行连接”，群集和服务才能正常运行。 与此相反，Service Fabric 托管群集的封装模型由单个 Service Fabric 托管群集资源组成。 群集的所有基础资源都是由 Azure 代表你进行摘录和管理。

**Service Fabric 传统群集模型**
![Service Fabric 传统群集模型][sf-composition]

**Service Fabric 托管群集模型**
![Service Fabric 封装群集模型][sf-encapsulation]

就大小和复杂性而言，Service Fabric 托管群集的 ARM 模板约为 100 个 JSON 行，而定义典型的 Service Fabric 群集需要大约 1000 行：

| Service Fabric 资源 | Service Fabric 托管群集资源 |
|----------|-----------|
| Service Fabric 群集 | Service Fabric 托管群集 |
| 虚拟机规模集 | |
| 负载均衡器 | |
| 公共 IP 地址 | |
| 存储帐户 | |
| 虚拟网络 | |

## <a name="service-fabric-managed-cluster-advantages"></a>Service Fabric 托管群集优势
与传统群集相比，Service Fabric 托管群集具有许多优势，包括：

**简化的群集部署和管理**
- 部署和管理单个 Azure 资源
- 群集证书管理和 90 天自动轮换
- 简化的缩放操作
- 自动 OS 映像升级支持
- 就地 OS SKU 更改支持

**防止操作错误**
- 防止与基础资源的配置不匹配
- 阻止不安全的操作（例如删除种子节点）

**默认情况下的最佳做法**
- 简化的可靠性和持久性设置

除了群集所需的基础资源成本，Service Fabric 托管群集不会产生额外的成本，而且同样的 Service Fabric SLA 适用于托管群集。

> [!NOTE]
> 没有从现有 Service Fabric 群集到托管群集的迁移路径。 需要创建新的 Service Fabric 托管群集才能使用此新资源类型。

## <a name="service-fabric-managed-cluster-skus"></a>Service Fabric 托管群集 SKU

“基本”和“标准”SKU 中都提供了 Service Fabric 托管群集。

| 功能 | 基本 | Standard |
| ------- | ----- | -------- |
| 网络资源（适用于[负载均衡器](../load-balancer/skus.md)、[公共 IP](../virtual-network/public-ip-addresses.md) 的 SKU） | 基本 | Standard |
| 最小节点（VM 实例）计数 | 3 | 5 |
| 每个节点类型的最大节点计数 | 100 | 1000 |
| 节点类型最大计数 | 1 | 20 |
| 添加/删除节点类型 | 否 | 是 |
| 区域冗余 | 否 | 是 |

## <a name="feature-support"></a>功能支持

托管群集的功能将继续扩展。 目前支持以下功能：

* [使用 ARM 模板进行应用程序部署](how-to-managed-cluster-app-deployment-template.md)
* [应用程序密钥](how-to-managed-cluster-application-secrets.md)
* [自动 OS 映像升级](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)
* [可用性区域跨越](how-to-managed-cluster-availability-zones.md)
* [磁盘加密](how-to-enable-managed-cluster-disk-encryption.md)和[托管磁盘类型](how-to-managed-cluster-managed-disk.md)选择
* 托管群集[节点类型](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md)和[应用程序身份验证](how-to-managed-cluster-application-managed-identity.md)的托管标识支持
* [NSG 规则和其他网络选项](how-to-managed-cluster-networking.md)
* [仅限无状态节点类型](how-to-managed-cluster-stateless-node-type.md)
* 节点类型的[虚拟机规模集扩展](how-to-managed-cluster-vmss-extension.md)

## <a name="next-steps"></a>后续步骤

若要开始使用 Service Fabric 托管群集，请尝试以下快速入门：

> [!div class="nextstepaction"]
> [创建 Service Fabric 托管群集](quickstart-managed-cluster-template.md)

以及参考[如何配置托管群集](how-to-managed-cluster-configuration.md)

[sf-composition]: ./media/overview-managed-cluster/sfrp-composition-resource.png
[sf-encapsulation]: ./media/overview-managed-cluster/sfrp-encapsulated-resource.png