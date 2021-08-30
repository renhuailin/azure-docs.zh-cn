---
title: 配置服务器参数 - Azure 门户 - Azure Database for PostgreSQL 灵活服务器
description: 本文介绍如何通过 Azure 门户在 Azure Database for PostgreSQL 灵活服务器中配置 Postgres 参数。
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: how-to
ms.date: 08/04/2021
ms.openlocfilehash: 1ca1501f790f914412fa61fce5ceed5ba45130b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736385"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>通过 Azure 门户在 Azure Database for PostgreSQL 灵活服务器中配置服务器参数 
可以通过 Azure 门户列出、显示和更新 Azure Database for PostgreSQL 的配置参数。

## <a name="prerequisites"></a>必备条件
若要逐步执行本操作方法指南，需要：
- [Azure Database for PostgreSQL - 灵活服务器](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>查看和编辑参数
1. 打开 [Azure 门户](https://portal.azure.com)。

2. 选择你的 Azure Database for PostgreSQL 服务器。

3. 在“设置”部分下，选择“服务器参数”。   该页显示了参数的列表及其值和说明。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="参数概述页":::

4. 选择 **下拉** 按钮查看 client_min_messages 等枚举类型的参数的可能值。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="枚举下拉按钮":::

5. 选择“i”（信息）按钮或将鼠标悬停于其上，查看 cpu_index_tuple_cost 等数字参数的可能值范围。 
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="信息按钮":::

6. 如果需要，可使用 **搜索框** 缩小特定参数的搜索范围。 搜索根据参数的名称和说明执行。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="搜索结果":::

7. 更改想要调整的参数值。 在此会话中所做的更改将以紫色突出显示。 更改值之后，可选择“保存”。  也可以放弃所做的更改  。
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="保存或放弃更改":::

8. 保存参数的新值后，随时可以通过选择“全部重置为默认设置”，将所有设置还原为默认值。 
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="全部重置为默认设置":::

## <a name="working-with-time-zone-parameters"></a>使用时区参数
如果打算在 PostgreSQL 中使用日期和时间数据，则需要确保为你的位置设置正确的时区。 所有需要显示时区的日期和时间均以 UTC 形式存储在 Postgres 内部。 这些日期和时间会转换为 TimeZone 服务器参数指定的时区中的当地时间，然后再显示给客户端。  如上所述，此参数可以在“服务器参数”页面编辑。 PostgreSQL 允许以三种不同的形式指定时区：
1. 完整的时区名称，例如 America/New_York。 识别的时区名称列在 [pg_timezone_names](https://www.postgresql.org/docs/9.2/view-pg-timezone-names.html) 视图中。  
   在 psql 中查询此视图并获取时区名称列表的示例：
   <pre>select name FROM pg_timezone_names LIMIT 20;</pre>

   应看到如下结果集：

   <pre>
            name
        -----------------------
        GMT0
        Iceland
        Factory
        NZ-CHAT
        America/Panama
        America/Fort_Nelson
        America/Pangnirtung
        America/Belem
        America/Coral_Harbour
        America/Guayaquil
        America/Marigot
        America/Barbados
        America/Porto_Velho
        America/Bogota
        America/Menominee
        America/Martinique
        America/Asuncion
        America/Toronto
        America/Tortola
        America/Managua
        (20 rows)
    </pre>
   
2. 时区缩写，例如 PST。 此类规范仅定义相对于 UTC 的特定偏移量，而完全时区名称则意味着一组夏令时转换日期规则。 识别的缩写列在 [pg_timezone_abbrevs 视图中](https://www.postgresql.org/docs/9.4/view-pg-timezone-abbrevs.html) 在 psql 中查询此视图并获取时区缩写的示例：

   <pre> select abbrev from pg_timezone_abbrevs limit 20;</pre>

    应看到如下结果集：

     <pre>
        abbrev|
        ------+
        ACDT  |
        ACSST |
        ACST  |
        ACT   |
        ACWST |
        ADT   |
        AEDT  |
        AESST |
        AEST  |
        AFT   |
        AKDT  |
        AKST  |
        ALMST |
        ALMT  |
        AMST  |
        AMT   |
        ANAST |
        ANAT  |
        ARST  |
        ART   |
    </pre>

3. 除了时区名称和缩写，PostgreSQL 还将接受 STDoffset 或 STDoffsetDST 形式的 POSIX 样式的时区规范，其中 STD 是区域缩写，offset 是 UTC 以西以小时为单位的偏移数值，DST 是一个可选的夏令时区域缩写，假定它代表比给定偏移量提前一个小时。 
   

## <a name="next-steps"></a>后续步骤
学习内容：
- [Azure Database for PostgreSQL 中的服务器参数概述](concepts-server-parameters.md)
- [通过 CLI 配置 Azure Database for PostgreSQL 灵活服务器参数](howto-configure-server-parameters-using-cli.md)
  