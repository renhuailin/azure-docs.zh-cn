---
title: 快速入门：创建服务器 - Azure 门户 - Azure Database for PostgreSQL - 单个服务器
description: 在本快速入门指南中，你将使用 Azure 门户创建和管理 Azure Database for PostgreSQL 服务器。
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96492380"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>快速入门：使用 Azure 门户创建 Azure Database for PostgreSQL 服务器

用于 PostgreSQL 的 Azure 数据库是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 PostgreSQL 数据库。 本快速入门介绍如何创建单个 Azure Database for PostgreSQL 服务器并与其连接。

## <a name="prerequisites"></a>先决条件
需要一个 Azure 订阅。 如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="create-an-azure-database-for-postgresql-server"></a>创建 Azure Database for PostgreSQL 服务器
转到 [Azure 门户](https://portal.azure.com/)创建 Azure Database for PostgreSQL 单一服务器数据库。 搜索并选择 Azure Database for PostgreSQL 服务器。

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="查找 Azure Database for PostgreSQL。":::

1. 选择 **添加** 。

2. 在“创建 Azure Database for PostgreSQL”页面上，选择“单一服务器”。

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="选择“单一服务器”":::

3. 现在将以下内容输入“基本信息”窗体。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="显示创建单个服务器的基本选项卡的屏幕截图。":::

   |设置|建议的值|说明|
   |:---|:---|:---|
   |订阅|订阅名称|选择所需的 Azure 订阅。|
   |资源组| myresourcegroup| 新的资源组，或订阅中的现有资源组。|
   |服务器名称 |*mydemoserver*|用于标识用于 PostgreSQL 的 Azure 数据库服务器的唯一名称。 域名 postgres.database.azure.com 将追加到你提供的服务器名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 必须包含 3 到 63 个字符。|
   |数据源 | 无 | 选择“无”，从头开始创建新的服务器  。 仅当从现有服务器的异地备份进行还原时，才选择“备份”。|
   |管理员用户名 |*myadmin*| 输入服务器管理员用户名。 它不能以 pg_ 开头，并且不允许以下值：azure_superuser、azure_pg_admin、admin、administrator、root、guest 或 public       。|
   |密码 |你的密码| 服务器管理员用户的新密码。 密码必须包含以下三个类别的 8 到 128 个字符：英文大写字母、英文小写字母、数字（0 到 9）和非字母数字字符（例如 !、$、#、%）。|
   |位置|你所需的位置| 从下拉列表中选择一个位置。|
   |版本|最新主版本| 除非另有特定的要求，否则为最新 PostgreSQL 主版本。|
   |计算 + 存储 | 使用默认值| 默认定价层是“常规用途”，其中包含 4 个 vCore 和 100 GB 存储  。 备份保留期设置为 7 天，并提供“异地冗余”备份选项 。<br/>了解[定价](https://azure.microsoft.com/pricing/details/postgresql/server/)并按需更新默认值。|


   > [!NOTE]
   > 如果轻量级计算和 I/O 足以满足工作负荷要求，请考虑使用“基本”定价层。 请注意，在“基本”定价层中创建的服务器以后不能扩展到“常规用途”或“内存优化”定价层。

5. 选择“查看 + 创建”，查看所选内容。 选择“创建”以预配服务器。 此操作可能需要几分钟时间。
    > [!NOTE]
    > 将创建一个空数据库“postgres”。 你还会发现一个 azure_maintenance 数据库，用于将托管服务进程与用户操作分开。 你不能访问 azure_maintenance 数据库。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="成功部署。":::

[存在问题？请告诉我们。](https://aka.ms/postgres-doc-feedback)

## <a name="configure-a-firewall-rule"></a>配置防火墙规则
默认情况下，你创建的服务器不可公开访问。 需要向你的 IP 地址授予权限。 请转到 Azure 门户中的服务器资源，然后从左侧菜单中为服务器资源选择“连接安全性”。 如果你不确定如何找到资源，请参阅[打开资源](../azure-resource-manager/management/manage-resources-portal.md#open-resources)。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="显示连接安全的防火墙规则的屏幕截图。":::

选择“添加当前客户端 IP 地址”，然后选择“保存” 。 你可以添加更多 IP 地址或提供一个 IP 范围，从该范围中的 IP 地址连接到服务器。 有关详细信息，请参阅 [Azure Database for PostgreSQL 中的防火墙规则](./concepts-firewall-rules.md)。

> [!NOTE]
> 若要避免连接问题，请检查你的网络是否允许通过端口 5432 进行出站通信。 Azure Database for PostgreSQL 使用该端口。

[存在问题？请告诉我们。](https://aka.ms/postgres-doc-feedback)

## <a name="connect-to-the-server-with-psql"></a>通过 psql 连接到服务器

可以使用 [psql](http://postgresguide.com/utilities/psql.html) 或 [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html)，它们都是热门 PostgreSQL 客户端。 在本快速入门中，我们将在 Azure 门户内的 [Azure Cloud Shell](../cloud-shell/overview.md) 中使用 psql 进行连接。

1. 通过服务器的“概述”部分，记录新创建的服务器的服务器名称、服务器管理员登录名、密码和订阅 ID。
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="获取连接信息。":::


2. 通过选择左上角的图标在门户中打开 Azure Cloud Shell。

   > [!NOTE]
   > 如果你是第一次打开 Cloud Shell，你将看到创建资源组和存储帐户的提示。 这是一个一次性步骤，将来会针对所有会话自动附加。

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="显示服务器信息和用于打开 Azure Cloud Shell 的图标的屏幕截图。":::

3. 在 Azure Cloud Shell 终端中运行以下命令。 将值替换为实际的服务器名称和管理员用户登录名。 以管理员用户的身份使用空数据库 postgres，格式如下：`<admin-username>@<servername>`。

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   下图显示了 Cloud Shell 终端中的该体验过程：

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. 在同一个 Azure Cloud Shell 终端中，创建一个名为“guest”的数据库。

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. 切换为与新创建的“guest”数据库的连接。

   ```bash
   \c guest
   ```
6. 键入 `\q`，再按 Enter 键关闭 psql。

[存在问题？请告诉我们。](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>清理资源
现在已成功在资源组中创建了 Azure Database for PostgreSQL 服务器。 如果将来不再需要这些资源，可以通过删除资源组或 PostgreSQL 服务器来删除它们。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户中，搜索并选择“资源组”。
2. 在资源组列表中，选择你的资源组的名称。
3. 在资源组的“概述”页面中，选择“删除资源组” 。
4. 在确认对话框中，输入资源组的名称，然后选择“删除”。

若要删除服务器，请在服务器的“概述”页面上选择“删除”按钮 ：

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="显示用于删除服务器的按钮的屏幕截图。":::

[存在问题？请告诉我们。](https://aka.ms/postgres-doc-feedback)

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [使用导出和导入功能迁移数据库](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [设计数据库](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[找不到要查找的内容？请告诉我们。](https://aka.ms/postgres-doc-feedback)