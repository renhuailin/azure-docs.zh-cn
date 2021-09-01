---
title: 配置服务器参数 - Azure 门户 - Azure Database for MySQL 灵活服务器
description: 本文介绍如何使用 Azure 门户在 Azure Database for MySQL 灵活服务器中配置 MySQL 服务器参数。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 11/10/2020
ms.openlocfilehash: 2d02a4e0e63bd0861c0a414f3d56e9c62e19dc8a
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652777"
---
# <a name="configure-server-parameters-in-azure-database-for-mysql---flexible-server-using-the-azure-portal"></a>使用 Azure 门户在 Azure Database for MySQL 灵活服务器中配置服务器参数

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

你可以使用服务器参数管理 Azure Database for MySQL 灵活服务器配置。 创建服务器时，将使用默认值和推荐值配置服务器参数。  

本文介绍如何使用 Azure 门户查看和配置服务器参数。 Azure 门户上的服务器参数边栏选项卡显示可修改和不可修改的服务器参数。 不可修改的服务器参数为灰显。

>[!Note]
> 可在服务器级别全局更新服务器参数，只需使用 [Azure CLI](./how-to-configure-server-parameters-cli.md) 或 [Azure 门户](./how-to-configure-server-parameters-portal.md)即可。

## <a name="configure-server-parameters"></a>配置服务器参数

1. 登录到 [Azure 门户](https://portal.azure.com)，然后找到 Azure Database for MySQL 灵活服务器。
2. 在“设置”部分下，单击“服务器参数”，打开 Azure Database for MySQL 灵活服务器的“服务器参数”页 。
[:::image type="content" source="./media/how-to-server-parameters/azure-portal-server-parameters.png" alt-text="Azure 门户中的服务器参数页":::](./media/how-to-server-parameters/azure-portal-server-parameters.png#lightbox)
3. 找到需要调整的任何服务器参数。 查看“说明”列，了解用途和允许的值。
[:::image type="content" source="./media/how-to-server-parameters/3-toggle-parameter.png" alt-text="枚举下拉按钮":::](./media/how-to-server-parameters/3-toggle-parameter.png#lightbox)
4. 单击“保存”，保存更改。
[:::image type="content" source="./media/how-to-server-parameters/4-save-parameters.png" alt-text="保存或放弃更改":::](./media/how-to-server-parameters/4-save-parameters.png#lightbox)
5. 静态参数需要重启服务器才会生效。 如果修改静态参数，系统将提示你“立即重启”或“稍后重启” 。
[:::image type="content" source="./media/how-to-server-parameters/5-save-parameter.png" alt-text="保存静态参数时重启":::](./media/how-to-server-parameters/5-save-parameter.png#lightbox)
6. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。 
[:::image type="content" source="./media/how-to-server-parameters/6-reset-parameters.png" alt-text="全部重置为默认设置":::](./media/how-to-server-parameters/6-reset-parameters.png#lightbox)

## <a name="setting-non-modifiable-server-parameters"></a>设置不可修改的服务器参数

如果要更新的服务器参数不可修改，则可以选择性地使用 `init_connect` 在连接级别设置参数。 此项可为每个连接到服务器的客户端设置服务器参数。 

1. 在“设置”部分下，单击“服务器参数”，打开 Azure Database for MySQL 服务器的“服务器参数”页。
2. 搜索 `init_connect`
3. 在 value 列的 value 中添加服务器参数，格式为 `SET parameter_name=YOUR_DESIRED_VALUE`。

    例如，可以通过将 `init_connect` 设置为 `SET character_set_client=utf8;SET character_set_database=utf8mb4;SET character_set_connection=latin1;SET character_set_results=latin1;` 来更改服务器的字符集
4.  以保存更改。

>[!Note]
> `init_connect` 可用于在会话级别更改无需 SUPER 权限的参数。 若要验证是否可以使用 `init_connect` 设置参数，请执行 `set session parameter_name=YOUR_DESIRED_VALUE;` 命令，如果出现“拒绝访问；需要 SUPER 权限”错误，则无法使用“init_connect”设置参数。

## <a name="working-with-the-time-zone-parameter"></a>使用时区参数

### <a name="populating-the-time-zone-tables"></a>填充时区表

可以通过从 MySQL 命令行或 MySQL Workbench 等工具调用 `mysql.az_load_timezone` 存储过程，填充服务器上的时区表。

> [!NOTE]
> 如果正在运行 MySQL Workbench 中的 `mysql.az_load_timezone` 命令，可能需要先使用 `SET SQL_SAFE_UPDATES=0;` 关闭安全更新模式。

```sql
CALL mysql.az_load_timezone();
```

> [!IMPORTANT]
>应重启服务器，确保正确填充时区表。<!-- FIX ME To restart the server, use the [Azure portal](how-to-restart-server-portal.md) or [CLI](how-to-restart-server-cli.md).-->

要查看可用的时区值，请运行以下命令：

```sql
SELECT name FROM mysql.time_zone_name;
```

### <a name="setting-the-global-level-time-zone"></a>设置全局级时区

可以从 Azure 门户中的“服务器参数”页设置全局级时区。 下面将全局时区值设置为“美国/太平洋”。

[:::image type="content" source="./media/how-to-server-parameters/timezone.png" alt-text="设置时区参数":::](./media/how-to-server-parameters/timezone.png#lightbox)

### <a name="setting-the-session-level-time-zone"></a>设置会话级时区

可以通过从 MySQL 命令行或 MySQL Workbench 等工具运行 `SET time_zone` 命令来设置会话级时区。 以下示例将时区设置为“美国/太平洋”时区。

```sql
SET time_zone = 'US/Pacific';
```

若要了解[日期和时间函数](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html#function_convert-tz)，请参阅 MySQL 文档。

## <a name="next-steps"></a>后续步骤

- 如何配置 [Azure CLI 中的服务器参数](./how-to-configure-server-parameters-cli.md)
