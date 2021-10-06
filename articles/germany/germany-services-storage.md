---
title: Azure 德国存储服务 |Microsoft Docs
description: 本主题将对 Azure 德国的存储服务进行比较。 此外，你还会发现其他相关信息。
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfdocs
ms.openlocfilehash: 840ac16914196a238bcf8fdaaa518799dcb104e9
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129275024"
---
# <a name="azure-germany-storage-services"></a>Azure 德国存储服务

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

## <a name="storage"></a>存储
有关 Azure 存储及其使用方法的详细信息，请参阅[存储全局文档](../storage/index.yml)。

复制存储在 Azure 存储中的数据，确保高可用性。 对于异地冗余存储和读取访问异地冗余存储，Azure 会在配对区域之间复制数据。 对于 Azure 德国，这些配对区域为：

| 主要区域 | 次要（配对）区域 |
| --- | --- |
| 德国中部 | 德国东北部 |
| 德国东北部 | 德国中部 |

数据复制会使数据保留在德国境内。 主要和次要区域配对可确保数据中心之间的必要距离，确保发生区域范围故障或灾难时的可用性。 对于异地冗余、高可用性存储，应在创建新的存储帐户时选择异地冗余存储或读取访问异地冗余存储。  

存储服务加密可保护 Azure 存储帐户中的静态数据。 启用该功能后，Azure 会在保存到存储之前自动加密数据。 使用 256 位 AES 加密对数据进行加密。 存储服务加密支持加密块 blob、追加 blob 和页 blob。

### <a name="storage-service-availability-by-azure-germany-region"></a>存储 Azure 德国地区的服务可用性

| 服务 | 德国中部 | 德国东北部 |
| --- | --- | --- |
| [Blob 存储](../storage/common/storage-introduction.md#blob-storage) |GA |GA |
| [Azure 文件](../storage/common/storage-introduction.md#azure-files) | GA | GA |
| [表存储](../storage/common/storage-introduction.md#table-storage) |GA  |GA |
| [队列存储](../storage/common/storage-introduction.md#queue-storage) |GA | GA |
| [冷/热 Blob 存储层](../storage/blobs/access-tiers-overview.md) |GA |GA |
| [存储服务加密](../storage/common/storage-service-encryption.md) |GA |GA |
| 导入/导出 |NA |NA |
| StorSimple |NA |NA |

### <a name="variations"></a>变体
Azure 德国中的存储帐户 URL 不同于全球 Azure 中的存储帐户 URL：

| 服务类型 | 全球 Azure | Azure 德国 |
| --- | --- | --- |
| Blob 存储 | *.blob.core.windows.net | *.blob.core.cloudapi.de |
| Azure 文件存储 | *.file.core.windows.net | *.file.core.cloudapi.de | 
| 队列存储 | *.queue.core.windows.net | *.queue.core.cloudapi.de |
| 表存储 | *.table.core.windows.net | *.table.core.cloudapi.de |

> [!NOTE]
> 所有脚本和代码都需要考虑适当的终结点。 有关详细信息，请参阅[配置 Azure 存储连接字符串](../storage/common/storage-configure-connection-string.md)。 
>
>

有关 API 的详细信息，请参阅[云存储帐户构造函数](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.-ctor)。

在这些重载中使用的终结点后缀为 core.cloudapi.de。

> [!NOTE]
> 如果错误 53（“找不到网络路径”）在[装载文件共享](../storage/files/storage-dotnet-how-to-use-files.md)时返回，则防火墙可能会阻止出站端口。 尝试将文件共享装载到与存储帐户相同的 Azure 订阅中的虚拟机上。
>
>


## <a name="next-steps"></a>后续步骤
有关补充信息和更新，请订阅 [Azure 德国博客](/archive/blogs/azuregermany/)。