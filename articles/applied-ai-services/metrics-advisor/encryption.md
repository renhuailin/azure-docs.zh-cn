---
title: 指标顾问服务加密
titleSuffix: Azure Cognitive Services
description: 指标顾问服务静态数据加密。
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: mbullwin
ms.openlocfilehash: 03f6a30bbe857b59c3fa8a6fa668bdac7cb681b1
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114341356"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>指标顾问服务静态数据加密

将数据保存到云时，指标顾问服务会自动加密数据。 指标顾问服务加密可保护数据，并帮助组织履行在安全性与合规性方面做出的承诺。

[!INCLUDE [cognitive-services-about-encryption](../../cognitive-services/includes/cognitive-services-about-encryption.md)]

指标顾问支持使用 BYOS（自带存储）进行 CMK 和双重加密。 

## <a name="steps-to-create-a-metrics-advisor-with-byos"></a>使用 BYOS 创建指标顾问的步骤

### <a name="step1-create-an-azure-database-for-postgresql-and-set-admin"></a>步骤 1. 创建 Azure Database for PostgreSQL 并设置管理员

- 创建用于 PostgreSQL 的 Azure 数据库

    登录到该Azure 门户并创建 Azure Database for PostgreSQL 的资源。 要注意的几个事项：

    1. 请选择“单个服务器”部署选项。 
    2. 选择“数据源”时，请指定为“无”。
    3. 对于“位置”，请确保在与指标顾问资源相同的位置创建。
    4. “版本”应该设置为 11。 
    5. “计算 + 存储”应选择至少具有 32 个 vCore 的“内存优化”SKU。
    
    ![创建用于 PostgreSQL 的 Azure 数据库](media/cmk-create.png)

- 为新建的 PG 设置 Active Directory 管理员

    成功创建 Azure Database for PostgreSQL 之后。 转到新创建的 Azure PG 资源的资源页。 选择“Active Directory 管理员”选项卡，将自己设置为管理员。


### <a name="step2-create-a-metrics-advisor-resource-and-enable-managed-identity"></a>步骤 2. 创建一个指标顾问资源并启用托管标识

- 在 Azure 门户中创建指标顾问资源

    再次转到 Azure 门户搜索“指标顾问”。 创建指标顾问时，切记以下事项：

    1. 选择与创建 Azure Database for PostgreSQL 相同的“区域”。 
    2. 将“自带存储”标记为“是”，然后在下拉列表中选择刚才创建的 Azure Database for PostgreSQL。

- 为指标顾问启用托管标识

    创建“指标顾问”资源后，选择“身份”并将“状态”设置为“开启”以启用托管标识。

- 获取托管标识的应用程序 ID

    转到“Azure Active Directory”，然后选择“企业应用程序”。 将“应用程序名称”更改为“托管标识”，复制指标顾问的资源名称，然后搜索。 然后可以从查询结果中查看“应用程序 ID”，复制 ID。

### <a name="step3-grant-metrics-advisor-access-permission-to-your-azure-database-for-postgresql"></a>步骤 3。 授予指标顾问访问你的 Azure Database for PostgreSQL 的权限

- 为 Azure Database for PostgreSQL 上的托管标识授予“所有者”角色

- 设置防火墙规则

    1. 将“允许访问 Azure 服务”设置为“是”。 
    2. 添加用于登录到 Azure Database for PostgreSQL 的客户端 IP 地址。

- 获取资源类型为“https://ossrdbms-aad.database.windows.net”的帐户的访问令牌。 访问令牌是通过你的帐户登录到 Azure Database for PostgreSQL 时所需的密码。 使用 `az` 客户端的示例：

   ```
   az login
   az account get-access-token --resource https://ossrdbms-aad.database.windows.net
   ```

- 获取令牌后，使用此令牌登录到 Azure Database for PostgreSQL。 将“servername”替换为可在 Azure Database for PostgreSQL 的“概述”部分中找到的名称。

   ```
   export PGPASSWORD=<access-token>
   psql -h <servername> -U <adminaccount@servername> -d postgres
   ```

- 登录后，执行以下命令，向指标顾问授予对 Azure Database for PostgreSQL 的访问权限。 将“appid”替换为在步骤 2 中获取的 appid。

   ```
   SET aad_validate_oids_in_tenant = off;
   CREATE ROLE metricsadvisor WITH LOGIN PASSWORD '<appid>' IN ROLE azure_ad_user;
   ALTER ROLE metricsadvisor CREATEDB;
   GRANT azure_pg_admin TO metricsadvisor;
   ```

通过完成上述所有步骤，你已成功创建了一个支持 CMK 的指标顾问资源。 等待几分钟，直到指标顾问变为可访问。

## <a name="next-steps"></a>后续步骤

* [指标顾问服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)
* [详细了解 Azure 密钥保管库](../../key-vault/general/overview.md)