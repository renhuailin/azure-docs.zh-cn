---
title: 对加密的 Azure Blob 存储内容进行搜索
titleSuffix: Azure Cognitive Search
description: 了解如何使用 Azure 认知搜索为 Azure Blob 存储的已加密文档中的文本编制索引以及从这些文档中提取文本。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.openlocfilehash: c78d8f3bc4a7bfc7b73d71a97e29c369926448c5
ms.sourcegitcommit: a2540262e05ffd4a4b059df0976940d60fabd125
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2021
ms.locfileid: "113139406"
---
# <a name="how-to-index-encrypted-blobs-using-blob-indexers-and-skillsets-in-azure-cognitive-search"></a>如何在 Azure 认知搜索中使用 Blob 索引器和技能组为加密的 Blob 编制索引

本文介绍了如何使用 [Azure 认知搜索](search-what-is-azure-search.md)为之前已使用 [Azure Key Vault](../key-vault/general/overview.md) 在 [Azure Blob 存储](../storage/blobs/storage-blobs-introduction.md)中加密的文档编制索引。 通常，索引器无法从加密的文件中提取内容，因为它无权访问加密密钥。 但是，通过利用后跟 [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) 的 [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/main/Utils/DecryptBlobFile) 自定义技能，你可以提供对该密钥的受控访问，以对文件进行解密，并从这些文件中提取内容。 这样就可以在不损害已存储文档的加密状态的情况下解锁为这些文档编制索引的功能。

本指南从以前加密的整个文档（非结构化文本，例如 Azure Blob 存储中的 PDF、HTML、DOCX 和 PPTX）着手，使用 Postman 和搜索 REST API 执行以下任务：

> [!div class="checklist"]
> * 定义一个管道，用于解密文档并从中提取文本。
> * 定义用于存储输出的索引。
> * 执行该管道以创建并加载索引。
> * 使用全文搜索和丰富的查询语法浏览结果。

如果你没有 Azure 订阅，请在开始之前建立一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

此示例假设你已将文件上传到 Azure Blob 存储，并在此过程中对它们进行了加密。 如果你在初次上传和加密文件时需要帮助，请参阅[此教程](../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md)来了解如何那样做。

+ [Azure 存储](https://azure.microsoft.com/services/storage/)
+ 与 Azure 认知搜索位于同一订阅中的 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)。 密钥保管库必须启用“软删除”和“清除保护”。 
+ [可计费层](search-sku-tier.md#tier-descriptions)（任何区域中的“基本”层或更高层）上的 [Azure 认知搜索](search-create-service-portal.md)
+ [Azure Function](https://azure.microsoft.com/services/functions/)
+ [Postman 桌面应用](https://www.getpostman.com/)

## <a name="1---create-services-and-collect-credentials"></a>1 - 创建服务并收集凭据

### <a name="set-up-the-custom-skill"></a>设置自定义技能

此示例使用 [Azure 搜索强大技能](https://github.com/Azure-Samples/azure-search-power-skills) GitHub 存储库中的示例 [DecryptBlobFile](https://github.com/Azure-Samples/azure-search-power-skills/blob/main/Utils/DecryptBlobFile) 项目。 在本部分，你将技能部署到 Azure 函数，以便在技能组中使用。 一个内置的部署脚本会创建一个名称以 **psdbf-function-app-** 开头的 Azure 函数资源，并加载该技能。 系统会提示你提供订阅和资源组。 请确保选择你的 Azure Key Vault 实例所在的订阅。

在操作方面，DecryptBlobFile 技能采用每个 blob 的 URL 和 SAS 令牌作为输入，并使用 Azure 认知搜索需要的文件引用约定输出下载的解密文件。 请记住，DecryptBlobFile 需要使用加密密钥来执行解密操作。 在设置过程中，你还将创建一个访问策略，用于授予对 Azure Key Vault 中的加密密钥的 DecryptBlobFile 函数访问权限。

1. 单击 [DecryptBlobFile 登陆页](https://github.com/Azure-Samples/azure-search-power-skills/blob/main/Utils/DecryptBlobFile#deployment)上的“部署到 Azure”按钮，这将在 Azure 门户中打开所提供的资源管理器模板。

1. 选择 **你的 Azure Key Vault 实例所在的订阅**（如果选择其他订阅，本指南将无法使用），并选择一个现有资源组或创建一个新资源组（如果创建新的资源组，则还需要选择要部署到的区域）。

1. 选择“查看 + 创建”，确保同意条款，然后选择“创建”来部署 Azure 函数。

    ![门户中的 ARM 模板](media/indexing-encrypted-blob-files/arm-template.jpg "门户中的 ARM 模板")

1. 等待部署完成。

1. 在门户中导航到你的 Azure Key Vault 实例。 在 Azure Key Vault 中[创建访问策略](../key-vault/general/assign-access-policy-portal.md)，以授予对自定义技能的密钥访问权限。
 
    1. 在“设置”下，选择“访问策略”，然后选择“添加访问策略”  
     
       ![Keyvault 的“添加访问策略”](media/indexing-encrypted-blob-files/keyvault-access-policies.jpg "Keyvault 访问策略")

    1. 在“从模板配置”下，选择“Azure Data Lake Storage”或“Azure 存储”。

    1. 对于主体，请选择你部署的 Azure 函数实例。 你可以使用在步骤 2 中用来创建它的资源前缀（默认前缀值为 **psdbf-function-app**）来搜索它。

    1. 对于经授权的应用程序选项，不要选择任何内容。
     
        ![Keyvault 的“添加访问策略”模板](media/indexing-encrypted-blob-files/keyvault-add-access-policy.jpg "Keyvault 访问策略模板")

    1. 请务必单击访问策略页上的“保存”，然后再离开该页以添加访问策略。
     
         ![Keyvault 的保存访问策略操作](media/indexing-encrypted-blob-files/keyvault-save-access-policy.jpg "保存 Keyvault 访问策略")

1. 在门户中导航到“psdbf-function-app”函数，记下以下属性，因为本指南中稍后将需要它们：

    1. 函数 URL，可在函数主页面上的“概要”下找到。
    
        ![函数 URL](media/indexing-encrypted-blob-files/function-uri.jpg "到哪里查找 Azure 函数 URL")

    1. 主机密钥代码，可以通过以下方式找到：导航到“应用密钥”，单击以显示 **默认** 密钥，然后复制值。
     
        ![函数主机密钥代码](media/indexing-encrypted-blob-files/function-host-key.jpg "到哪里查找 Azure 函数主机密钥代码")

### <a name="cognitive-services"></a>认知服务

AI 扩充和技能组执行由认知服务（包括用于自然语言和图像处理的文本分析与计算机视觉）提供支持。 如果你的目标是完成实际原型或项目，则此时应预配认知服务（在 Azure 认知搜索所在的同一区域中），以便可将认知服务附加到索引操作。

但是，对于本练习，可以跳过资源预配，因为 Azure 认知搜索在幕后可以连接到认知服务，并为每个索引器运行提供 20 个免费事务。 处理 20 个文档后，索引器会失败，除非将一个认知服务密钥附加到技能组。 对于大型项目，请计划在即用即付 S0 层预配认知服务。 有关详细信息，请参阅[附加认知服务](cognitive-search-attach-cognitive-services.md)。 请注意，若要在文档数超过 20 个的情况下运行技能组，即使没有任何选定的认知技能连接到认知服务，也需要使用认知服务密钥（例如，在使用提供的技能组的情况下，即使未向其中添加任何技能，也需要这样做）。

### <a name="azure-cognitive-search"></a>Azure 认知搜索

最后一个组件是可以[在门户中创建](search-create-service-portal.md)的 Azure 认知搜索。 可使用免费层完成本指南。 

与 Azure 函数一样，请花点时间收集管理密钥。 此外，在开始构建请求时，需要提供终结点和管理 API 密钥用于对每个请求进行身份验证。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>获取 Azure 认知搜索的管理 API 密钥和 URL

1. [登录到 Azure 门户](https://portal.azure.com/)，在搜索服务的“概述”页中获取搜索服务的名称。  可以通过查看终结点 URL 来确认服务名称。 如果终结点 URL 为 `https://mydemo.search.windows.net`，则服务名称为 `mydemo`。

2. 在“设置” > “密钥”中，获取有关该服务的完全权限的管理员密钥   。 有两个可交换的管理员密钥，为保证业务连续性而提供，以防需要滚动一个密钥。 可以在请求中使用主要或辅助密钥来添加、修改和删除对象。

   ![获取服务名称以及管理密钥和查询密钥](media/search-get-started-javascript/service-name-and-keys.png)

所有请求要求在发送到服务的每个请求的标头中指定 API 密钥。 具有有效的密钥可以在发送请求的应用程序与处理请求的服务之间建立信任关系，这种信任关系以每个请求为基础。

## <a name="2---set-up-postman"></a>2 - 设置 Postman

安装并设置 Postman。

### <a name="download-and-install-postman"></a>下载并安装 Postman

1. 下载 [Postman 集合源代码](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)。
1. 选择“文件” > “导入”将源代码导入 Postman。
1. 选择“集合”选项卡，然后选择“...”（省略号）按钮。
1. 选择“编辑”。 
   
   ![显示导航栏的 Postman 应用](media/indexing-encrypted-blob-files/postman-edit-menu.jpg "转到 Postman 中的“编辑”菜单")
1. 在“编辑”对话框中，选择“变量”选项卡。 

在“变量”选项卡上，可以添加 Postman 每次在遇到双大括号中的值时要替换成的值。 例如，Postman 会将符号 `{{admin-key}}` 替换成为 `admin-key` 设置的当前值。 Postman 将在 URL、标头和请求正文等内容中进行这种替换。 

若要获取 `admin-key` 的值，请使用你前面记下的 Azure 认知搜索管理 API 密钥。 将 `search-service-name` 设置为你使用的 Azure 认知搜索服务的名称。 使用你的存储帐户的“访问密钥”选项卡上的值设置 `storage-connection-string`，并将 `storage-container-name` 设置为该存储帐户上存储加密文件的 blob 容器的名称。 将 `function-uri` 设置为你之前记下的 Azure 函数 URL，将 `function-code` 设置为你之前记下的 Azure 函数主机密钥代码。 其他值可保留默认设置。

![Postman 应用变量选项卡](media/indexing-encrypted-blob-files/postman-variables-window.jpg "Postman 的变量窗口")

| 变量    | 从何处获取 |
|-------------|-----------------|
| `admin-key` | 在 Azure 认知搜索服务的“密钥”页上。  |
| `search-service-name` | Azure 认知搜索服务的名称。 该 URL 为 `https://{{search-service-name}}.search.windows.net`。 |
| `storage-connection-string` | 在存储帐户中的“访问密钥”选项卡上，选择“密钥 1” > “连接字符串”。 |
| `storage-container-name` | 包含要编制索引的加密文件的 blob 容器的名称。 |
| `function-uri` |  在主页面上“概要”下的 Azure 函数中。 |
| `function-code` | 在 Azure 函数中，获取方法是：导航到“应用密钥”，单击以显示 **默认** 密钥，然后复制值。 |
| `api-version` | 保留为“2020-06-30”。 |
| `datasource-name` | 保留为“encrypted-blobs-ds”。 |
| `index-name` | 保留为“encrypted-blobs-idx”。 |
| `skillset-name` | 保留为“encrypted-blobs-ss”。 |
| `indexer-name` | 保留为“encrypted-blobs-ixr”。 |

### <a name="review-the-request-collection-in-postman"></a>查看 Postman 中的请求集合

运行本指南时，必须发出四个 HTTP 请求：

- **创建索引的 PUT 请求**：此索引保存 Azure 认知搜索使用和返回的数据。
- **创建数据源的 POST 请求**：此数据源会将你的 Azure 认知搜索服务连接到你的存储帐户，进而连接到加密的 blob 文件。 
- **创建技能集的 PUT 请求**：技能组为将解密 blob 文件数据的 Azure 函数指定自定义技能定义，并指定 [DocumentExtractionSkill](cognitive-search-skill-document-extraction.md) 以在解密每个文档后从其中提取文本。
- **创建索引器的 PUT 请求**：运行索引器可读取数据、应用技能集并存储结果。 必须在最后运行此请求。

[源代码](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/index-encrypted-blobs/Index%20encrypted%20Blob%20files.postman_collection.json)包含一个 Postman 集合，其中有这四个请求以及一些有用的后续请求。 若要发出这些请求，请在 Postman 中选择与请求对应的选项卡，并针对每个请求选择“发送”。

## <a name="3---monitor-indexing"></a>3 - 监视索引编制

提交“创建索引器”请求后，索引编制和扩充立即开始。 编制索引可能需要一段时间，具体取决于存储帐户中的文档数量。 若要确定索引器是否仍在运行，请使用作为 Postman 集合的一部分提供的“获取索引器状态”请求，并查看响应以了解索引器是否正在运行，或者查看错误和警告信息。  

如果使用的是免费层，应会收到以下消息：`"Could not extract content or metadata from your document. Truncated extracted text to '32768' characters"`。 出现此消息的原因是，免费层上的 Blob 索引编制存在 [32K 字符提取限制](search-limits-quotas-capacity.md#indexer-limits)。 在更高的层上，此数据集不会出现此消息。 

## <a name="4---search"></a>4 - 搜索

索引器执行完成后，可以运行一些查询来验证是否已将数据成功解密并编制索引。 在门户中导航到你的 Azure 认知搜索服务，并使用[搜索资源管理器](search-explorer.md)对已编制索引的数据运行查询。

## <a name="next-steps"></a>后续步骤

成功地为加密的文件编制索引后，即可[通过添加更多认知技能来循环访问此管道](cognitive-search-defining-skillset.md)。 这将允许你扩充数据并获得对数据的更多见解。

如果你处理双重加密的数据，则可能需要研究 Azure 认知搜索中提供的索引加密功能。 尽管索引器需要使用解密的数据进行索引编制，但是索引一旦存在，就可以使用客户管理的密钥对其进行加密。 这将确保数据在静止时始终加密。 有关详细信息，请参阅[在 Azure 认知搜索中为数据加密配置客户管理的密钥](search-security-manage-encryption-keys.md)。