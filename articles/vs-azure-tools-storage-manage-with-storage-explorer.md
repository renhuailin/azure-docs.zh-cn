---
title: 存储资源管理器入门 | Microsoft Docs
description: 开始使用存储资源管理器管理 Azure 存储资源。 下载并安装 Azure 存储资源管理器、连接到存储帐户或服务，等等。
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 969e2d2424b9f9af1dcd921a1cebfa9407e313eb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438362"
---
# <a name="get-started-with-storage-explorer"></a>存储资源管理器入门

## <a name="overview"></a>概述

Microsoft Azure 存储资源管理器是一个独立的应用，使用它可在 Windows、macOS 和 Linux 上轻松处理 Azure 存储数据。

本文介绍如何通过多种方式连接到 Azure 存储帐户并对其进行管理。

:::image type="content" alt-text="Microsoft Azure 存储资源管理器" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-overview.png":::

## <a name="prerequisites"></a>先决条件

# <a name="windows"></a>[Windows](#tab/windows)

以下版本的 Windows 支持存储资源管理器：

* Windows 10（推荐）
* Windows 8
* Windows 7

对于所有 Windows 版本，存储资源管理器都至少需要 .NET Framework 4.7.2。

# <a name="macos"></a>[macOS](#tab/macos)

以下版本的 macOS 支持存储资源管理器：

* macOS 10.12 Sierra 和更高版本

# <a name="linux"></a>[Linux](#tab/linux)

最常用 Linux 分发版的 [Snap Store](https://snapcraft.io/storage-explorer) 中都提供了存储资源管理器。 我们建议通过 Snap Store 安装存储资源管理器。 将新版本发布到 Snap Store 时，存储资源管理器内嵌项会安装其所有依赖项和更新。

有关受支持的分发版，请参阅[`snapd` 安装页](https://snapcraft.io/docs/installing-snapd)。

存储资源管理器要求使用密码管理器。 可能必须手动连接到密码管理器。 可以运行以下命令，将存储资源管理器连接到系统的密码管理器：

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

存储资源管理器还以 *.tar.gz* 下载文件格式提供。 如果使用 .tar.gz，则必须手动安装依赖项。 以下 Linux 分发版支持 *.tar.gz* 安装：

* Ubuntu 20.04 x64
* Ubuntu 18.04 x64
* Ubuntu 16.04 x64

*.tar.gz* 安装也许能够在其他分发版上正常进行，但目前只有列出的分发版受到官方支持。

在 Linux 上安装存储资源管理器时如需更多帮助，请参阅 Azure 存储资源管理器故障排除指南中的 [Linux 依赖项](./storage/common/storage-explorer-troubleshooting.md#linux-dependencies)。

---

## <a name="download-and-install"></a>下载并安装

若要下载并安装存储资源管理器，请参阅 [Azure 存储资源管理器](https://www.storageexplorer.com)。

## <a name="connect-to-a-storage-account-or-service"></a>连接到存储帐户或服务

存储资源管理器提供了多种用来连接到 Azure 资源的方式：

* [登录到 Azure 以访问自己的订阅及其资源](#sign-in-to-azure)
* [附加到单个 Azure 存储资源](#attach-to-an-individual-resource)
* [附加到 CosmosDB 资源](#connect-to-azure-cosmos-db)

### <a name="sign-in-to-azure"></a>登录 Azure

> [!NOTE]
> 登录后，存储资源管理器要求拥有管理（Azure 资源管理器）和数据层权限才能全面访问资源。 这意味着，你需要拥有 Azure Active Directory (Azure AD) 权限以访问自己的存储帐户、该帐户中的容器以及该容器中的数据。 如果你仅在数据层具有权限，请考虑在附加到资源时，选择“使用 Azure Active Directory (Azure AD) 登录”。 若要详细了解存储资源管理器所需的具体权限，请参阅 [Azure 存储资源管理器故障排除指南](./storage/common/storage-explorer-troubleshooting.md#azure-rbac-permissions-issues)。

1. 在存储资源管理器中，选择“视图” > “帐户管理”，或选择“管理帐户”按钮。  

    :::image type="content" alt-text="管理帐户" source ="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-manage-accounts.png":::

1. “帐户管理”现在显示登录的所有 Azure 帐户。 若要连接到另一个帐户，请选择“添加帐户…”。

1. 随即将打开“连接到 Azure 存储”对话框。 在“选择资源”面板中，选择“订阅” 。

    :::image type="content" alt-text="连接对话框" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-dialog.png":::

1. 在“选择 Azure 环境”面板中，选择要登录到的 Azure 环境。 你可以登录到全球 Azure、区域云和 Azure Stack 实例。 然后，选择“下一步”。

    :::image type="content" alt-text="登录选项" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-environment.png":::

    > [!TIP]
    > 有关 Azure Stack 的详细信息，请参阅[将存储资源管理器连接到 Azure Stack 订阅或存储帐户](/azure-stack/user/azure-stack-storage-connect-se)。

1. 存储资源管理器将打开一个网页供你登录。

1. 使用 Azure 帐户成功登录后，该帐户以及与之关联的 Azure 订阅将显示在“帐户管理”下。 选择要使用的 Azure 订阅，并选择“应用”。

    :::image type="content" alt-text="选择 Azure 订阅" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-account-panel.png":::

1. “资源管理器”会显示与所选 Azure 订阅关联的存储帐户。

    :::image type="content" alt-text="选择的 Azure 订阅" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-subscription-node.png":::

### <a name="attach-to-an-individual-resource"></a>附加到单个资源

存储资源管理器允许使用各种身份验证方法连接到单个资源，如 Azure Data Lake Storage Gen2 容器。 某些资源类型仅支持某些身份验证方法。

| 资源类型    | Azure AD | 帐户名称和密钥 | 共享访问签名 (SAS)  | 公共（匿名） |
|------------------|----------|----------------------|--------------------------------|--------------------|
| 存储帐户 | 是      | 是                  | 是（连接字符串或 URL） | 否                 |
| Blob 容器  | 是      | 否                   | 是（URL）                      | 是                |
| Gen2 容器  | 是      | 否                   | 是（URL）                      | 是                |
| Gen2 目录 | 是      | 否                   | 是（URL）                      | 是                |
| 文件共享      | 否       | 否                   | 是（URL）                      | 否                 |
| 队列           | 是      | 否                   | 是（URL）                      | 否                 |
| 表           | 否       | 否                   | 是（URL）                      | 否                 |
 
存储资源管理器还可以使用仿真器的已配置端口连接到[本地存储仿真器](#local-storage-emulator)。

若要连接到单个资源，请选择左侧工具栏中的“连接”按钮。 然后，按照你要连接到的资源类型的说明进行操作。

:::image type="content" alt-text="“连接到 Azure 存储”选项" source="./media/vs-azure-tools-storage-manage-with-storage-explorer/vs-storage-explorer-connect-button.png":::

成功添加到存储帐户的连接后，“本地和附加” > “存储帐户”下将显示新的树节点 。

对于其他资源类型，会在“本地和附加” > “存储帐户” > “(附加的容器)”下添加一个新节点  。 节点将出现在与其类型匹配的组节点下。 例如，到 Azure Data Lake Storage Gen2 容器的新连接将显示在“Blob 容器”下。

如果存储资源管理器无法添加你的连接，或者在成功添加连接后你无法访问自己的数据，请参阅 [Azure 存储资源管理器故障排除指南](./storage/common/storage-explorer-troubleshooting.md)。

以下各节介绍了可用于连接到单个资源的不同身份验证方法。

#### <a name="azure-ad"></a>Azure AD

存储资源管理器可以使用 Azure 帐户连接到以下资源类型：
* Blob 容器
* Azure Data Lake Storage Gen2 容器
* Azure Data Lake Storage Gen2 目录
* 队列
 
如果你具有对资源的数据层访问权限，但没有管理层访问权限，则 Azure AD 是首选选项。

1. 使用[上述步骤](#sign-in-to-azure)登录到至少一个 Azure 帐户。
1. 在“连接到 Azure 存储”对话框的“选择资源”面板中，选择“Blob 容器”、“ADLS Gen2 容器”或“队列”    。
1. 选择“使用 Azure Active Directory (Azure AD) 登录”，然后选择“下一步” 。
1. 选择 Azure 帐户和租户。 帐户和租户必须有权访问所要附加到的存储资源。 选择“**下一步**”。
1. 输入连接的显示名称和资源的 URL。 选择“**下一步**”。
1. 在“摘要”面板中查看连接信息。 如果连接信息正确，请选择“连接”。

#### <a name="account-name-and-key"></a>帐户名称和密钥

存储资源管理器可以使用存储帐户的名称和密钥连接到存储帐户。

可以在 [Azure 门户](https://portal.azure.com)中找到帐户密钥。 打开存储帐户页，然后选择“设置” > 访问密钥” 。

1. 在“连接到 Azure 存储”对话框的“选择资源”面板中，选择“存储帐户”  。
1. 选择“帐户名称和密钥”并选择“下一步” 。
1. 输入连接的显示名称、帐户名称和某个帐户密钥。 选择相应的 Azure 环境。 选择“**下一步**”。
1. 在“摘要”面板中查看连接信息。 如果连接信息正确，请选择“连接”。

#### <a name="shared-access-signature-sas-connection-string"></a>共享访问签名 (SAS) 连接字符串

存储资源管理器可以使用具有共享访问签名 (SAS) 的连接字符串连接到存储帐户。 SAS 连接字符串如下所示：

```text
SharedAccessSignature=sv=2020-04-08&ss=btqf&srt=sco&st=2021-03-02T00%3A22%3A19Z&se=2020-03-03T00%3A22%3A19Z&sp=rl&sig=fFFpX%2F5tzqmmFFaL0wRffHlhfFFLn6zJuylT6yhOo%2FY%3F;
BlobEndpoint=https://contoso.blob.core.windows.net/;
FileEndpoint=https://contoso.file.core.windows.net/;
QueueEndpoint=https://contoso.queue.core.windows.net/;
TableEndpoint=https://contoso.table.core.windows.net/;
```

1. 在“连接到 Azure 存储”对话框的“选择资源”面板中，选择“存储帐户”  。
1. 选择“共享访问签名(SAS)”并选择“下一步” 。
1. 输入连接的显示名称和存储帐户的 SAS 连接字符串。 选择“**下一步**”。
1. 在“摘要”面板中查看连接信息。 如果连接信息正确，请选择“连接”。

#### <a name="shared-access-signature-sas-url"></a>共享访问签名 (SAS) URL

存储资源管理器可以使用 SAS URI 连接到以下资源类型：
* Blob 容器
* Azure Data Lake Storage Gen2 容器或目录
* 文件共享
* 队列
* 表

SAS URI 如下所示：

```text
https://contoso.blob.core.windows.net/container01?sv=2020-04-08&st=2021-03-02T00%3A30%3A33Z&se=2020-03-03T00%3A30%3A33Z&sr=c&sp=rl&sig=z9VFdWffrV6FXU51T8b8HVfipZPOpYOFLXuQw6wfkFY%3F
```

1. 在“连接到 Azure 存储”对话框的“选择资源”面板中，选择要连接到的资源 。
1. 选择“共享访问签名(SAS)”并选择“下一步” 。
1. 输入连接的显示名称和资源的 SAS URI。 选择“**下一步**”。
1. 在“摘要”面板中查看连接信息。 如果连接信息正确，请选择“连接”。

#### <a name="local-storage-emulator"></a>本地存储仿真器

存储资源管理器可以连接到 Azure 存储仿真器。 当前有两个受支持的仿真器：

* [Azure 存储仿真器](storage/common/storage-use-emulator.md)（仅适用于 Windows）
* [Azurite](https://github.com/azure/azurite)（Windows、macOS 或 Linux）

如果仿真器正在侦听默认端口，则可使用“本地和附加” > “存储帐户” > “仿真器 - 默认端口”节点访问仿真器  。

若要为连接使用不同的名称，或者仿真器不在默认端口上运行：

1. 启动仿真器。

   > [!IMPORTANT]
   > 存储资源管理器不会自动启动你的仿真器。 必须手动启动它。

1. 在“连接到 Azure 存储”对话框的“选择资源”面板中，选择“本地存储仿真器”  。
1. 为连接输入显示名称，并为每个要使用的仿真服务输入端口号。 如果不想将其用于服务，请将相应的端口留空。 选择“**下一步**”。
1. 在“摘要”面板中查看连接信息。 如果连接信息正确，请选择“连接”。

### <a name="connect-to-azure-cosmos-db"></a>连接到 Azure Cosmos DB

存储资源管理器还支持连接到 Azure Cosmos DB 资源。

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>使用连接字符串连接到 Azure Cosmos DB 帐户

如果不通过 Azure 订阅管理 Azure Cosmos DB 帐户，可以使用连接字符串连接到 Azure Cosmos DB。 若要进行连接，请执行以下步骤：

1. 在“资源管理器”下，展开“本地和附加”，右键单击“Azure Cosmos DB 帐户”，然后选择“连接到 Azure Cosmos DB”。   

    ![通过连接字符串连接到 Azure Cosmos DB][21]

1. 选择“Azure Cosmos DB API”，输入“连接字符串”数据，然后选择“确定”以连接到 Azure Cosmos DB 帐户。  有关如何检索连接字符串的信息，请参阅[管理 Azure Cosmos 帐户](./cosmos-db/how-to-manage-database-account.md)。

    ![连接字符串][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>通过 URI 连接到 Azure Data Lake Store

可以访问不在订阅中的资源。 需要请求某个有权访问该资源的人员提供资源 URI。 登录后，使用该 URI 连接到 Data Lake Store。 若要进行连接，请执行以下步骤：

1. 在“资源管理器”下，展开“本地和附加”。 

1. 右键单击“Data Lake Storage Gen1”并选择“连接到 Data Lake Storage Gen1”。 

    ![“连接到 Data Lake Store”上下文菜单](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. 输入 URI，然后选择“确定”。 你的 Data Lake Store 将显示在“Data Lake Storage”下。

    ![“连接到 Data Lake Store”操作结果](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

本示例使用 Data Lake Storage Gen1。 Azure Data Lake Storage Gen2 现已推出。 有关详细信息，请参阅[什么是 Azure Data Lake Storage Gen1](./data-lake-store/data-lake-store-overview.md)。

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>在存储资源管理器中生成共享访问签名<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>帐户级共享访问签名

1. 右键单击要共享的存储帐户，并选择“获取共享访问签名”。

    ![“获取共享访问签名”上下文菜单选项][14]

1. 在“共享访问签名”中，指定要授予该帐户的时间范围和权限，然后选择“创建”。 

    ![获取共享访问签名][15]

1. 将“连接字符串”或原始“查询字符串”复制到剪贴板。 

### <a name="service-level-shared-access-signature"></a>服务级共享访问签名

可以在服务级别获取共享访问签名。 有关详细信息，请参阅[获取 Blob 容器的 SAS](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)。

## <a name="search-for-storage-accounts"></a>搜索存储帐户

若要查找存储资源，可以在“资源管理器”窗格中搜索。

在搜索框中输入文本时，存储资源管理器会显示与当前已输入的搜索值匹配的所有资源。 本示例演示如何搜索 **终结点**：

![存储帐户搜索][23]

> [!NOTE]
> 若要加快搜索速度，可使用“帐户管理”取消选择不包含所要搜索的项的订阅。 还可以右键单击某个节点，然后选择“从此处搜索”，从特定的节点开始搜索。

## <a name="next-steps"></a>后续步骤

* [使用存储资源管理器管理 Azure Blob 存储资源](vs-azure-tools-storage-explorer-blobs.md)
* [使用 Azure 存储资源管理器处理数据](./cosmos-db/storage-explorer.md)
* [使用存储资源管理器管理 Azure Data Lake Store 资源](./data-lake-store/data-lake-store-in-storage-explorer.md)

[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
