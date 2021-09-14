---
title: 使用 Azure 免费帐户免费试用 Azure Database for PostgreSQL 灵活服务器
description: 有关如何使用 Azure 免费帐户免费部署 Azure Database for PostgreSQL 灵活服务器的指导。
author: shreyaaithal
ms.author: shaithal
ms.service: postgresql
ms.topic: how-to
ms.date: 08/23/2021
ms.custom: template-how-to
ms.openlocfilehash: 38a1e264d3febfc114df253a24479d9580c86bb5
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123538356"
---
# <a name="use-an-azure-free-account-to-try-azure-database-for-postgresql---flexible-server-for-free"></a>使用 Azure 免费帐户免费试用 Azure Database for PostgreSQL 灵活服务器

Azure Database for PostgreSQL 灵活服务器（预览版）是一项托管服务，用于在云中运行、管理和缩放高度可用的 PostgreSQL 数据库。 利用 Azure 免费帐户，可免费使用灵活服务器 12 个月，其中每月的限制如下 ：
- 750 小时的可突发 B1MS 实例，时长足以每月连续运行一个数据库实例 。
- 32 GB 存储和 32 GB 备份存储。

本文介绍如何使用 [Azure 免费帐户](https://azure.microsoft.com/free/)免费创建和使用灵活服务器。 

> [!IMPORTANT]
> Azure Database for PostgreSQL 灵活服务器当前以公共预览版提供。


## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

- 一个 Azure 免费帐户。 如果没有，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。 


## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>创建 Azure Database for PostgreSQL 灵活服务器

1. 使用 Azure 免费帐户登录到 [Azure 门户](https://portal.azure.com/)。
    
    默认视图是服务仪表板。

1. 若要创建 PostgreSQL 灵活服务器数据库，请搜索并选择“Azure Database for PostgreSQL 服务器”：

    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql.png" alt-text="显示如何搜索并选择 Azure Database for PostgreSQL 的屏幕截图。":::


    此外，你还可搜索并导航到“免费服务”，然后从列表中选择 “Azure Database for PostgreSQL”磁贴 ：


    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-all-postgresql.png" alt-text="显示 Azure 门户上所有免费服务的列表的屏幕截图，其中突出显示 PostgreSQL。":::

1. 选择“创建”。

1. 在“选择 Azure Database for MySQL 部署选项”页面上，选择“灵活服务器(预览版)” 。
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/select-postgresql-deployment-option.png" alt-text="显示要选择的灵活服务器部署选项的屏幕截图。":::

1. 输入新的灵活服务器的基本设置。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/basic-settings-postgresql.png" alt-text="显示用于创建灵活服务器的基本设置的屏幕截图。":::

    |设置    |建议的值    |说明 |
    |-------|------|------|
    |订阅   |订阅名称 |选择免费试用版 Azure 订阅。|
    |资源组 |你的资源组    |输入新资源组，或订阅中的现有资源组。|
    |服务器名称    |mydemoserver-pgsql |指定用于标识灵活服务器的唯一名称。 域名 postgres.database.azure.com 附加到提供的服务器名称。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 它必须包含 3 到 63 个字符。 |
    |区域 |离用户最近的区域   |从列表中选择一个位置，最好是离用户最近的位置。|
    |工作负荷类型  |开发    |对于免费试用版，请选择“开发”工作负载。 对于生产工作负载，可选择“小/中”或“大”，具体取决于你的要求。 |
    |可用性区域  |无首选项  |如果托管在 Azure VM 上的应用程序（虚拟机规模集或 AKS 实例）是在特定的可用性区域中预配的，则可在同一可用性区域中创建灵活服务器。 归置应用程序和数据库，通过跨区域降低网络延迟来提高性能。 如果选择“无首选项”，则系统将为你选择默认可用性区域。|
    |PostgreSQL 版本 |最新主版本   |除非另有特定的要求，否则请使用最新 PostgreSQL 主版本。|
    |高可用性  |默认    |将“高可用性”选项保持在未选中状态。|
    |管理员用户名 |myadmin    |连接到服务器时创建需要使用的登录帐户。 管理员用户名不能以 pg_开头，且不能是 azure_superuser、azure_pg_admin、admin、administrator、root、guest 或 public  。 |
    |密码   |你的密码  |指定服务器管理员帐户的密码。 密码必须包含 8 到 128 个字符。 密码还必须包含以下四类中的三类字符：英文大写字母、英文小写字母、数字（0 到 9）和非字母数字字符（!、$、#、% 等）。|

1. 对于“计算 + 存储”设置，请保留在选择“开发”工作负载类型时填充的默认值 。 
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-default-postgresql.png" alt-text="显示“计算 + 存储”设置的默认值的屏幕截图。":::

    选择“配置服务器”，来查看和自定义“计算 + 存储”设置 。
    
    请确保选择“可突发 B1MS实例(1-2 个 vCore)”，指定包括小于或等于 32 GB 的存储，并保留剩余选项的默认设置。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/compute-storage-postgresql.png" alt-text="显示“计算和 + 存储”的“配置服务器”边栏选项卡的屏幕截图，用于选择 B1MS SKU 和 32GB 存储。":::

    选择“保存”以继续配置。

1.  选择“网络”选项卡来配置访问服务器的方式。

    Azure Database for MySQL 灵活服务器提供两种连接方法：
    - 公共访问（允许的 IP 地址）
    - 专用访问（VNet 集成）
    
    使用公共访问时，对服务器的访问仅限于已添加到防火墙规则中的允许的 IP 地址。 除非创建了规则以打开特定 IP 地址或范围的防火墙，否则此方法会阻止外部应用程序和工具连接到服务器和服务器上的任何数据库。
    
    使用专用访问（VNet 集成）时，对服务器的访问仅限于虚拟网络。 有关连接方法的详细信息，请参阅网络概述。
    
    在本教程中，启用公共访问以连接到服务器。

1. 在“网络”选项卡上，对于“连接方法”，选择“公共访问”  。

1. 为配置“防火墙规则”，请选择“添加当前客户端 IP 地址”。 
    
    :::image type="content" source="media/how-to-deploy-on-azure-free-account/networking-postgresql.png" alt-text="屏幕截图显示了要选择的网络选项，并突出显示了“添加当前客户端 IP 地址”的按钮。":::

1. 选择“查看 + 创建”，查看你的灵活服务器配置。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/review-create-postgresql.png" alt-text="屏幕截图显示“查看 + 创建”边栏选项卡。":::

    >[!IMPORTANT]
    >从 Azure 免费帐户创建灵活服务器实例时，“每月估计成本”会显示在“计算 + 存储：成本摘要”边栏选项卡和“查看 + 创建”选项卡中。但是，只要使用的是 Azure 免费帐户，并且免费服务使用量未超出每月限制（若要查看使用量信息，请参阅下面的[监视和跟踪免费服务使用量](#monitor-and-track-free-services-usage)部分），就不需要为该服务付费。 目前，我们正在努力改进免费服务的成本摘要体验。 

1. 选择“创建”以预配服务器。

    预配可能需要几分钟时间

1.  在工具栏上，选择“通知”（钟形图标）来监视部署过程。

    完成部署后，可选择“固定到仪表板”，在 Azure 门户仪表板上为此灵活服务器创建一个磁贴。 此磁贴是服务器“概述”页的快捷方式。 选择“转到资源”时，会打开此服务器的“概述”页 。

    默认情况下，会在服务器下创建 **postgres** 数据库。 postgres 是供用户、实用工具和第三方应用程序使用的默认数据库。 （另一个默认数据库是 **azure_maintenance**， 其功能是将托管服务进程与用户操作分开。 你不能访问此数据库。）


## <a name="connect-and-query"></a>连接和查询

现在你已在资源组中创建了一个 Azure Database for PostgreSQL 灵活服务器，接下来可按照以下连接和查询快速入门连接到服务器和查询数据库：
- [psql](quickstart-create-server-portal.md#connect-to-the-postgresql-database-using-psql)
- [Azure CLI](connect-azure-cli.md)
- [Python](connect-python.md)
- [Java](connect-java.md)
- [.NET](connect-csharp.md)
- [在 VNET 中连接到服务器](quickstart-create-connect-server-vnet.md)


## <a name="monitor-and-track-free-services-usage"></a>监视和跟踪免费服务使用情况

我们不会对 Azure 免费帐户中包含的 Azure Database for PostgreSQL 灵活服务器免费服务收费，除非超出这些免费服务的限值。 若要保持在限值内，请使用Azure 门户跟踪和监视免费服务的使用情况。

1.  在 Azure 门户中，搜索“订阅”，然后选择 Azure 免费帐户 -“免费试用版”订阅 。
1.  在概述页面上，向下滚动以显示“按使用情况排名的热门免费服务”，然后选择“查看所有免费服务”  。

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-usage-overview.png" alt-text="屏幕截图显示了免费试用版订阅概述页面，并突出显示了“查看所有免费服务”。":::

1. 找到与 Azure Database for PostgreSQL 灵活服务器相关的下列计量，来跟踪使用情况：

    |计量  |说明    |每月限制|
    |-------|---------|-------|
    |Azure Database for PostgreSQL、灵活服务器可突发 BS 系列计算、B1MS   |跟踪计算使用情况，以运行小时数表示    |750 小时/月 - 可突发 B1MS 计算层|
    |Azure Database for PostgreSQL、灵活服务器存储、存储的数据    |跟踪预配的存储的数据，以每月使用的 GB 大小表示  |32 GB/月|

    :::image type="content" source="media/how-to-deploy-on-azure-free-account/free-services-tracking.png" alt-text="显示 Azure 门户上所有免费服务的“查看和跟踪使用情况信息”边栏选项卡的屏幕截图。":::

    - 计量： 列出耗用服务的度量单位。
    - 使用情况/限值： 当前月份的测定仪使用情况和限值。
    - 状态： 服务的使用状态。 根据使用情况，可能会具有以下状态之一：
    - 未使用： 未使用测定仪或测定仪的使用情况尚未提交至计费系统。
    - 超出限值的日期\<Date\>： 超出测定仪限值的日期 \<Date\>。
    - 不可能超过： 不太可能超过测定仪的限值。
    - 超出限值的日期\<Date\>： 可能会于 \<Date\> 超过测定仪限值。

    >[!IMPORTANT]
    >通过 Azure 免费帐户，你还可获得 200 美元的赠金，可在 30 天内使用。 在此期间，超出每月免费服务量的任何使用额都将从此赠金中扣除。
    >在前 30 天结束时或你用完 200 美元的赠金后（以先发生者为准），你只需为超出每月免费服务量的使用内容付费。 若要在 30 天后继续获取免费服务，请移到即用即付定价。 如果不转为即用即付，则不能购买超出 200 美元赠金的 Azure 服务，最终你的帐户和服务将被禁用。
    >有关详细信息，请参阅 [Azure 免费帐户常见问题解答](https://azure.microsoft.com/free/free-account-faq/)。


## <a name="clean-up-resources"></a>清理资源
    
如果将灵活服务器用于开发工作负载、测试工作负载或可预测的限时生产工作负载，请按需启动和停止服务器来优化使用情况。 停止服务器后，服务器将保持该状态七天（除非提前重启它）。 有关详细信息，请参阅“启动/停止服务器以降低总拥有成本 (TCO)”。 灵活服务器停止后，将不产生计算使用量，但仍会考虑存储使用量。

另外，如果将来不再需要这些资源，可通过删除资源组来删除它们，或者可只删除 PostgreSQL 服务器。

- 若要删除资源组，请完成以下步骤：
    1. 在 Azure 门户中，搜索并选择“资源组”。
    1. 在资源组列表中，选择资源组的名称。
    1. 在资源组的概述页面中，选择“删除资源组” 。
    1. 在确认对话框中，键入资源组的名称，然后选择“删除”。
    
- 若要删除 PostgreSQL 服务器，可在服务器的概述页面中选择“删除” 。
 


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
>[使用应用服务和 PostgreSQL 部署 Django 应用](tutorial-django-app-service-postgres.md)

