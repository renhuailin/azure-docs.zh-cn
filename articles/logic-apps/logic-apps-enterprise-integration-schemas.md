---
title: 添加架构以在工作流中验证 XML
description: 通过 Azure 逻辑应用和 Enterprise Integration Pack 添加架构以在工作流中验证 XML 文档。
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: 2c64deb35d89d6e1381fd3b296c7c73d82567ade
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363855"
---
# <a name="add-schemas-to-validate-xml-in-workflows-with-azure-logic-apps"></a>使用 Azure 逻辑应用添加架构以在工作流中验证 XML

若要检查文档是否使用有效的 XML 并包含预定义格式的所需数据，逻辑应用工作流可以使用 XML 架构进行“XML 验证”操作。 XML 架构使用 [XML 架构定义 (XSD)](https://www.w3.org/TR/xmlschema11-1/) 来描述以 XML 表示的业务文档。

如果不熟悉逻辑应用，请查看[什么是 Azure 逻辑应用？](logic-apps-overview.md) 有关 B2B 企业集成的详细信息，请参阅[使用 Azure 逻辑应用和 Enterprise Integration Pack 构建的 B2B 企业集成工作流](logic-apps-enterprise-integration-overview.md)。

## <a name="prerequisites"></a>先决条件

* Azure 帐户和订阅。 如果没有订阅，可以[注册免费的 Azure 帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 若要创建架构，可使用以下工具：

  * Visual Studio 2019 和 [Microsoft Azure 逻辑应用企业集成工具扩展](https://aka.ms/vsenterpriseintegrationtools)。

  * Visual Studio 2015 和[适用于 Visual Studio 2015 2.0 的 Microsoft Azure 逻辑应用企业集成工具](https://aka.ms/vsmapsandschemas)扩展。

   > [!IMPORTANT]
   > 不要将此扩展与 BizTalk Server 扩展一起安装。 同时具有这两个扩展可能会导致意外行为。 请确保只安装其中一个扩展。

   > [!NOTE]
   > 在高分辨率监视器上，可能会在 Visual Studio 中遇到[地图设计器显示问题](/visualstudio/designers/disable-dpi-awareness)。 若要解决此显示问题，请[在 DPI 无感知模式下重启 Visual Studio](/visualstudio/designers/disable-dpi-awareness#restart-visual-studio-as-a-dpi-unaware-process) 或添加 [DPIUNAWARE 注册表值](/visualstudio/designers/disable-dpi-awareness#add-a-registry-entry)。

* 一个可以在其中定义和存储项目（如贸易合作伙伴、协议、证书等）的[集成帐户资源](logic-apps-enterprise-integration-create-integration-account.md)，用于企业集成和 B2B 工作流。 此资源必须满足以下要求：

  * 与逻辑应用资源所在的同一个 Azure 订阅相关联。

  * 与你打算在其中使用 XML 验证操作的逻辑应用资源位于同一个位置或 Azure 区域。

  * 如果使用的是[“逻辑应用（消耗）”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，必须[将集成帐户链接到逻辑应用资源](logic-apps-enterprise-integration-create-integration-account.md#link-account)，然后才能在工作流中使用项目。

    若要创建和添加要在“逻辑应用（消耗）”工作流中使用的架构，尚不需要逻辑应用资源。 但是，当你准备好在工作流中使用这些架构时，逻辑应用资源需有一个用于存储这些架构的链接集成帐户。

  * 如果使用的是[“逻辑应用（标准）”资源类型](logic-apps-overview.md#resource-type-and-host-environment-differences)，则需要一个现有的逻辑应用资源，因为你不会在集成帐户中存储架构。 但可以使用 Azure 门户或 Visual Studio Code 将架构直接添加到逻辑应用资源。 然后，可以在同一逻辑应用资源中跨多个工作流使用这些架构。

    你仍需要一个集成帐户，用来存储其他项目（例如合作伙伴、协议和证书），以及使用 [AS2](logic-apps-enterprise-integration-as2.md)、[X12](logic-apps-enterprise-integration-x12.md) 和 [EDIFACT](logic-apps-enterprise-integration-edifact.md) 操作。 但是，你不需要将逻辑应用资源链接到集成帐户，因此链接功能不存在。 集成帐户仍必须满足其他要求，例如，使用相同的 Azure 订阅并与逻辑应用资源存在于同一位置。

    > [!NOTE]
    > 目前，仅“逻辑应用(消耗)”资源类型支持 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。 “逻辑应用(标准)”资源类型不包括 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 操作。

* 如果架构为 [2 MB 或更小](#smaller-schema)，可以直接从 Azure 门户将架构添加到集成帐户。 但是，如果架构大于 2 MB 但不超过[架构大小限制](logic-apps-limits-and-config.md#artifact-capacity-limits)，则你可以将架构上传到 Azure 存储帐户。 若要将该架构添加到集成帐户，可以从集成帐户链接到存储帐户。 若要完成此任务，需要提供以下各项：

    | 项 | 说明 |
    |------|-------------|
    | [Azure 存储帐户](../storage/common/storage-account-overview.md) | 在此帐户中为架构创建一个 Azure Blob 容器。 了解[如何创建存储帐户](../storage/common/storage-account-create.md)。 |
    | Blob 容器 | 在此容器中可以上传架构。 稍后在将架构添加到集成帐户时，也需要提供此容器的内容 URI。 了解如何[创建 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)。 |
    | [Azure 存储资源管理器](../vs-azure-tools-storage-manage-with-storage-explorer.md) | 借助此工具可以更轻松地管理存储帐户和 Blob 容器。 若要使用存储资源管理器，请选择一个步骤： <p>- 在 Azure 门户中选择你的存储帐户。 在存储帐户菜单中选择“存储资源管理器”。  <p>- 对于桌面版本，请[下载并安装 Azure 存储资源管理器](https://www.storageexplorer.com/)。 然后，遵循[存储资源管理器入门](../vs-azure-tools-storage-manage-with-storage-explorer.md)中的步骤将存储资源管理器连接到存储帐户。 若要了解详细信息，请参阅[快速入门：使用 Azure 存储资源管理器在对象存储中创建 Blob](../storage/blobs/quickstart-storage-explorer.md)。  |
    |||

  若要为“逻辑应用（消耗）”资源类型添加更大的架构，还可使用 [Azure 逻辑应用 REST API - 架构](/rest/api/logic/schemas/create-or-update)。 但是，对于“逻辑应用（标准）”资源类型，Azure 逻辑应用 REST API 目前不可用。

## <a name="limits"></a>限制

* 对于“逻辑应用（标准）”，架构文件大小没有限制。

* 对于“逻辑应用（消耗）”，集成帐户和项目（例如架构）存在限制。 有关详细信息，请查看 [Azure 逻辑应用的限制和配置信息](logic-apps-limits-and-config.md#integration-account-limits)。

  通常，当将集成帐户与工作流结合使用并且想要验证 XML 时，可以将架构添加或上传到该帐户。 如果要引用或导入未在集成帐户中的架构，则在使用 `xsd:redefine` 元素时可能会收到以下错误：

  `An error occurred while processing the XML schemas: ''SchemaLocation' must successfully resolve if <redefine> contains any child other than <annotation>.'.`

  要解决此错误，需要使用元素 `xsd:import` 或 `xsd:include`，而不是 `xsd:redefine`，或使用 URI。

<a name="add-schema"></a>

## <a name="add-schemas"></a>添加架构

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，使用 Azure 帐户凭据登录。

1. 在 Azure 主搜索框中输入 `integration accounts`，然后选择“集成帐户”。

1. 选择要将架构添加到的集成帐户。

1. 在集成帐户菜单中的“设置”下，选择“架构” 。

1. 在“架构”窗格工具栏中选择“添加” 。

根据架构 (.xsd) 文件的大小，遵循相应的步骤来上传[不超过 2 MB](#smaller-schema) 或[大于 2 MB 但不超过 8 MB](#larger-schema) 的架构。

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>添加不超过 2 MB 的架构

1. 在“添加架构”窗格中，输入架构的名称。 将“小文件”保持选定状态。  选择“架构”框旁边的文件夹图标。 找到并选择要上传的架构。

1. 完成后，请选择“确定”。

   完成架构上传后，该架构将显示在“架构”列表中。

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>添加 2 MB 以上的架构

若要添加更大的架构，可将架构上传到 Azure 存储帐户中的 Azure Blob 容器。 添加架构的步骤根据 Blob 容器是否具有公共读取访问权限而异。 因此，首先请执行以下步骤检查 Blob 容器是否具有公共读取访问权限：[为 Blob 容器设置公共访问级别](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>检查容器访问级别

1. 打开 Azure 存储资源管理器。 在“资源管理器”窗口中展开你的 Azure 订阅（如果尚未展开）。

1. 展开“存储帐户”>“{你的存储帐户}”>“Blob 容器”。    选择你的 Blob 容器。

1. 在 Blob 容器的快捷菜单中，选择“设置公共访问级别”。 

   * 如果 Blob 容器至少具有公共访问权限，请选择“取消”，并执行本页稍后所述的步骤：[上传到具有公共访问权限的容器](#public-access)

     ![公共访问](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * 如果 Blob 容器没有公共访问权限，请选择“取消”，并执行本页稍后所述的步骤：[上传到没有公共访问权限的容器](#public-access)

     ![没有公共访问权限](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>上传到具有公共访问权限的容器

1. 将架构上传到存储帐户。 在右侧窗口中，选择“上传”。

1. 完成上传后，选择上传的架构。 在工具栏中，选择“复制 URL”以复制架构的 URL。

1. 返回 Azure 门户，其中已打开“添加架构”窗格。 输入程序集的名称。 选择“大文件(大于 2 MB)”。

   此时会显示“内容 URI”框而不是“架构”框。

1. 在“内容 URI”框中，粘贴架构的 URL。 完成添加架构。

完成架构上传后，该架构将显示在“架构”列表中。 在集成帐户“概述”页的“项目”下，会显示上传的架构 。

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>上传到没有公共访问权限的容器

1. 将架构上传到存储帐户。 在右侧窗口中，选择“上传”。

1. 上传完成后，为架构生成共享访问签名 (SAS)。 在架构的快捷菜单中，选择“获取共享访问签名”。

1. 在“共享访问签名”窗格中，选择“生成容器级共享访问签名 URI” > “创建”。    生成 SAS URL 后，选择“URL”框旁边的“复制” 。

1. 返回 Azure 门户，其中已打开“添加架构”窗格。 选择“大文件”。

   此时会显示“内容 URI”框而不是“架构”框。

1. 在“内容 URI”框中，粘贴前面生成的 SAS URI。  完成添加架构。

完成架构上传后，该架构将显示在“架构”列表中。 在集成帐户“概述”页的“项目”下，会显示上传的架构 。

### <a name="standard"></a>[标准](#tab/standard)

#### <a name="azure-portal"></a>Azure 门户

1. 在逻辑应用资源菜单中的“设置”下，选择“架构” 。

1. 在“架构”窗格工具栏中选择“添加” 。

1. 在“添加架构”窗格中，输入架构的唯一名称。

1. 选择“架构”框旁边的文件夹图标。 选择要上传的架构。

1. 完成后，请选择“确定”。

   完成架构文件上传后，该架构将显示在“架构”列表中。 在集成帐户“概述”页的“项目”下，也会显示上传的架构 。

#### <a name="visual-studio-code"></a>Visual Studio Code

1. 在逻辑应用项目的结构中打开“项目”文件夹，然后打开“架构”文件夹 。

1. 在“架构”文件夹中添加你的架构。

---

<a name="edit-schema"></a>

## <a name="edit-a-schema"></a>编辑架构

若要更新现有的架构，必须上传一个新的架构文件，其中包含所要做出的更改。 但是，可以先下载现有的架构进行编辑。

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，打开你的集成帐户（如果尚未打开）。

1. 在集成帐户菜单中的“设置”下，选择“架构” 。

1. “架构”窗格打开后，选择你的架构。 若要先下载并编辑架构，请在“架构”窗格工具栏中选择“下载”，然后保存架构 。

1. 准备好上传已更新的架构时，请在“架构”窗格中选择要更新的架构。 在“架构”窗格工具栏中选择“更新” 。

1. 找到并选择要上传的已更新架构。

1. 完成后，请选择“确定”。

   完成架构文件的上传后，更新的架构将显示在“架构”列表中。

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)中打开你的逻辑应用资源（如果尚未打开）。

1. 在逻辑应用资源菜单中的“设置”下，选择“架构” 。

1. “架构”窗格打开后，选择你的架构。 若要先下载并编辑架构，请在“架构”窗格工具栏中选择“下载”，然后保存架构 。

1. 在“架构”窗格工具栏中选择“添加” 。

1. 在“添加架构”窗格中，输入架构的唯一名称。

1. 选择“架构”框旁边的文件夹图标。 选择要上传的架构。

1. 完成后，请选择“确定”。

   完成架构文件的上传后，更新的架构将显示在“架构”列表中。

---

<a name="delete-schema"></a>

## <a name="delete-a-schema"></a>删除架构

### <a name="consumption"></a>[消耗](#tab/consumption)

1. 在 [Azure 门户](https://portal.azure.com)中，打开你的集成帐户（如果尚未打开）。

1. 在集成帐户菜单中的“设置”下，选择“架构” 。

1. “架构”窗格打开后，请选择你的架构，然后选择“删除” 。

1. 若要确认删除该架构，请选择“是”。

### <a name="standard"></a>[标准](#tab/standard)

1. 在 [Azure 门户](https://portal.azure.com)中打开你的逻辑应用资源（如果尚未打开）。

1. 在逻辑应用资源菜单中的“设置”下，选择“架构” 。

1. “架构”窗格打开后，请选择你的架构，然后选择“删除” 。

1. 若要确认删除该架构，请选择“是”。

---

## <a name="next-steps"></a>后续步骤

* [在 Azure 逻辑应用中验证工作流的 XML](logic-apps-enterprise-integration-xml-validation.md)
* [在 Azure 逻辑应用中转换工作流的 XML](logic-apps-enterprise-integration-transform.md)
