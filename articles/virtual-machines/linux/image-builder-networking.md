---
title: Azure 映像生成器服务网络选项
description: 了解部署 Azure VM 映像生成器服务时的网络选项
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 19e77b8a2baa168b1ca6ed335711b2f41a2c1aea
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113596274"
---
# <a name="azure-image-builder-service-networking-options"></a>Azure 映像生成器服务网络选项

需要选择使用或不使用现有 VNET 来部署 Azure 映像生成器。

## <a name="deploy-without-specifying-an-existing-vnet"></a>部署但不指定现有 VNET

如果未指定现有 VNET，Azure 映像生成器将在暂存资源组中创建一个 VNET 和子网。 使用公共 IP 资源和网络安全组来限制到 Azure 映像生成器服务的入站流量。 公共 IP 使 Azure 映像生成器命令的通道在生成过程中更为便利。 生成完成后，将删除 VM、公共 IP、磁盘和 VNET。 要使用此选项，请不要指定任何 VNET 属性。

## <a name="deploy-using-an-existing-vnet"></a>使用现有 VNET 进行部署

如果指定 VNET 和子网，Azure 映像生成器会将生成 VM 部署到所选 VNET。 可以访问在 VNET 中可访问的资源。 你还可以创建未连接到任何其他 VNET 的孤立 VNET。 如果指定 VNET，Azure 映像生成器不会使用公共 IP 地址。 从 Azure 映像生成器服务到生成 VM 的通信使用 Azure 专用链接技术。

有关详细信息，请参阅以下示例之一：

* [使用 Azure 映像生成器创建允许访问现有 Azure VNET 的 Windows VM](../windows/image-builder-vnet.md)
* [使用 Azure 映像生成器创建允许访问现有 Azure VNET 的 Linux VM](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>什么是 Azure 专用链接？

Azure 专用链接提供从虚拟网络到 Azure 平台即服务 (PaaS)、客户拥有的服务或 Microsoft 合作伙伴服务的专用连接。 它简化了网络体系结构，并通过消除数据在公共 Internet 上的暴露来保护 Azure 中终结点之间的连接。 有关详细信息，请参阅[专用终结点文档](../../private-link/index.yml)。

### <a name="required-permissions-for-an-existing-vnet"></a>现有 VNET 的必需权限

Azure 映像生成器需要特定权限才能使用现有 VNET。 有关详细信息，请参阅[使用 Azure CLI 配置 Azure 映像生成器服务权限](image-builder-permissions-cli.md)或[使用 PowerShell 配置 Azure 映像生成器服务权限](image-builder-permissions-powershell.md)。

### <a name="what-is-deployed-during-an-image-build"></a>映像生成过程中部署了哪些内容？

使用现有的 VNET 意味着 Azure 映像生成器将额外的 VM 部署（代理 VM）以及连接到专用链接的 Azure 负载均衡器 (ALB)。 来自 AIB 服务的流量通过专用链接到达 ALB。 对于 Linux OS，ALB 使用端口 60001 与代理 VM 进行通信，而对于 Windows OS，则使用端口 60000。 对于 Linux OS，代理使用端口 22 将命令转发到生成 VM，对于 Windows OS，则使用端口 5986。

> [!NOTE]
> VNET 必须与 Azure VM 映像生成器服务区域在同一个区域中。
> 

### <a name="why-deploy-a-proxy-vm"></a>为什么要部署代理 VM？

当没有公共 IP 的 VM 位于内部负载均衡器后面时，该 VM 无法访问 Internet。 用于 VNET 的 Azure 负载均衡器是内部的。 代理 VM 允许在生成期间对生成 VM 进行 Internet 访问。 可以使用关联的网络安全组来限制生成 VM 的访问。

除了生成 VM 外，部署的代理 VM 大小为标准 A1_v2。 代理 VM 用于在 Azure 映像生成器服务和生成 VM 之间发送命令。 不能更改代理 VM 的属性，包括大小或操作系统。 无法为 Windows 和 Linux 映像版本更改代理 VM 属性。

### <a name="image-template-parameters-to-support-vnet"></a>支持 VNET 的映像模板参数
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| 设置 | 说明 |
|---------|---------|
| name | （可选）先前存在的虚拟网络的名称。 |
| subnetName | 指定的虚拟网络中的子网名称。 仅当已指定 name 时，才可以指定该元素。 |
| resourceGroupName | 包含指定虚拟网络的资源组的名称。 仅当已指定 name 时，才可以指定该元素。 |

专用链接服务需要给定 VNET 和子网中的 IP。 目前，Azure 不支持这些 IP 上的网络策略。 因此，需要在子网上禁用网络策略。 有关详细信息，请参阅[专用终结点文档](../../private-link/index.yml)。

### <a name="checklist-for-using-your-vnet"></a>使用 VNET 时的核对清单

1. 允许 Azure 负载均衡器 (ALB) 与 NSG 中的代理 VM 进行通信
    * [AZ CLI 示例](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell 示例](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. 在子网上禁用专用服务策略
    * [AZ CLI 示例](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell 示例](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. 允许 Azure 映像生成器创建 ALB 并将 VM 添加到 VNET
    * [AZ CLI 示例](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell 示例](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. 允许 Azure 映像生成器读取/写入源映像并创建映像
    * [AZ CLI 示例](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell 示例](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. 确保在 Azure 映像生成器服务区域所在的同一区域中使用 VNET。


## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure 映像生成器概述](../image-builder-overview.md)。