---
title: Azure Stack Edge 的资源提供程序 API 配置文件版本 | Microsoft Docs
description: 了解 Azure Stack Edge 中的配置文件支持的 Azure 资源管理器 API 版本。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2021
ms.author: alkohli
ms.openlocfilehash: 485b1f8dccf104a9d31abb9aa79fd1ea4eb3940a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965338"
---
# <a name="resource-provider-api-profile-versions-for-azure-stack-edge"></a>Azure Stack Edge 的资源提供程序 API 配置文件版本


[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

可以在本文中找到 Azure Stack Edge 使用的每个 API 配置文件的资源提供程序和版本号。 本文中的表格列出了每个资源提供程序支持的版本以及配置文件的 API 版本。 每个资源提供程序包含一组资源类型和特定的版本号。

API 配置文件使用三个命名约定：

- **latest**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**


## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>2019-03-01-hybrid 配置文件概述

|资源提供程序                                   |API 版本 |
|----------------------------------------------------|------------|
|Microsoft.Compute                                   |2017-12-01  |
|Microsoft.Network                                   |2017-10-01<br>VPN 网关将为 2017-10-01 |
|Microsoft.Storage（数据平面）                      |2019-07-07  |
|Microsoft.Storage（控制平面）                   |2019-06-01  |
|Microsoft.Resources（Azure 资源管理器本身） |2020-06-01  |
|Microsoft.Authorization（策略操作）         |2016-09-01  |

有关 API 配置文件中提供程序的每种资源类型的版本列表，请参阅 [2019-03-01-hybrid 配置文件的详细信息](/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions?view=azs-2008&preserve-view=true#details-for-the-2019-03-01-hybrid-profile)。

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>2019-03-01-hybrid 配置文件的详细信息

### <a name="microsoftcompute"></a>Microsoft.Compute

使用 Azure 计算 API 可通过编程方式访问虚拟机及其支持资源。 有关详细信息，请参阅 [Azure 计算](/rest/api/compute/)。

|资源类型               |API 版本 |
|----------------------------|------------|
|位置                   |2017-12-01  |
|位置/VM 大小           |2017-12-01  |
|虚拟机            |2017-12-01  |
|虚拟机/扩展 |2017-12-01  |

### <a name="microsoftnetwork"></a>Microsoft.Network

操作调用结果是可用网络云操作列表的表示形式。 有关详细信息，请参阅[操作 REST API](/rest/api/operation/)。

|资源类型     |API 版本|
|-------------------|------------|
|网络接口 |2017-10-01  |
|虚拟网络   |2017-10-01  |

### <a name="microsoftresources"></a>Microsoft.Resources

使用 Azure 资源管理器可以部署和管理 Azure 解决方案的基础结构。 可在资源组中组织相关的资源，并使用 JSON 模板部署资源。 有关使用资源管理器部署和管理资源的简介，请参阅 [Azure 资源管理器概述](../azure-resource-manager/management/overview.md)。

|资源类型                        |API 版本|
|--------------------------------------|------------|
|部署                           |2020-06-01  |
|部署/操作                |2020-06-01  |
|链接                                 |2020-06-01  |
|位置                             |2020-06-01  |
|Operations                            |2020-06-01  |
|提供程序                             |2020-06-01  |
|ResourceGroups                        |2020-06-01  |
|资源                             |2020-06-01  |
|订阅                         |2018-09-01  |
|订阅/位置               |2018-09-01  |
|订阅/操作结果        |2020-06-01  |
|订阅/提供程序               |2020-06-01  |
|订阅/资源组          |2020-06-01  |
|订阅/资源组/资源|2020-06-01  |
|订阅/资源               |2020-06-01  |
|订阅/标记名称                |2020-06-01  |
|订阅/标记名称/标记值      |2020-06-01  |
|租户                               |2018-09-01  |

### <a name="microsoftstorage"></a>Microsoft.Storage

使用存储资源提供程序 (SRP) 可通过编程方式管理存储帐户和密钥。 有关详细信息，请参阅 [Azure Storage Resource Provider REST API reference](/rest/api/storagerp/)（Azure 存储资源提供程序 REST API 参考）。

|资源类型       |API 版本|
|---------------------|------------|
|CheckNameAvailability|2019-06-01  |
|位置            |2019-06-01  |
|位置/配额     |2019-06-01  |
|Operations           |2019-06-01  |
|存储帐户      |2019-06-01  |
|用途               |2019-06-01  |

## <a name="next-steps"></a>后续步骤

- [通过 Windows PowerShell 管理 Azure Stack Edge Pro GPU 设备](azure-stack-edge-gpu-connect-powershell-interface.md)