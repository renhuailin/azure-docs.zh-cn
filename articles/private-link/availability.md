---
title: Azure 专用链接可用性
description: 在本文中，了解哪些 Azure 服务支持专用链接。
author: asudbring
ms.author: allensu
ms.service: private-link
ms.topic: conceptual
ms.date: 3/15/2021
ms.custom: template-concept,references_regions
ms.openlocfilehash: aaa013a25e9131ec55f002db089de0d353f13f7c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128585759"
---
# <a name="azure-private-link-availability"></a>Azure 专用链接可用性

使用 Azure 专用链接，可以通过虚拟网络中的[专用终结点](private-endpoint-overview.md)访问 Azure PaaS 服务（例如，Azure 存储和 SQL 数据库）和 Azure 托管的客户拥有的服务/合作伙伴服务。 

> [!IMPORTANT]
> Azure 专用链接现已推出正式版。 专用终结点和专用链接服务（标准负载均衡器后面的服务）都已推出正式版。 不同的 Azure PaaS 会按不同计划加入 Azure 专用链接。 有关已知的限制，请参阅[专用终结点](private-endpoint-overview.md#limitations)和[专用链接服务](private-link-service-overview.md#limitations)。 

## <a name="service-availability"></a>服务可用性

下表列出了专用链接服务及其可用区域。 

### <a name="ai--machine-learning"></a>AI + 机器学习

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure 机器学习 | 所有公共区域    |  | GA   <br/> [了解如何为 Azure 机器学习创建专用终结点。](../machine-learning/how-to-configure-private-link.md)   |

### <a name="analytics"></a>分析

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure Synapse Analytics| 所有公共区域 <br/> 所有政府区域 |  支持代理[连接策略](../azure-sql/database/connectivity-architecture.md#connection-policy) |GA <br/> [了解如何为 Azure Synapse Analytics 创建专用终结点。](../azure-sql/database/private-endpoint-overview.md)|
|Azure 事件中心 | 所有公共区域<br/>所有政府区域      |   | GA   <br/> [了解如何为 Azure 事件中心创建专用终结点。](../event-hubs/private-link-service.md)  |
| Azure Monitor <br/>（Log Analytics 和 Application Insights） | 所有公共区域      |  | GA   <br/> [了解如何为 AAzure Monitor 创建专用终结点。](../azure-monitor/logs/private-link-security.md)   |
|Azure 数据工厂 | 所有公共区域<br/> 所有政府区域<br/>所有中国区域    | 凭据需要存储在某个 Azure 密钥保管库中| GA   <br/> [了解如何为 Azure 数据工厂创建专用终结点。](../data-factory/data-factory-private-link.md)   |

### <a name="compute"></a>计算

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure 应用配置 | 所有公共区域      |  | GA  </br> [了解如何为 Azure 应用程序配置服务创建专用终结点](../azure-app-configuration/concept-private-endpoint.md) |
|Azure 托管磁盘 | 所有公共区域<br/> 所有政府区域<br/>所有中国区域    | [选择以了解已知限制](../virtual-machines/disks-enable-private-links-for-import-export-portal.md#limitations) | GA   <br/> [了解如何为 Azure 托管磁盘创建专用终结点。](../virtual-machines/disks-enable-private-links-for-import-export-portal.md)   |

### <a name="containers"></a>容器

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure 容器注册表 | 所有公共区域<br/> 所有政府区域    | 在容器注册表的高级层中受支持。 [针对层级进行选择](../container-registry/container-registry-skus.md)| GA   <br/> [了解如何为 Azure 容器注册表创建专用终结点。](../container-registry/container-registry-private-link.md)   |
|Azure Kubernetes 服务 - Kubernetes API | 所有公共区域      |  | GA   <br/> [了解如何为 Azure Kubernetes 服务创建专用终结点。](../aks/private-clusters.md)   |

### <a name="databases"></a>数据库

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure SQL Database         | 所有公共区域 <br/> 所有政府区域<br/>所有中国区域      |  支持代理[连接策略](../azure-sql/database/connectivity-architecture.md#connection-policy) | GA <br/> [了解如何为 Azure SQL 创建专用终结点](./tutorial-private-endpoint-sql-portal.md)      |
|Azure Cosmos DB|  所有公共区域<br/> 所有政府区域</br> 所有中国区域 | |GA <br/> [了解如何为 Cosmos DB 创建专用终结点。](./tutorial-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL - 单一服务器         | 所有公共区域 <br/> 所有政府区域<br/>所有中国区域     | 在常规用途和内存优化定价层中受支持 | GA <br/> [了解如何为 Azure Database for PostgreSQL 创建专用终结点。](../postgresql/concepts-data-access-and-security-private-link.md)      |
|  Azure Database for MySQL         | 所有公共区域<br/> 所有政府区域<br/>所有中国区域      |  | GA <br/> [了解如何为 Azure Database for MySQL 创建专用终结点。](../mysql/concepts-data-access-security-private-link.md)     |
|  Azure Database for MariaDB         | 所有公共区域<br/> 所有政府区域<br/>所有中国区域     |  | GA <br/> [了解如何为 Azure Database for MariaDB 创建专用终结点。](../mariadb/concepts-data-access-security-private-link.md)      |

### <a name="integration"></a>集成

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|Azure 事件网格| 所有公共区域<br/> 所有政府区域       |  | GA   <br/> [了解如何为 Azure 事件网格创建专用终结点。](../event-grid/network-security.md) |
|Azure 服务总线 | 所有公共区域<br/>所有政府区域  | 在 Azure 服务总线的高级层受支持。 [针对层级进行选择](../service-bus-messaging/service-bus-premium-messaging.md) | GA   <br/> [了解如何为 Azure 服务总线创建专用终结点。](../service-bus-messaging/private-link-service.md)    |

### <a name="internet-of-things-iot"></a>物联网 (IoT)

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure IoT 中心 | 所有公共区域    |  | GA   <br/> [了解如何为 Azure IoT 中心创建专用终结点。](../iot-hub/virtual-network-support.md) |
|  Azure 数字孪生         | Azure 数字孪生支持的所有公共区域     |  | 预览 <br/> [了解如何为 Azure 数字孪生创建专用终结点。](../digital-twins/how-to-enable-private-link-portal.md)      |

### <a name="management-and-governance"></a>管理和治理

| 支持的服务 | 可用区域 | 其他注意事项 | 状态  |
| ------------ | ----------------| ------------| ----------------|
| Azure 自动化  | 所有公共区域<br/> 所有政府区域 |  | GA </br> [了解如何为 Azure 自动化服务创建专用终结点。](../automation/how-to/private-link-security.md)|
|Azure 备份 | 所有公共区域<br/> 所有政府区域   |  | GA <br/> [了解如何为 Azure 备份服务创建专用终结点。](../backup/private-endpoints.md)   |

### <a name="security"></a>安全性

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|  Azure Key Vault         | 所有公共区域<br/> 所有政府区域      |  | GA   <br/> [了解如何为 Azure Key Vault 创建专用终结点。](../key-vault/general/private-link-service.md)   |

### <a name="storage"></a>存储
|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure Blob 存储（包括 Data Lake Storage Gen2）       |  所有公共区域<br/> 所有政府区域       |  仅帐户类型常规用途 V2 支持 | GA <br/> [了解如何为 Blob 存储创建专用终结点。](tutorial-private-endpoint-storage-portal.md)  |
| Azure 文件 | 所有公共区域<br/> 所有政府区域      | |   GA <br/> [了解如何创建 Azure 文件存储网络终结点。](../storage/files/storage-files-networking-endpoints.md)   |
| Azure 文件同步 | 所有公共区域      | |   GA <br/> [了解如何创建 Azure 文件存储网络终结点。](../storage/file-sync/file-sync-networking-endpoints.md)   |
| Azure 队列存储       |  所有公共区域<br/> 所有政府区域       |  仅帐户类型常规用途 V2 支持 | GA <br/> [了解如何为队列存储创建专用终结点。](tutorial-private-endpoint-storage-portal.md) |
| Azure 表存储       |  所有公共区域<br/> 所有政府区域       |  仅帐户类型常规用途 V2 支持 | GA <br/> [了解如何为表存储创建专用终结点。](tutorial-private-endpoint-storage-portal.md)  |
| Azure Batch | 除以下区域外的所有公共区域：德国中部、德国东北部 <br/> 所有政府区域  | | GA <br/> [了解如何为 Azure Batch 创建专用终结点。](../batch/private-connectivity.md) |

### <a name="web"></a>Web
|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
| Azure SignalR | 美国东部、美国中南部、<br/>美国西部 2、所有中国区域      |  | 预览   <br/> [了解如何为 Azure SignalR 创建专用终结点。](../azure-signalr/howto-private-endpoints.md)   |
|Azure Web 应用 | 所有公共区域<br/> 中国北部 2 和东部 2    | 支持 PremiumV2、PremiumV3 或 Function Premium 计划  | GA   <br/> [了解如何为 Azure Web 应用创建专用终结点。](./tutorial-private-endpoint-webapp-portal.md)   |
|Azure 搜索 | 所有公共区域 <br/> 所有政府区域 | 在隐私模式下的服务中受支持 | GA   <br/> [了解如何为 Azure 搜索服务创建专用终结点。](../search/service-create-private-endpoint.md)    |
|Azure 中继 | 所有公共区域      |  | 预览 <br/> [了解如何为 Azure 中继服务创建专用终结点。](../azure-relay/private-link-service.md)  |

### <a name="private-link-service-with-a-standard-load-balancer"></a>使用标准负载均衡器的专用链接服务

|支持的服务  |可用区域 | 其他注意事项 | 状态  |
|:-------------------|:-----------------|:----------------|:--------|
|标准 Azure 负载均衡器后面的专用链接服务 | 所有公共区域<br/> 所有政府区域<br/>所有中国区域  | 在标准负载均衡器上受支持 | GA <br/> [了解如何创建专用链接服务。](create-private-link-service-portal.md) |

## <a name="next-steps"></a>后续步骤

详细了解 Azure 专用链接服务：
- [什么是 Azure 专用链接？](private-link-overview.md)
- [使用 Azure 门户创建专用终结点](create-private-endpoint-portal.md)
