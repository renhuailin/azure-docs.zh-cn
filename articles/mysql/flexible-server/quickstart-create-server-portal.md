---
title: 快速入门：创建 Azure Database for MySQL 灵活服务器 - Azure 门户
description: 本文介绍如何使用 Azure 门户在几分钟内创建 Azure Database for MySQL 灵活服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/22/2020
ms.openlocfilehash: 864152d1f1d0074305cbba448946bc05888b4f3b
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94566752"
---
# <a name="quickstart-use-the-azure-portal-to-create-an-azure-database-for-mysql-flexible-server"></a>快速入门：使用 Azure 门户创建 Azure Database for MySQL 灵活服务器

Azure Database for MySQL 灵活服务器是一种托管服务，可用于在云中运行、管理和缩放具有高可用性的 MySQL 服务器。 本快速入门介绍如何使用 Azure 门户创建灵活服务器。

> [!IMPORTANT] 
> Azure Database for MySQL 灵活服务器当前以公共预览版提供。

如果还没有 Azure 订阅，可以在开始前创建一个[免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户
转到 [Azure 门户](https://portal.azure.com/)。 输入登录到门户所需的凭据。 默认视图是服务仪表板。

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>创建 Azure Database for MySQL 灵活服务器

使用一组定义的[计算和存储资源](./concepts-compute-storage.md)创建灵活服务器。 请在 [Azure 资源组](../../azure-resource-manager/management/overview.md)中创建该服务器。

如需创建灵活服务器，请完成以下步骤：

1. 在门户中搜索并选择“Azure Database for MySQL 服务器”：
    
    > :::image type="content" source="./media/quickstart-create-server-portal/find-mysql-portal.png" alt-text="显示对 Azure Database for MySQL 服务器进行搜索的屏幕截图。":::

2. 选择 **添加** 。 

3. 在“选择 Azure Database for MySQL 部署选项”页面上，选择“灵活服务器”作为部署选项 ：
     
    > :::image type="content" source="./media/quickstart-create-server-portal/deployment-option.png" alt-text="显示“灵活服务器”选项的屏幕截图。":::    

4. 在“基本”选项卡上，输入下列信息： 

    > :::image type="content" source="./media/quickstart-create-server-portal/create-form.png" alt-text="显示“灵活服务器”页的“基本信息”选项卡的屏幕截图。"::: 
                                    
    |**设置**|**建议的值**|**说明**|
    |---|---|---|
    订阅|订阅名称|要用于服务器的 Azure 订阅。 如果你有多个订阅，请选择要计费的资源所在的订阅。|
    资源组|myresourcegroup| 新的资源组名称，或订阅中的现有资源组。|
    服务器名称 |**mydemoserver**|标识你的灵活服务器的唯一名称。 域名 `mysql.database.azure.com` 将追加到所提供的服务器名称后面。 服务器名称只能包含小写字母、数字和连字符 (-) 字符。 它必须包含 3 到 63 个字符。|
    管理员用户名 |mydemouser| 连接到服务器时需要使用的你自己的登录帐户。 管理员用户名不能是“azure_superuser”、“admin”、“administrator”、“root”、“guest”或“public”     。|
    密码 |你的密码| 服务器管理员帐户的新密码。 该密码必须包含 8 到 128 个字符。 密码还必须包含以下三个类别的字符：英文大写字母、英文小写字母、数字（0 到 9）和非字母数字字符（!, $, #, % 等）。|
    区域|离用户最近的区域| 最靠近用户的位置。|
    版本|**5.7**| MySQL 主要版本。|
    计算 + 存储 | “可突增”、“Standard_B1ms”、“10 GiB”和“7 天”    | 新服务器的计算、存储和备份配置。 选择“配置服务器”。 “可突发”、“Standard_B1ms”、“10 GiB”和“7 天”分别是“计算层”、“计算大小”、“存储大小”和“备份保持期”的默认值       。 可按原样保留这些值，也可对其进行调整。 若要保存此计算和存储选择，请选择“保存”继续进行配置。 下面的屏幕截图显示了计算和存储选项。|
    
    > :::image type="content" source="./media/quickstart-create-server-portal/compute-storage.png" alt-text="显示计算和存储选项的屏幕截图。":::

5. 配置网络选项。

    在“网络”选项卡上，可选择访问服务器的方式。 Azure Database for MySQL 灵活服务器提供两种连接到服务器的方法： 
   - 公共访问（允许的 IP 地址）
   - 专用访问（VNet 集成） 
   
   使用“公共访问”时，对服务器的访问仅限于已添加到防火墙规则中的允许的 IP 地址。 除非创建了规则以打开特定 IP 地址或范围的防火墙，否则此方法会阻止外部应用程序和工具连接到服务器和服务器上的任何数据库。 使用“专用访问(VNet 集成)”时，对服务器的访问仅限于虚拟网络。 本快速入门介绍如何启用公共访问以连接到服务器。 [若要详细了解连接方法，请参阅概念文章。](./concepts-networking.md)

    > [!NOTE]
    > 创建服务器后，不能更改连接方法。 例如，如果在创建服务器时选择了“公共访问(允许的 IP 地址)”，则在服务器创建后无法将其更改为“专用访问(VNet 集成)” 。 强烈建议创建具有专用访问的服务器，以帮助通过 VNet 集成安全地访问服务器。 [若要详细了解专用访问，请参阅概念文章。](./concepts-networking.md)

    > :::image type="content" source="./media/quickstart-create-server-portal/networking.png" alt-text="显示“网络”选项卡的屏幕截图。":::  

6. 选择“查看 + 创建”，查看你的灵活服务器配置。

7. 选择“创建”以预配服务器。 预配可能需要几分钟时间。

8. 在工具栏上选择“通知”（钟形按钮）以监视部署过程。 完成部署后，可以选择“固定到仪表板”，以便在 Azure 门户仪表板上为此灵活服务器创建磁贴。 此磁贴是服务器“概述”页的快捷方式。 选择“转到资源”时，会打开此服务器的“概述”页 。

默认情况下，将在服务器下创建以下数据库：information_schema、mysql、performance_schema 和 sys。

> [!NOTE]
> 若要避免连接问题，请检查网络是否允许通过端口 3306 送出出站流量，该端口由 Azure Database for MySQL 灵活服务器使用。  

## <a name="connect-to-the-server-by-using-mysqlexe"></a>通过 mysql.exe 连接到服务器

如果使用“专用访问(VNet 集成)”创建了灵活服务器，需要从与服务器相同的虚拟网络中的资源连接到服务器。 可以创建虚拟机并将其添加到使用灵活服务器创建的虚拟网络中。

如果使用“公共访问(允许的 IP 地址)”创建了灵活服务器，可以将本地 IP 地址添加到服务器上的防火墙规则列表中。

可以使用 [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) 或 [MySQL Workbench](./connect-workbench.md) 从本地环境连接到服务器。 

如果使用的是 mysql .exe，请使用以下命令进行连接。 在命令中使用服务器名称、用户名和密码。 

```bash
 mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p
```
## <a name="clean-up-resources"></a>清理资源
现在已在资源组中创建 Azure Database for MySQL 灵活服务器。 如果将来不再需要这些资源，可以通过删除资源组来删除它们，也可以直接删除 MySQL 服务器。 若要删除资源组，请完成以下步骤：

1. 在 Azure 门户中，搜索并选择“资源组”。
1. 在资源组列表中，选择资源组的名称。
1. 在资源组的“概述”页中，选择“删除资源组” 。
1. 在确认对话框中，键入资源组的名称，然后选择“删除”。

若要删除服务器，可以在服务器的“概述”页中选择“删除”，如下所示 ：

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-server-portal/delete-server.png" alt-text="显示如何删除服务器的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 MySQL 生成 PHP (Laravel) Web 应用](tutorial-php-database-app.md)