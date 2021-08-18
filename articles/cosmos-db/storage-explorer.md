---
title: 使用 Azure 存储资源管理器管理 Azure Cosmos DB 资源
description: 了解如何使用 Azure 存储资源管理器连接到 Azure Cosmos DB 并管理其资源。
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: 447ebaac46235d551236b0b354acbacb67df0d07
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397142"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>使用 Azure 存储资源管理器管理 Azure Cosmos DB 资源
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

可使用 Azure 存储资源管理器连接到 Azure Cosmos DB。 借助此功能，可通过 Windows、macOS 或 Linux 连接到托管在 Azure 和主权云上的 Azure Cosmos DB 帐户。

使用相同的工具在一个位置管理不同的 Azure 实体。 可管理 Azure Cosmos DB 实体、操作数据、更新存储过程和触发器，还可更新其他 Azure 实体（例如存储 Blob 和队列）。 Azure 存储资源管理器支持为 SQL、MongoDB、图形和表 API 配置的 Cosmos 帐户。

> [!NOTE]
> 与存储资源管理器的 Azure Cosmos DB 集成已弃用。 任何现有功能至少在一年内不会从此版本中删除。 应改为使用 [Azure 门户](https://portal.azure.com/)、[Azure 门户桌面应用](https://portal.azure.com/App/Download)或独立的 [Azure Cosmos DB 资源管理器](data-explorer.md)。 替代选项包含存储资源管理器当前不支持的许多新功能。

## <a name="prerequisites"></a>先决条件

具有 Cosmos 帐户，且该帐户带有用于 MongoDB 的 SQL API 或 Azure Cosmos DB API。 如果没有帐户，可在 Azure 门户中创建一个。 有关详细信息，请参阅 [Azure Cosmos DB：使用 .NET 和 Azure 门户生成 SQL API Web 应用](create-sql-api-dotnet.md)。

## <a name="installation"></a>安装

若要安装最新版本的 Azure 存储资源管理器，请参阅 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。 我们支持 Windows、Linux 和 macOS 版本。

## <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

1. 安装 Azure 存储资源管理器后，在左侧窗格中选择插件图标 。

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="显示左侧窗格中的插件图标的屏幕截图。":::

1. 选择“添加 Azure 帐户”，然后选择“登录”。

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="“连接到 Azure 存储”窗口的屏幕截图，其中显示已选择“添加 Azure 帐户”单选按钮，还包含“Azure 环境”下拉菜单。":::

1. 在“Azure 登录”对话框中，选择“登录”，然后输入 Azure 凭据。

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="“登录”窗口的屏幕截图，其中显示在何处输入 Azure 订阅的凭据。":::

1. 从列表中选择订阅，然后选择“应用”。

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="“帐户管理”窗格的屏幕截图，其中显示订阅列表和“应用”按钮。":::

    资源管理器窗格会更新，并显示所选订阅中的帐户。

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="“资源管理器”窗格的屏幕截图，该窗格已更新为显示所选订阅中的帐户。":::

    Cosmos DB 帐户已连接到 Azure 订阅。

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>使用连接字符串连接到 Azure Cosmos DB

可使用连接字符串连接到 Azure Cosmos DB。 此方法仅支持 SQL 和表 API。 请按照以下步骤，使用连接字符串进行连接：

1. 在左侧树中找到“本地和附加”，右键单击“Cosmos DB 帐户”，然后选择“连接到 Cosmos DB”  。

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="此屏幕截图显示了右键单击后的下拉菜单，其中突出显示了“连接到 Azure Cosmos DB”。":::

2. 在“连接到 Cosmos DB”窗口中：
   1. 从下拉菜单中选择 API。
   1. 将连接字符串粘贴到“连接字符串”框中。 要了解如何检索主要连接字符串，请参阅[获取连接字符串](manage-with-powershell.md#list-keys)。
   1. 输入帐户标签，然后选择“下一步”以查看摘要 。
   1. 选择“连接”来连接 Azure Cosmos DB 帐户。

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="“连接到 Cosmos DB”窗口的屏幕截图，其中显示 API 下拉菜单、“连接字符串”框和“帐户标签”框。":::

> [!NOTE]
> 如果 Azure 存储资源管理器显示 Azure Cosmos DB 连接字符串的格式无效，请确保连接字符串的末尾有一个分号 (`;`)。 有效 Azure Cosmos DB 连接字符串的示例如下：`AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>使用本地模拟器连接到 Azure Cosmos DB

按照以下步骤，通过模拟器连接到 Azure Cosmos DB。 此方法仅支持 SQL 帐户。

1. 安装 Cosmos DB 模拟器，然后将其打开。 要了解如何安装模拟器，请参阅 [Cosmos DB 模拟器](./local-emulator.md)。

1. 在左侧树中找到“本地和附加”，右键单击“Cosmos DB 帐户”，然后选择“连接到 Cosmos DB 模拟器”  。

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="显示右键单击后显示的菜单的屏幕截图，其中突出显示了“连接到 Azure Cosmos DB 模拟器”。":::

1. 在“连接到 Cosmos DB”窗口中：
   1. 将连接字符串粘贴到“连接字符串”框中。 有关检索主连接字符串的信息，请参阅[获取连接字符串](manage-with-powershell.md#list-keys)。
   1. 输入帐户标签，然后选择“下一步”以查看摘要 。
   1. 选择“连接”来连接 Azure Cosmos DB 帐户。

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="“连接到 Cosmos DB”窗口的屏幕截图，其中显示“连接字符串”框和“帐户标签”框。":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB 资源管理

执行以下操作来管理 Azure Cosmos DB 帐户：

* 在 Azure 门户中打开该帐户。
* 将资源添加到“快速访问”列表。
* 搜索和刷新资源。
* 创建和删除数据库。
* 创建和删除集合。
* 创建、编辑、删除和筛选文档。
* 管理存储过程、触发器和用户定义的函数。

### <a name="quick-access-tasks"></a>快速访问任务

可右键单击“资源管理器”窗格上的订阅，执行多项快速操作任务，例如：

* 右键单击 Azure Cosmos DB 帐户或数据库，然后选择“在门户中打开”，通过浏览器在 Azure 门户上管理资源。

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="显示右键单击后显示的菜单的屏幕截图，其中突出显示了“在门户中打开”。":::

* 右键单击 Azure Cosmos DB 帐户、数据库或集合，然后选择“添加到快速访问”，将其添加到“快速访问”菜单。

* 选择“从此处搜索”，启用所选路径下的关键字搜索。

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="突出显示搜索框的屏幕截图。":::

### <a name="database-and-collection-management"></a>数据库和集合管理

#### <a name="create-a-database"></a>创建数据库

1. 右键单击 Azure Cosmos DB 帐户，然后选择“创建数据库”。

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="显示右键单击后显示的菜单的屏幕截图，其中突出显示了“创建数据库”。":::

1. 输入数据库名称，然后按 Enter 完成操作。

#### <a name="delete-a-database"></a>删除数据库

1. 右键单击数据库，然后选择“删除数据库”。 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="显示右键单击后显示的菜单的屏幕截图，其中突出显示了“删除数据库”。":::

1. 在弹出窗口中选择“是”。 数据库节点会删除，并且 Azure Cosmos DB 帐户会自动刷新。

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="确认窗口的屏幕截图，其中突出显示了“是”按钮。":::

#### <a name="create-a-collection"></a>创建集合

1. 右键单击数据库，然后选择“创建集合”。

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="显示右键单击后显示的菜单的屏幕截图，其中突出显示了“创建集合”。":::

1. 在“创建集合”窗口中，输入所请求的信息，例如集合 ID 和存储容量等 。 选择“确定”，以完成操作。

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="“创建集合”窗口的屏幕截图，其中显示“集合 ID”框和“存储容量”按钮。":::

1. 选择“无限制”，以便可指定分区键，然后选择“确定”来完成操作 。

   > [!NOTE]
   > 如果在创建集合时使用了分区键，则创建完成后，无法更改集合上的分区键值。

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="“创建集合”窗口的屏幕截图，其中显示为“存储容量”选择了“无限制”，并突出显示了“分区键”框。":::

#### <a name="delete-a-collection"></a>删除集合

- 右键单击集合，选择“删除集合”，然后在弹出窗口中选择“是”。

    集合节点会删除，并且数据库会自动刷新。

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="显示右键单击后显示的菜单的屏幕截图，其中突出显示了“删除集合”。":::

### <a name="document-management"></a>文档管理

#### <a name="create-and-modify-documents"></a>创建和修改文档

- 在左侧窗格中打开“文档”，选择“新建文档”，在右侧窗格中编辑内容，然后选择“保存”  。
- 还可以更新现有文档，然后选择“保存”。 若要放弃更改，请选择“放弃”。

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="左侧窗格中突出显示了“文档”的屏幕截图。在右侧窗格中，突出显示了“新建文档”、“保存”和“放弃”。":::

#### <a name="delete-a-document"></a>删除文档

* 选择“删除”按钮，删除所选文档。

#### <a name="query-for-documents"></a>查询文档

* 若要编辑文档筛选器，请输入 [SQL 查询](./sql-query-getting-started.md)，然后选择“应用”。

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="右侧窗格的屏幕截图，其中突出显示了“筛选”和“应用”按钮、ID 号和查询框。":::

### <a name="graph-management"></a>图形管理

#### <a name="create-and-modify-a-vertex"></a>创建和修改顶点

* 若要创建新顶点，请从左侧窗格中打开“图形”，选择“新建顶点”，编辑内容，然后选择“确定”  。
* 若要修改现有顶点，请在右侧窗格中选择笔图标。

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="一张屏幕截图，其中左侧窗格中选择了“图形”，显示了“新建顶点”，且右侧窗格中突出显示了笔图标。":::

#### <a name="delete-a-graph"></a>创建图形

* 若要删除某个顶点，请选择顶点名称旁边的回收站图标。

#### <a name="filter-for-graph"></a>筛选图形

* 若要编辑图形筛选器，请输入 [gremlin 查询](gremlin-support.md)，然后选择“应用筛选器”。

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="一张屏幕截图，其中左侧窗格中选择了“图形”，显示了“应用筛选器”，且右侧窗格中突出显示了查询框。":::

### <a name="table-management"></a>表管理

#### <a name="create-and-modify-a-table"></a>创建和修改表

* 若要创建新表：
   1. 在左侧窗格中，打开“实体”，然后选择“添加” 。
   1. 在“添加实体”对话框中，编辑内容。
   1. 选择“添加属性”按钮以添加属性。
   1. 选择“插入”。

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="一张屏幕截图，其中左侧窗格中突出显示了“实体”，右侧窗格中突出显示了“添加”、“编辑”、“添加属性”和“插入”。":::

* 若要修改表，请选择“编辑”，修改内容，然后选择“更新” 。

   

#### <a name="import-and-export-table"></a>导入和导出表

* 若要导入，请选择“导入”按钮并选择现有的表。
* 若要导出，请选择“导出”按钮并选择一个目标。

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="右侧窗格中突出显示了“导入”和“导出”按钮的屏幕截图。":::

#### <a name="delete-entities"></a>删除实体

* 选择实体，然后选择“删除”按钮。

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="一张屏幕截图，其中右侧窗格中突出显示了“删除”按钮，还有一个突出显示了“是”选项的确认弹出窗口。":::

#### <a name="query-a-table"></a>查询表

- 选择“查询”按钮，输入查询条件，然后选择“执行查询”按钮 。 若要关闭查询窗格，请选择“关闭查询”按钮。

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="右侧窗格的屏幕截图，其中显示了“执行查询”按钮并突出显示了“关闭查询”按钮。":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>管理存储过程、触发器和 UDF

* 若要创建存储过程：
  1. 在左侧树中，右键单击“存储过程”，然后选择“创建存储过程” 。
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="左侧窗格的屏幕截图，其中显示右键单击后显示的菜单并突出显示了“创建存储过程”。":::
  
  1. 在左侧输入名称，在右侧窗格中输入存储过程脚本，然后选择“创建”。
  
* 若要编辑现有存储过程，请双击该过程，进行更新，然后选择“更新”进行保存。 也可选择“放弃”来取消更改。

* 用于触发器和 UDF 的操作与存储过程类似  。

## <a name="troubleshooting"></a>故障排除

下面是针对在存储资源管理器中使用 Azure Cosmos DB 时出现的常见问题的解决方案。

### <a name="sign-in-issues"></a>登录问题

首先，重启应用程序，看看能否解决问题。 如果问题仍然存在，请继续进行故障排除。

#### <a name="self-signed-certificate-in-certificate-chain"></a>证书链中的自签名证书

出现此错误有多个原因，最常见的两个原因是：

* 你在使用透明代理。 有人（例如你的 IT 部门）会截获 HTTPS 流量，对其进行解密，然后使用自签名证书对其进行加密。

* 你正在运行软件，例如防病毒软件。 软件将自签名的 TLS/SSL 证书注入收到的 HTTPS 消息中。

当存储资源管理器找到自签名证书时，它不知道自己收到的 HTTPS 消息是否遭到篡改。 如果拥有自签名证书的副本，则可命令存储资源管理器信任它。 如果不确定谁注入了证书，可按照以下步骤操作，尝试将其找出来：

1. 安装 OpenSSL：

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html)：任意轻量版本均可。
     - macOS 和 Linux：应包含在操作系统中。

1. 运行 OpenSSL：
    * Windows:转到安装目录，转到“/bin/”，然后双击“openssl.exe” 。
    * Mac 和 Linux：从终端执行 openssl。
1. 执行 `s_client -showcerts -connect microsoft.com:443`。
1. 查找自签名证书。 如果不确定哪个是自签名证书，请查找使用者（“s:”）与证书颁发者（“i:”）相同的任意位置。
1. 如果找到任何自签名证书，请将每个证书中从“-----BEGIN CERTIFICATE-----”（含）到“-----END CERTIFICATE-----”（含）的部分复制粘贴到新的 .CER 文件。 。
1. 打开存储资源管理器，然后转到“编辑” > “SSL 证书” > “导入证书”  。 使用文件选取器查找、选择并打开所创建的 .CER 文件。

如果找不到任何自签名证书，则可发送反馈以获取更多帮助。

#### <a name="unable-to-retrieve-subscriptions"></a>无法检索订阅

如果登录后无法检索到订阅，请尝试以下建议：

* 验证你的帐户是否可访问订阅。 为此，请登录 [Azure 门户](https://portal.azure.com/)。
* 确保登录到正确的环境：
  * [Azure](https://portal.azure.com/)
  * [Azure 中国](https://portal.azure.cn/)
  * [Azure 德国](https://portal.microsoftazure.de/)
  * [Azure 美国政府版](https://portal.azure.us/)
  * 自定义环境/Azure Stack
* 如果使用代理，请确保已正确配置存储资源管理器代理。
* 删除帐户，然后重新添加它。
* 从主目录（例如：C:\Users\ContosoUser）中删除以下文件，然后重新添加帐户：
  * .adalcache
  * .devaccounts
  * .extaccounts
* 按 F12 键打开开发人员控制台。 登录时，请在控制台中查看是否出现任何错误消息。

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="开发人员工具控制台的屏幕截图，其中突出显示了“控制台”。":::

#### <a name="unable-to-see-the-authentication-page"></a>无法查看身份验证页

如果无法看到身份验证页面：

* 根据连接速度，可能需要一段时间才能加载登录页面。 请等待至少一分钟，然后再关闭身份验证对话框。
* 如果使用代理，请确保已正确配置存储资源管理器代理。
* 在开发人员工具控制台 (F12) 上，观察响应，查看能否找到有关身份验证为何不起作用的任何线索。

#### <a name="cant-remove-an-account"></a>无法删除帐户

如果无法删除帐户，或者重新验证链接不起作用，请执行以下操作：

* 从主目录中删除以下文件，然后再次添加帐户：
  * .adalcache
  * .devaccounts
  * .extaccounts

* 若要删除附加了 SAS 的存储资源，请删除：
  * %AppData%/StorageExplorer 文件夹（对于 Windows）
  * /Users/<your_name>/Library/Application SUpport/StorageExplorer（对于 macOS）
  * ~/.config/StorageExplorer（对于 Linux）
  
  > [!NOTE]
  > 如果删除这些文件，则必须重新输入所有凭据。

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS 代理问题

在 ASE 中配置 HTTP/HTTPS 代理时，无法在左侧树中列出 Azure Cosmos DB 节点。 可在 Azure 门户中使用 Azure Cosmos DB 数据资源管理器作为解决方法。

### <a name="development-node-under-local-and-attached-node-issue"></a>“本地和附加”节点下的“开发”节点问题

在左侧树中选择“本地和附加”节点下的“开发”节点后没有响应 。 此行为是预期的行为。

:::image type="content" source="./media/storage-explorer/development.png" alt-text="显示选定的“开发”节点的屏幕截图。":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>在“本地和附加”节点错误中附加 Azure Cosmos DB 帐户

如果在“本地和附加”节点中附加 Azure Cosmos DB 帐户后看到以下错误，请确保使用正确的连接字符串。

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="“无法检索子资源”错误弹出窗口的屏幕截图，其中指示 getaddrinfo ENOTFOUND。":::

### <a name="expand-azure-cosmos-db-node-error"></a>展开 Azure Cosmos DB 节点错误

尝试在左侧树中展开节点时，可能会出现以下错误。

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="“无法检索子资源”错误弹出窗口的屏幕截图，其中指示无法连接到该 Cosmos DB 帐户。":::

请尝试以下建议：

* 检查 Azure Cosmos DB 帐户是否正在预配。 成功创建帐户后，请重试。
* 如果该帐户位于“快速访问”或“本地和附加”节点下，请检查该帐户是否已删除 。 如果是，则需要手动删除该节点。

## <a name="next-steps"></a>后续步骤

* 观看此视频，了解如何在 Azure 存储资源管理器中使用 Azure Cosmos DB：[在 Azure 存储资源管理器中使用 Azure Cosmos DB](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be)。
* 在[存储资源管理器入门](../vs-azure-tools-storage-manage-with-storage-explorer.md)中了解有关存储资源管理器和连接更多服务的详细信息。
