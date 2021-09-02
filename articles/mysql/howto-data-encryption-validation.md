---
title: 如何确保“Azure Database for MySQL - 数据加密”的验证
description: 了解如何验证使用客户管理密钥的“Azure Database for MySQL - 数据加密”的加密。
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/28/2020
ms.openlocfilehash: 36cdf60dd318250efbfe7386fa9eb172091afde3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652222"
---
# <a name="validating-data-encryption-for-azure-database-for-mysql"></a>验证 Azure Database for MySQL 的数据加密

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文将帮助你验证 Azure Database for MySQL 的使用客户管理密钥的数据加密是否按预期工作。

## <a name="check-the-encryption-status"></a>检查加密状态

### <a name="from-portal"></a>从门户

1. 如果要验证客户的密钥是否用于加密，请按照以下步骤进行操作：

    * 在 Azure 门户中，导航到“Azure Key Vault” -> “密钥”
    * 选择用于服务器加密的密钥。
    * 将密钥的状态“已启用”设置为“否”。
  
       一段时间（约 15 分钟）后，Azure Database for MySQL 服务器的“状态”应为“无法访问”。 针对服务器执行的任何 I/O 操作都将失败，这将证实服务器确实已使用客户密钥加密并且该密钥当前无效。
    
       为了使服务器“可用”，可以重新验证该密钥。 
    
    * 将 Key Vault 中密钥的状态设置为“是”。
    * 在服务器“数据加密”上，选择“重新验证密钥”。
    * 密钥重新验证成功后，服务器“状态”更改为“可用”。

2. 在 Azure 门户上，如果可以确保设置了加密密钥，则使用 Azure 门户中使用的客户密钥对数据进行加密。

  :::image type="content" source="media/concepts-data-access-and-security-data-encryption/byok-validate.png" alt-text="访问策略概述":::

### <a name="from-cli"></a>从 CLI

1. 我们可以使用 az CLI 命令验证将用于 Azure Database for MySQL 服务器的密钥资源。

    ```azurecli-interactive
   az mysql server key list --name  '<server_name>'  -g '<resource_group_name>'
    ```

    对于没有设置数据加密的服务器，此命令会生成空集 []。

### <a name="azure-audit-reports"></a>Azure 审核报告

此外，也可以审阅[审核报告](https://servicetrust.microsoft.com)，其中提供关于数据保护标准和法规要求合规性的信息。

## <a name="next-steps"></a>后续步骤

若要详细了解数据加密，请参阅[使用客户管理的密钥进行 Azure Database for MySQL 数据加密](concepts-data-encryption-mysql.md)。