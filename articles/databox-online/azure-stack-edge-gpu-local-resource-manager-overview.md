---
title: 什么是 Azure Stack Edge Pro GPU 设备上的本地 Azure 资源管理器
description: 概述了 Azure Stack Edge 设备上的本地 Azure 资源管理器。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 06/30/2021
ms.author: alkohli
ms.openlocfilehash: 8d200195540f92c88ae3dc93737ea09a461e6c26
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "113114444"
---
# <a name="what-is-local-azure-resource-manager-on-azure-stack-edge"></a>什么是 Azure Stack Edge 上的本地 Azure 资源管理器？

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure 资源管理器提供一个管理层，用于在 Azure 订阅中创建、更新和删除资源。 Azure Stack Edge 设备支持用相同的 Azure 资源管理器 API 在本地订阅中创建、更新和删除 VM。 通过此支持，你能够以与云一致的方式管理设备。 

本文概述了可用于连接到 Azure Stack Edge 设备上的本地 API 的本地 Azure 资源管理器。

## <a name="about-local-azure-resource-manager"></a>关于本地 Azure 资源管理器

本地 Azure 资源管理器通过使用资源管理器模板为 Azure Stack Edge 设备的所有调用提供一致管理层。 下面几节将讨论本地 Azure 资源管理器的好处。

#### <a name="consistent-management-layer"></a>一致的管理层

本地 Azure 资源管理器提供一致的管理层来调用 Azure Stack Edge 设备 API，并执行创建、更新和删除 VM 等操作。 

1. 从 REST API 或 SDK 发送请求时，设备上的本地 Azure 资源管理器将接收到该请求。 
1. 本地 Azure 资源管理器使用安全令牌服务 (STS) 对请求进行身份验证和授权。 STS 负责创建、验证、续订和取消安全令牌。 STS 创建两种类型的安全令牌 - 访问令牌和刷新令牌。 这些令牌用于设备和通过本地 Azure 资源管理器访问设备的客户端之间的持续通信。
1. 然后，资源管理器将请求发送到执行所请求操作的资源提供程序。   

    已预先注册 Azure Stack Edge 的资源提供程序如下：

    - 计算：`Microsoft.Compute` 或计算资源提供程序允许你在 Azure Stack Edge 上部署 VM。 计算资源提供程序包括创建 VM 和 VM 扩展的能力。 

    - 网络资源提供程序：`Microsoft.Network` 或网络资源提供程序允许你创建网络接口和虚拟网络等资源。

    - 存储资源提供程序：`Microsoft.Storage` 或存储资源提供程序提供 Azure 一致的 blob 存储服务和 Key Vault 帐户管理，用于管理和审核密码和证书等机密内容。  
    
    - 磁盘资源提供程序：`Microsoft.Disks` 或磁盘资源提供程序将允许创建可用于创建 VM 的托管磁盘。

    资源是可以通过 Azure Stack Edge 获得的可管理项目，资源提供程序负责提供资源。 例如，虚拟机、存储帐户、存储帐户和虚拟网络都是资源示例。 计算资源提供程序提供虚拟机资源。    

由于所有请求是通过同一个 API 处理的，因此在所有不同的工具中会看到一致的结果和功能。

下图显示了处理所有 API 请求的机制，以及本地 Azure 资源管理器在提供一致管理层来处理这些请求方面所扮演的角色。

![Azure 资源管理器关系图。](media/azure-stack-edge-gpu-connect-resource-manager/edge-device-flow.svg)


#### <a name="use-of-resource-manager-templates"></a>使用资源管理器模板

Azure 资源管理器的另一个关键好处是，它允许使用资源管理器模板。 这些 JavaScript 对象表示法 (JSON) 文件采用声明性语法，可用于一致且重复地部署资源。 声明性语法让你能够声明“以下是我想要创建的项目”，无需编写一系列编程命令进行创建。 例如，可以使用这些声明性语法模板将虚拟机部署到 Azure Stack Edge 设备。 有关详细信息，请参阅[通过模板在 Azure Stack Edge 设备上部署虚拟机](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)。

## <a name="connect-to-the-local-azure-resource-manager"></a>连接到本地 Azure 资源管理器

若要在 Azure Stack Edge 设备上创建虚拟机或共享或存储帐户，需要创建相应的资源。 例如，对于虚拟机，需要来自网络、磁盘和存储资源提供程序的资源，例如 VM 上的网络接口、OS 和数据磁盘。 

若要请求从资源提供程序创建任何资源，首先需要连接到本地 Azure 资源管理器。 有关详细步骤，请参阅[连接到 Azure Stack Edge 设备上的 Azure 资源管理器](azure-stack-edge-gpu-connect-resource-manager.md)。

首次连接到 Azure 资源管理器时，还需要重置密码。 有关详细步骤，请参阅[重置 Azure 资源管理器密码](azure-stack-edge-gpu-set-azure-resource-manager-password.md)。


## <a name="azure-resource-manager-endpoints"></a>Azure 资源管理器终结点

本地 Azure 资源管理器和 STS 服务在设备上运行，并可在特定终结点访问。 下表总结了由这些服务在设备上公开的各种终结点、支持的协议以及用于访问这些终结点的端口。 

| # | 端点 | 支持的协议 | 使用的端口 | 用途 |
| --- | --- | --- | --- | --- |
| 1. | Azure 资源管理器 | https | 443 | 连接到 Azure 资源管理器以实现自动化 |
| 2. | 安全令牌服务 | https | 443 | 通过访问和刷新令牌进行身份验证 |


## <a name="next-steps"></a>后续步骤

[连接到 Azure Stack Edge Pro GPU 设备上的本地 Azure 资源管理器](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)。
