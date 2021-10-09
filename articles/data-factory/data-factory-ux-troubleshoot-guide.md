---
title: 排查 Azure 数据工厂 Studio 问题
description: 了解如何排查 Azure 数据工厂 Studio 问题。
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: fda5131d4a4578de01dc5a34aada295c6b364848
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663129"
---
# <a name="troubleshoot-azure-data-factory-studio-issues"></a>排查 Azure 数据工厂 Studio 问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介绍 [Azure 数据工厂 Studio（服务的用户界面）](https://adf.azure.com)的常用问题排查方法。

## <a name="azure-data-factory-studio-fails-to-load"></a>Azure 数据工厂 Studio 无法加载

> [!NOTE]
> Azure 数据工厂 Studio 官方支持 Microsoft Edge 和 Google Chrome。 使用其他 Web 浏览器可能会导致意外行为或未记录的行为。

### <a name="third-party-cookies-blocked"></a>已阻止第三方 Cookie

Azure 数据工厂 Studio 使用浏览器 Cookie 来保留用户会话状态并启用交互式开发和监视体验。 你的浏览器可能会阻止第三方 Cookie，因为你正在使用匿名会话或启用了广告阻止程序。 在加载门户时阻止第三方 Cookie 可能导致问题。  你可能会被重定向到空白页“https://adf.azure.com/accesstoken.html”，或者可能会遇到警告消息，指出已阻止第三方 cookie。 若要解决此问题，请使用以下步骤在浏览器上启用第三方 Cookie 选项：

# <a name="microsoft-edge"></a>[Microsoft Edge](#tab/edge)

#### <a name="allow-all-cookies"></a>允许所有 Cookie

1. 在浏览器中访问“edge://settings/content/cookies”。
1. 确保启用了“允许站点保存和读取 Cookie 数据”，并禁用了“阻止第三方 Cookie”选项  

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png" alt-text="允许 Edge 中的所有 Cookie":::
1. 刷新 Azure 数据工厂 Studio，然后重试。

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>仅允许 Azure 数据工厂 Studio 使用 Cookie

如果你不想要允许所有 Cookie，则可以选择只允许 ADF Studio：

1. 访问“edge://settings/content/cookies”。
1. 在“允许”部分下，选择“添加”并添加“adf.azure.com”站点  。 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png" alt-text="将 ADF UX 添加到 Edge 中允许的站点":::
1. 刷新 ADF UX，然后重试。

# <a name="google-chrome"></a>[Google Chrome](#tab/chrome)

#### <a name="allow-all-cookies"></a>允许所有 Cookie

1. 在浏览器中访问“chrome://settings/cookies”。
1. 选择“允许所有 Cookie”选项 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png" alt-text="在 Chrome 中允许所有 Cookie":::
1. 刷新 Azure 数据工厂 Studio，然后重试。

#### <a name="only-allow-azure-data-factory-studio-to-use-cookies"></a>仅允许 Azure 数据工厂 Studio 使用 Cookie

如果你不想要允许所有 Cookie，则可以选择只允许 ADF Studio：
1. 访问“chrome://settings/cookies”。
1. 选择“始终可以使用 Cookie 的站点”选项下的“添加”  

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png" alt-text="将 ADF UX 添加到 Chrome 中允许的站点":::
1. 添加“adf.azure.com”站点，选中“所有 Cookie”选项，然后保存 。 

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png" alt-text="允许来自 ADF UX 站点的所有 Cookie":::
1. 刷新 ADF UX，然后重试。

---

## <a name="connection-failed-error-in-azure-data-factory-studio"></a>Azure 数据工厂 Studio 中连接失败错误

有时，你可能会在 Azure 数据工厂 Studio 中看到类似于下面的屏幕截图的“连接失败”错误，例如，在单击“测试连接”或“预览”后。  这意味着操作失败，因为本地计算机无法连接到 ADF 服务。

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/connection-failed.png" alt-text="连接失败":::

若要解决此问题，你可以先在浏览器中使用 InPrivate 浏览模式尝试相同的操作。

如果仍然无法解决此问题，请从错误消息中查找服务器名称（在本例中为 dpnortheurope.svc.datafactory.azure.com），然后直接在浏览器的地址栏中键入服务器名称。 

- 如果你在浏览器中看到 404，则通常意味着客户端正常，问题出在 ADF 服务端。 使用错误消息中的“活动 ID”提交支持票证。

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/status-code-404.png" alt-text="找不到资源":::

- 如果没有看到 404，或者在浏览器中看到类似下方的错误，则通常意味着你遇到了某个客户端问题。 进一步执行故障排除步骤。

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/client-side-error.png" alt-text="客户端错误":::

若要进一步进行故障排除，请打开命令提示符并键入 `nslookup dpnortheurope.svc.datafactory.azure.com`。 正常响应应如下所示：

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-1.png" alt-text="命令响应 1":::

- 如果你看到正常的域名服务 (DNS) 响应，请联系你的本地 IT 支持部门检查防火墙设置。  请确保不要阻止到此主机名的 HTTPS 连接。 如果此问题仍然存在，请使用 ADF 提供支持票证，并提供错误消息中的“活动 ID”。

- 在解析 DNS 名称时，DNS 响应与上述正常响应不同，这也可能意味着 DNS 服务器存在问题。 在这种情况下，更改 DNS 服务器（例如，更改为 Google DNS 8.8.8.8）或许可以解决问题。 

- 如果此问题仍然存在，你可以进一步尝试 `nslookup datafactory.azure.com` 和 `nslookup azure.com` 以查看 DNS 解析失败所处的级别，并将所有信息提交给本地 IT 支持或 ISP 进行故障排除。 如果他们认为问题仍然存在于 Microsoft 端，请提交支持票证，并提供错误消息中的“活动 ID”。

    :::image type="content" source="media/data-factory-ux-troubleshoot-guide/command-response-2.png" alt-text="命令响应 2":::

## <a name="change-linked-service-type-warning-message-in-datasets"></a>更改数据集中的链接服务类型警告消息

在活动中使用文件格式数据集时，可能会遇到以下警告消息，并且稍后需要指向与之前在活动中使用的类型不同的链接服务（例如，从文件系统到 Azure Data Lake Storage Gen2）。

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/warning-message.png" alt-text="警告消息":::

文件格式数据集可以与所有基于文件的连接器一起使用；例如，你可以在 Azure Blob 或 Azure Data Lake Storage Gen2 上配置 Parquet 数据集。 注意，每个连接器在活动中通过不同的应用模型，支持不同的数据存储相关设置集。 

在 ADF 创作 UI 上，若你在活动（包括复制、查找、GetMetadata、删除活动）中使用文件格式数据集，并且在该数据集中，你希望指向不同于活动中当前类型的链接服务（例如从文件系统切换到 ADLS Gen2），则会看到此警告消息。 若要确保这是一个干净的切换操作，在你同意的情况下，引用此数据集的管道和活动也将被修改以使用新类型，并且任何与新类型不兼容的现有数据存储设置都将被清除，因为它不再适用。

若要详细了解每个连接器支持的数据存储设置，可以转到相应的连接器文章 -> 复制活动属性以查看详细的属性列表。 请参阅 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md) 和 [SFTP](connector-sftp.md)。


## <a name="could-not-load-resource-while-opening-pipeline"></a>打开管道时无法加载资源 

用户使用 Azure 数据工厂 Studio 访问管道时，会出现一条错误消息，指示“无法加载资源‘xxxxxx’”。  确保 JSON 中没有错误，并且有引用的资源。 状态: TypeError: 无法读取未定义的属性 'xxxxx'。可能的原因: TypeError: 无法读取未定义的属性 'xxxxxxx'。”

错误消息的源是描述管道的 JSON 文件。 如果客户使用 Git 集成并且管道 JSON 文件由于某种原因而损坏，则会发生这种情况。 你会在管道名称左侧看到一个错误（带 x 的红点），如下所示。

:::image type="content" source="media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png" alt-text="管道 JSON 错误":::

解决方案是先修复 JSON 文件，然后使用创作工具重新打开管道。


## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

* [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [数据工厂功能请求](/answers/topics/azure-data-factory.html)
* [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter 中有关数据工厂的信息](https://twitter.com/hashtag/DataFactory)
* [Azure 视频](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft 问答页](/answers/topics/azure-data-factory.html)