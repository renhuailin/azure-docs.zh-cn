---
title: 排查 Azure 数据工厂 UX 问题
description: 了解如何排查 Azure 数据工厂 UX 问题。
author: ceespino
ms.service: data-factory
ms.subservice: authoring
ms.topic: troubleshooting
ms.date: 06/01/2021
ms.author: ceespino
ms.reviewer: susabat
ms.openlocfilehash: a6d2a05e5f461a9ccf2f5e6a3cea0ba92c461b15
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123260444"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>排查 Azure 数据工厂 UX 问题

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文探讨了排查 Azure 数据工厂 UX 问题的常见方法。

## <a name="adf-ux-not-loading"></a>ADF UX 未加载

> [!NOTE]
> Azure 数据工厂 UX 官方支持 Microsoft Edge 和 Google Chrome。 使用其他 Web 浏览器可能会导致意外行为或未记录的行为。

### <a name="third-party-cookies-blocked"></a>已阻止第三方 Cookie

ADF UX 使用浏览器 Cookie 来保留用户会话并启用交互式开发和监视体验。 你的浏览器可能会阻止第三方 Cookie，因为你正在使用匿名会话或启用了广告阻止程序。 阻止第三方 Cookie 可能会导致加载门户时出现问题，例如被重定向到空白页 https://adf.azure.com/accesstoken.html ，或收到警告消息，指出已阻止第三方 Cookie。 若要解决此问题，请使用以下步骤在浏览器上启用第三方 Cookie 选项：

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>允许所有 Cookie

1. 在浏览器中访问“chrome://settings/cookies”。
1. 选择“允许所有 Cookie”选项 

    ![在 Chrome 中允许所有 Cookie](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. 刷新 ADF UX，然后重试。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>仅允许 ADF UX 使用 Cookie
如果你不想要允许所有 Cookie，则可以选择只允许 ADF UX：
1. 访问“chrome://settings/cookies”。
1. 选择“始终可以使用 Cookie 的站点”选项下的“添加”  

    ![将 ADF UX 添加到 Chrome 中允许的站点](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. 添加“adf.azure.com”站点，选中“所有 Cookie”选项，然后保存 。 

    ![允许来自 ADF UX 站点的所有 Cookie](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. 刷新 ADF UX，然后重试。

### <a name="microsoft-edge"></a>Microsoft Edge

1. 在浏览器中访问“edge://settings/content/cookies”。
1. 确保启用了“允许站点保存和读取 Cookie 数据”，并禁用了“阻止第三方 Cookie”选项  

    ![允许 Edge 中的所有 Cookie](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. 刷新 ADF UX，然后重试。

#### <a name="only-allow-adf-ux-to-use-cookies"></a>仅允许 ADF UX 使用 Cookie

如果你不想要允许所有 Cookie，则可以选择只允许 ADF UX：

1. 访问“edge://settings/content/cookies”。
1. 在“允许”部分下，选择“添加”并添加“adf.azure.com”站点  。 

    ![将 ADF UX 添加到 Edge 中允许的站点](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. 刷新 ADF UX，然后重试。

## <a name="connection-failed-on-adf-ux"></a>ADF UX 上出现连接失败

有时，在单击“测试连接”、“预览”等项后，你会在 ADF UI 上看到类似于以下屏幕截图的“连接失败”错误。这意味着 ADF 无法执行该操作，因为它无法从你的计算机连接到 ADF 服务。

![连接失败](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

若要解决此问题，你可以先在浏览器中使用 InPrivate 浏览模式尝试相同的操作。

如果仍然无法解决此问题，请从错误消息中查找服务器名称（在本例中为 dpnortheurope.svc.datafactory.azure.com），然后直接在浏览器的地址栏中键入服务器名称。 

- 如果你在浏览器中看到 404，则通常意味着客户端正常，问题出在 ADF 服务端。 使用错误消息中的“活动 ID”提交支持票证。

    ![找不到资源](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

- 如果没有看到 404，或者在浏览器中看到类似下方的错误，则通常意味着你遇到了某个客户端问题。 进一步执行故障排除步骤。

    ![客户端错误](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

若要进一步进行故障排除，请打开命令提示符并键入 `nslookup dpnortheurope.svc.datafactory.azure.com`。 正常响应应如下所示：

![命令响应 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

- 如果你看到正常的 DNS 响应，请与本地 IT 支持取得进一步联系，检查防火墙设置，确定是否阻止了与此主机名的 HTTPS 连接。 如果无法解决该问题，请使用错误消息中的“活动 ID”提交支持票证。

- 如果你看到除此之外的任何内容，则通常意味着解析 DNS 名称时 DNS 服务器出现了问题。 通常，更改 ISP（Internet 服务提供商）或 DNS（例如，改为 Google DNS 8.8.8.8）可能是一种可行的解决办法。 如果此问题仍然存在，你可以进一步尝试 `nslookup datafactory.azure.com` 和 `nslookup azure.com` 以查看 DNS 解析失败所处的级别，并将所有信息提交给本地 IT 支持或 ISP 进行故障排除。 如果他们认为问题仍然存在于 Microsoft 端，请提交支持票证，并提供错误消息中的“活动 ID”。

    ![命令响应 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>更改数据集中的链接服务类型

文件格式数据集可以与所有基于文件的连接器一起使用；例如，你可以在 Azure Blob 或 Azure Data Lake Storage Gen2 上配置 Parquet 数据集。 注意，每个连接器在活动中通过不同的应用模型，支持不同的数据存储相关设置集。 

在 ADF 创作 UI 上，若你在活动（包括复制、查找、GetMetadata、删除活动）中使用文件格式数据集，并且在该数据集中，你希望指向不同于当前类型的链接服务（例如从文件系统切换到 ADLS Gen2），则会看到以下警告消息。 若要确保这是一个干净的切换操作，在你同意的情况下，引用此数据集的管道和活动也将被修改以使用新类型，并且任何与新类型不兼容的现有数据存储设置都将被清除，因为它不再适用。

若要详细了解每个连接器支持的数据存储设置，可以转到相应的连接器文章 -> 复制活动属性以查看详细的属性列表。 请参阅 [Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure 文件存储](connector-azure-file-storage.md)、[文件系统](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、[HTTP](connector-http.md) 和 [SFTP](connector-sftp.md)。

![警告消息](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="could-not-load-resource-while-opening-pipeline"></a>打开管道时无法加载资源 

当用户使用 ADF GUI 创作工具访问管道时，错误消息显示“无法加载资源 'xxxxxx'。 请确保 JSON 中没有错误，并且所引用的资源存在。 状态: TypeError: 无法读取未定义的属性 'xxxxx'。可能的原因: TypeError: 无法读取未定义的属性 'xxxxxxx'。”

错误消息的源是描述管道的 JSON 文件。 如果客户使用 Git 集成并且管道 JSON 文件由于某种原因而损坏，则会发生这种情况。 你会在管道名称左侧看到一个错误（带 x 的红点），如下所示。

![管道 JSON 错误](media/data-factory-ux-troubleshoot-guide/pipeline-json-error.png)

解决方案是先修复 JSON 文件，然后使用创作工具重新打开管道。



## <a name="next-steps"></a>后续步骤

若要获取故障排除方面的更多帮助，请尝试参阅以下资源：

* [数据工厂博客](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [数据工厂功能请求](/answers/topics/azure-data-factory.html)
* [数据工厂 Stack Overflow 论坛](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter 中有关数据工厂的信息](https://twitter.com/hashtag/DataFactory)
* [Azure 视频](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft 问答页](/answers/topics/azure-data-factory.html)
