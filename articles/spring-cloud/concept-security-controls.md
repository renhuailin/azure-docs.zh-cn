---
title: Azure Spring Cloud 服务的安全控制
description: 使用内置于 Azure Spring Cloud 服务中的安全控制。
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 04/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: dbd544a3b8a2a55b6eafa51115277f6ef142b41c
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121861772"
---
# <a name="security-controls-for-azure-spring-cloud-service"></a>Azure Spring Cloud 服务的安全控制

本文适用于：✔️ Java ✔️ C#

安全控制已内置于 Azure Spring Cloud 服务中。

安全控制是促使 Azure 服务能够防范、检测和响应安全漏洞的一种服务质量或功能。  对于每项控制，我们使用“是”或“否”来指示它当前是否可用于该服务。  对于不适用于该服务的控制，我们使用“不适用”。

**数据保护安全控制**

| 安全控制 | Yes/No | 注释 | 文档 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 用户上传的源和项目、配置服务器设置、应用设置和永久性存储中的数据都会存储在 Azure 存储中，该存储会自动加密静态内容。<br><br>配置服务器缓存、从上传源生成的运行时二进制文件以及应用程序生存期内的应用程序日志都会保存到 Azure 托管磁盘，该磁盘会自动加密静态内容。<br><br>从用户上传的源生成的容器映像会保存在 Azure 容器注册表中，该注册表会自动加密静态映像内容。 | [适用于静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)<br><br>[Azure 托管磁盘的服务器端加密](../virtual-machines/disk-encryption.md)<br><br>[Azure 容器注册表中的容器映像存储](../container-registry/container-registry-storage.md) |
| 瞬态加密 | 是 | 默认情况下，用户应用公共终结点对入站流量使用 HTTPS。 |  |
| 加密的 API 调用 | 是 | 可以通过基于 HTTPS 的 Azure 资源管理器调用进行管理调用，以便配置 Azure Spring Cloud 服务。 | [Azure Resource Manager](../azure-resource-manager/index.yml) |

**网络访问安全控制**

| 安全控制 | Yes/No | 注释 | 文档 |
|:-------------|:-------|:-------------------------------|:----------------------|
| 服务标记 | 是 | 使用“AzureSpringCloud”服务标记定义[网络安全组](../virtual-network/network-security-groups-overview.md#security-rules)或 [Azure 防火墙](../firewall/service-tags.md)上的出站网络访问控制，以允许到 Azure Spring Cloud 应用程序的流量。 | [服务标记](../virtual-network/service-tags-overview.md) |

## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](./quickstart.md)
